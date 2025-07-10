📌 What is Load Shedding?
Load Shedding is the practice of intentionally dropping requests when a system is overloaded to maintain overall system performance and prevent a total crash.
It's like a bouncer at a club: when it's too crowded, new guests are turned away to keep things safe inside.
________________________________________

⚠️ Why Load Shedding is Important?
Without load shedding, an overwhelmed system may:
•	Crash completely ⚠️
•	Serve all users slowly ⏳
•	Fail to prioritize critical users or services 🚫
________________________________________

🧠 Real-World Analogy
🏥 Hospital Emergency Room
•	During a disaster, only patients with critical needs are treated first (triage).
•	Others are asked to wait or redirected elsewhere.
________________________________________

🟢 Techniques to Implement Load Shedding
Strategy	Description
✅ Request Dropping	Simply drop low-priority or excess requests when the load is too high.
✅ Prioritization	Serve high-priority users/services first; shed less important ones.
✅ Rate Limiting	Limit the number of requests per user/IP to avoid overwhelming the system.
✅ Timeouts	Set timeouts to quickly abandon long-running or stuck requests.
✅ Circuit Breakers	Prevent cascading failures by halting requests to a failing service.
✅ Backpressure	Push back to upstream systems to slow down request rates.
________________________________________

🔴 When to Shed Load?
Condition	Action
CPU usage > 90%	Drop non-critical requests
Memory usage nearing capacity	Avoid requests that increase load
Response time degrading rapidly	Limit incoming traffic
Queue sizes are overflowing	Drop or reject excess requests
________________________________________

🛠️ Examples of Load Shedding in Practice
•	Netflix: Prioritizes video stream requests over recommendations when overloaded.
•	Cloudflare: Drops excess malicious/bot traffic during DDoS attacks.
•	Amazon: Favors order placement requests over non-critical services like reviews.
________________________________________

📦 Load Shedding vs Rate Limiting
Feature	Load Shedding	Rate Limiting
Purpose	Drop traffic under stress	Control traffic proactively
Triggered by	System load	Defined user/IP request limits
Dynamic?	✅ Yes (based on system state)	❌ No (typically fixed rules)
________________________________________

📌 When to Use Load Shedding?
✅ Systems under high, unpredictable load
✅ To maintain critical services during overload
✅ For graceful degradation instead of full failure
✅ In real-time apps (e.g., video calls, chat)
________________________________________

🧠 Final Takeaways
✔ Load shedding prevents total system failure
✔ Helps maintain quality of service for critical users
✔ Best used with rate limiting, timeouts, and circuit breakers
✔ Especially valuable in microservices and cloud-native architectures