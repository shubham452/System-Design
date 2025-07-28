Here are detailed notes on the provided sources regarding the system design of applications like Uber:

The video covers the **system design of Uber**, which also applies to similar cab services like Ola, Lyft, and Careem. The first step in system design is to understand the **customer user journeys (CUJs)**, which describe how customers interact with the product. These CUJs directly translate into **functional requirements** for the application.

### Customer User Journeys (CUJs)

The application caters to two main types of users: **Riders (normal users)** and **Drivers**.

*   **Driver CUJs:**
    *   Create their profile.
    *   Show their availability on the application.
    *   **Accept or reject a ride notification** from a nearby user. If rejected, they return to the availability state.
    *   Navigate to the pickup location if the ride is accepted.
    *   Start the ride after the user provides authentication (e.g., OTP).
    *   Navigate to the drop location.
    *   End the ride and accept payment.
    *   Optionally, rate the rider.
    *   See past trips.
    *   See current trip status.

*   **Rider CUJs:**
    *   Create their profile.
    *   View **nearby available cabs** based on current location.
    *   Add start and end locations for the cab service.
    *   View **estimated time of arrival (ETA)** and **approximate price** for the trip.
    *   **Book a cab**.
    *   Make payment.
    *   Rate the driver.
    *   See past trips.
    *   See current trip status.

### Non-Functional Requirements (NFRs)

These requirements affect the user experience and system performance, not directly the user journey itself.

*   **Minimal Latency:** While not requiring super low latency like high-frequency trading firms, some CUJs, such as finding a cab, need low latency. Other services like checking past trips or payment reflection can tolerate some delay.
*   **Highly Available:** Services like Uber must be available 24/7 in all operational cities, making high availability crucial.
*   **Durable:** Data must persist forever, including trip records, location data, and payment history. Data loss is unacceptable.
*   **Highly Scalable:** The system must scale to handle **peak hours** (e.g., 9 AM, 5-7 PM) and easily expand to new cities globally.
*   **Strongly Consistent (or Mutually Exclusive Consistency):** The **CAP theorem** states that a service cannot be both highly available and strongly consistent simultaneously. For Uber, certain microservices require strong consistency, while others can tolerate eventual consistency.
    *   **Strong Consistency is Required for:** Cab finder service (to ensure that once a cab is booked, other drivers don't see the request anymore), and trip status (to show the trip has started immediately).
    *   **Eventual Consistency is Acceptable for:** Trip service or payment service (checking past payments or payment reflection can have a slight delay). The features that require high availability and strong consistency are mutually exclusive and do not overlap.

### Scale of Uber

The discussion is based on data published by Uber's blog:

*   **100 Million Monthly Active Users (MAU)**.
*   **10 Million Daily Rides**.
*   **1 Million Daily Active Drivers** (assuming one driver typically does 5-10 rides a day).
*   **Operational Load (QPS):** 10 million rides per day / 86,400 seconds in a day (approximated to 100,000 seconds for ease) results in **~100 rides requested/booked per second (100 QPS)**. This high QPS indicates a significant operational load.

### Core Technical Concepts and Services

#### 1. Communication Between Clients and Servers

*   Both drivers and riders communicate with Uber servers.
*   **HTTP (Synchronous):** A request-response model where a client sends a request and waits for a response. This is inefficient for real-time location pings to all drivers.
*   **WebSockets (Asynchronous/P2P):** Create an **open, persistent connection** between two peers (client and server), allowing asynchronous bidirectional message exchange.
    *   Drivers can continuously send location pings to the server (e.g., every 4-10 seconds).
    *   When a driver becomes available, they send a ping to the server, establishing a WebSocket connection with a unique **connection ID** linked to their **user ID**.
    *   WebSockets are used for communication between Riders and the server, and Drivers and the server, enabling continuous message transmission.

#### 2. Finding Cabs Nearby (Geospatial Indexing)

Finding drivers in a user's vicinity efficiently for 100 QPS is challenging with 1 million active drivers.

*   **Inefficient Methods:**
    *   Iterating through all 1 million driver locations.
    *   Creating a primary index on a single dimension (latitude or longitude) is still inefficient if many drivers are in a given range.
    *   Creating two databases with primary indexes on latitude and longitude separately, then finding the intersection, is also very inefficient for 100 QPS.
*   **Efficient Methods (Converting 2D to 1D Data):**
    *   **Geohash:** Divides the world map into chunks, each with a unique geohash.
    *   **Quad Trees:** Another method for spatial indexing.
    *   **Google S2 Library (Uber's choice):** Divides the entire spherical globe (3D data) into smaller **chunks** (effectively converting to 1D data).
        *   When a user pings their location, the Map Service uses the Google S2 Library to determine which **chunk ID** the user belongs to.
        *   It then creates a radius around the user's location (e.g., 2-5 km) to identify a **set of nearby chunks** where drivers should be searched.
        *   This significantly **reduces the search space** for finding drivers.

#### 3. Microservices Architecture

Uber's system is built upon several specialized microservices.

*   **Map Service:**
    *   Converts latitude and longitude pairs to **chunk IDs** using Google S2 Library.
    *   Identifies nearby chunks based on a user's location and desired radius.
    *   Performs **ETA computation** (estimated time of arrival) and **route calculations** between points (driver to pickup, pickup to drop). It uses actual road distances, not just aerial/Euclidean distances.
    *   Collaborates with the Payment Service to compute the **approximate price** based on distance and time.
    *   Handles updating a driver's chunk ID as they move.

*   **Location Service:**
    *   **Source of truth** for all location data (user and driver).
    *   Stores **latest location data** (driver ID, lat/long) in a **Redis cluster** for fast retrieval. This table is small (~25 MB for 1 million drivers).
    *   Stores **historical location data** in a **NoSQL/Cassandra cluster**. This data is crucial for:
        *   Machine learning and analytics.
        *   Retracing trips to verify optimal routes or address user complaints (e.g., driver taking a bad route, fraudulent charges).
        *   Safety and fraud detection.

*   **Cab Finder Service (Dispatch Optimizer/Dispatch Service/Supply Demand Service):**
    *   **Matches demand (user requests) with supply (available drivers)** in a particular radius/chunks.
    *   It is a **distributed service** with multiple servers, each responsible for a set of chunk IDs.
    *   Leverages **consistent hashing** to assign chunk ID responsibilities to servers. This allows for **easy scaling** by adding/removing servers as traffic in chunks increases/decreases (e.g., breaking a high-traffic chunk into smaller ones or merging chunks).
    *   Uses **Gossip Protocol** to notify other servers when a server's chunk responsibilities change.
    *   Process: When a user requests a cab, the Cab Finder Service gets the user's chunk ID from the Map Service. It then identifies which server is responsible for that chunk and uses the Gossip Protocol to find which servers are responsible for nearby chunks. It pings these servers to get lists of drivers, then sends ride requests via the WebSocket manager to those drivers.

*   **Profile Service:**
    *   Handles **CRUD (Create, Read, Update, Delete) APIs** for user and driver profiles.

*   **Trip Service:**
    *   Manages the **entire trip lifecycle**: ride accepted, started, ended.
    *   Stores **inactive/completed trips** in a **Cassandra cluster (NoSQL)** because this data continuously grows over time.
    *   Stores **active/live trips** in a **SQL cluster** for faster retrieval, as these are frequently accessed.
    *   An **archiver (Cron job)** periodically (e.g., every 6 hours) transfers inactive trips from the SQL database to the Cassandra cluster.
    *   Stores detailed trip information, including **location pings from both the user and the driver** throughout the trip. This is vital for verifying trip details, detecting fraud, and handling complaints.
    *   Notifies the Payment Service when a trip ends.

*   **Payment Service:**
    *   Calculates the **price of a ride** once the trip ends, based on distance covered, time taken, and location data from the Trip Service.
    *   Integrates with a **payment gateway** (e.g., Stripe, Razorpay).

#### 4. Load Balancing and Routing

*   A **load balancing layer** is placed in front of the WebSocket servers to distribute traffic from 100 million MAU and 100 QPS across multiple servers.
*   A **WebSocket Manager** tracks connection IDs to user IDs and which WebSocket server handles each connection.
*   A **Routing Service** behind the WebSocket servers distributes requests to the appropriate microservices (e.g., location service for pings, cab finder service for booking).

#### 5. Additional Concepts

*   **Preferred Access Points:** Identified by aggregating **historical location data** (stored in Cassandra) using **machine learning**. This data is processed via Kafka queues and services like Spark Storm for analytics.
*   **Intelligent Driver Matching:** The system can consider drivers who are **about to end their current trip near a user's location** as potential candidates for new rides. This is done using machine learning on Trip Service data.
*   **Fraud Detection:** Aggregated **payment data** and **location pings** (transmitted via Kafka queues) are used by machine learning fraud services to detect fraudulent activities, such as drivers booking rides with their own phones to gain incentives.
*   **Authentication and Authorization:**
    *   To ensure secure interactions, an **additional context** like a **user token** or **API key** is passed with requests to services.
    *   This token/key authenticates the user and ensures they only access or modify data related to their own user ID. User tokens are generated each time the app is opened, while API keys persist longer.
*   **Logging, Monitoring, and Alerting:**
    *   All services generate various **logs** (error, debugging).
    *   These logs are consumed by **Kafka queues** and then processed by an **ELK stack** (Elasticsearch, Logstash, Kibana).
    *   ELK is used for **log analysis, creating visualizations**, and setting up **monitoring and alerting** for system availability and performance.

This comprehensive design covers the functional and non-functional aspects of a large-scale application like Uber, from user interactions to backend services and data management.
