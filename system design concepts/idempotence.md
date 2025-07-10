📌 What is Idempotence?
Idempotence is a property of an operation where performing it once or multiple times has the same effect.
Whether a request is sent once or multiple times, the outcome remains unchanged.
________________________________________

🔁 Why is Idempotence Important?
In distributed systems or APIs, network issues or retries can cause duplicate requests.
Idempotent operations prevent data corruption or unintended side effects.
________________________________________

🧠 Real-World Analogy
💡 Light Switch (OFF position)
•	Turning off the light once = OFF
•	Turning it off multiple times = Still OFF
•	No extra effect = Idempotent
________________________________________

✅ Idempotent vs ❌ Non-Idempotent
Operation	Idempotent?	Why?
GET /user/123	✅ Yes	Returns the same user data every time
DELETE /post/45	✅ Yes	Deleting the same post multiple times has no effect
PUT /user/123 {name: "John"}	✅ Yes	Replaces the data with the same result
POST /orders	❌ No	Creates a new order every time it's called
PATCH /profile	❌ Maybe	Depends on the logic; could cause partial updates
________________________________________

🛡️ How to Achieve Idempotence?
Strategy	Description
✅ Use PUT instead of POST	PUT replaces entire resource — safe for retries
✅ Track request IDs	Save client-generated IDs and ignore duplicates
✅ Stateless design	Design APIs so repeated calls don't depend on internal state
✅ Database constraints	Use unique keys (like ORDER_ID) to prevent duplicate inserts
________________________________________

🔄 When Do You Need Idempotence?
✅ During API retries due to failures or timeouts
✅ In payment processing — avoid charging twice
✅ In microservices for safe communication
✅ When building robust client-side retry logic
________________________________________

🛠️ Examples in Practice
•	Stripe API → Requires an idempotency_key when creating a charge to avoid duplicates
•	AWS Lambda → Automatically retries failed executions — you must ensure idempotency
•	Payment Gateways → Must not double-charge on retry
________________________________________

📌 Final Thoughts
✔ Idempotence increases reliability and safety
✔ Crucial for fault-tolerant systems
✔ Design operations to be repeat-safe whenever possible
✔ Helps prevent duplicate side effects like multiple charges, emails, or orders