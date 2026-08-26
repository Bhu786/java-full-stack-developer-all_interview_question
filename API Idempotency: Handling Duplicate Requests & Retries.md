Ye issue **mainly API / distributed-system level ka hai**, lekin **DB bhi important role** play karta hai.

Simple breakdown:

| Part           | Role                                                           |
| -------------- | -------------------------------------------------------------- |
| **Client**     | Timeout ke baad request retry karta hai                        |
| **API/Server** | Same request ko dobara process kar deta hai → duplicate create |
| **Database**   | Duplicate record store ho jata hai agar constraint nahi hai    |
| **Network**    | Response lost/timeout hone ka reason ho sakta hai              |

### Actual problem flow

```text
Client
  ↓
POST /orders
  ↓
API Server
  ↓
DB → Order CREATED ✅
  ↓
Response ❌ timeout/lost
  ↓
Client thinks "failed"
  ↓
Retry same operation
  ↓
API Server
  ↓
DB → Order CREATED AGAIN ❌
```

### Isliye interview mein ise kaise classify karoge?

**Primary issue:**
👉 **API reliability + retry/idempotency problem**

**Supporting protection:**
👉 **Database unique constraint**

So agar interviewer bole:

> "API retries are causing duplicate data. How will you fix it?"

Tumhara first answer hona chahiye:

**"I will make the API idempotent using an idempotency key."**

DB mein `UNIQUE` constraint **second line of defense** hai, primary solution nahi.

### Ek line mein yaad rakho

**Retry problem → API level → Idempotency Key → DB Unique Constraint as safety net.**
=====================================
=========================================
================================================
Haan, is image ka main concept **API retries ki wajah se duplicate data** hai. Isko interview perspective se simple way mein samjho.

## 1. Problem kya hai?

Suppose client ne request bheji:

```text
POST /orders
```

Server ko request mili aur server ne **order create kar diya**.

Lekin response client tak nahi pahucha — maan lo **network timeout** ho gaya.

Client ko laga:

> "Request fail ho gayi."

Toh client same request **dobara bhej deta hai**.

```text
Client                  Server

POST /orders  -------->  Order create
             <--------   Response lost/timeout

POST /orders  -------->  Order create AGAIN
```

Result:

```text
Order #101
Order #102
```

**Same logical order ke 2 records ban gaye.**

---

# 2. Root cause

Important point:

> **Request fail nahi hui thi; response fail/lost hua tha.**

Server ne pehli request successfully process kar di thi.

Client ko iska pata nahi chala, isliye retry kiya.

Ye distributed systems mein common problem hai.

---

# 3. Solution — Idempotency Key

Client har logical operation ke liye ek **unique Idempotency-Key** bhejega.

Example:

```http
POST /orders
Idempotency-Key: abc-123
```

First request:

```text
Client
   |
   | POST /orders
   | Idempotency-Key: abc-123
   ↓
Server
   |
   | Order create
   | Save key + result
   ↓
Order created
```

Ab response timeout ho gaya.

Client retry karega:

```http
POST /orders
Idempotency-Key: abc-123
```

Server dekhega:

```text
abc-123 already exists
```

Toh **dobara order create nahi karega**.

Instead:

```text
Already processed
        ↓
Return previous/cached result
```

So:

```text
1 logical request
        ↓
1 order
```

---

# 4. Server ko kya store karna hai?

Server kuch aisa mapping maintain kar sakta hai:

```text
Idempotency-Key       Result
------------------------------------------------
abc-123               Order #101
xyz-456               Order #102
pqr-789               Order #103
```

Request aayi:

```text
abc-123
```

Server checks:

```text
Does abc-123 exist?
```

### No

```text
Create order
Save abc-123 → Order #101
Return Order #101
```

### Yes

```text
Don't create again
Return Order #101
```

---

# 5. Image ka code basically ye kar raha hai

```text
if (store.exists(key)) {
    return store.getCachedResponse(key);
}

Order o = createOrder(req);

store.save(key, o);

return o;
```

Line by line:

### Step 1

```text
store.exists(key)
```

Check karo ki ye request pehle process hui hai ya nahi.

### Step 2 — already processed

```text
return store.getCachedResponse(key);
```

Agar key already hai → previous result return karo.

**New order mat banao.**

### Step 3 — first request

```text
Order o = createOrder(req);
```

Order create karo.

### Step 4

```text
store.save(key, o);
```

Idempotency key ke saath result save karo.

---

# 6. DB Unique Constraint bhi important hai

Image mein ek aur important point hai:

> **DB-level unique constraint on natural business key**

Suppose business rule ke according:

```text
order_id
```

ya

```text
payment_transaction_id
```

unique hona chahiye.

Database mein:

```sql
UNIQUE(payment_transaction_id)
```

rakh sakte ho.

Ye **second safety net** hai.

Even agar application-level check mein race condition aa jaye:

```text
Request A → check → doesn't exist
Request B → check → doesn't exist
```

Dono simultaneously create karne ki koshish kar sakti hain.

Database unique constraint final protection de sakta hai.

---

# 7. Idempotency vs Upsert

Image ka last point bhi important hai:

> Write operation ko idempotent by design banao.

For example:

### Non-idempotent

```sql
INSERT INTO orders ...
```

Har baar run hoga → new record.

### Idempotent approach

```sql
UPSERT
```

Meaning:

> Record nahi hai → create
> Record already hai → update/ignore

Isse repeated operation duplicate create karne se bach sakta hai.

---

# 8. Interview mein kaise answer karna hai?

Agar interviewer pooche:

**"Your API is returning duplicate data due to retries. How do you fix it?"**

Aap bol sakte ho:

> "The root cause is that the client times out and retries even though the server has already processed the first request. I would make the API idempotent by introducing an idempotency key. The client sends a unique key for each logical operation, and the server stores that key along with the result. If the same key comes again due to a retry, the server doesn't process the operation again; it returns the previously stored result. I would also add a database-level unique constraint as a second safety mechanism, and wherever possible use idempotent operations such as upsert."

### Short version yaad rakhne ke liye:

**Retry → Duplicate → Idempotency Key → Store Result → Same Key = Same Result**

Aur ek strong interview line:

> **"The key represents the logical operation, not the HTTP request."**

Matlab retry mein **same key** use honi chahiye. New request hone ke baad bhi operation logically wahi hai.




===============================
====================
========================

Yes. I researched this specifically as an **interview-pattern topic**, not just definitions.

The important insight is: **Idempotency is much bigger than "duplicate payment."** It appears whenever a distributed system can **retry, redeliver, timeout, duplicate, replay, or concurrently repeat a side effect**. Current interview material repeatedly frames it around payments, POST retries, network timeouts, duplicate webhooks, Kafka redelivery, and concurrent requests. ([Greenroom][1])

For your **Java Full Stack / Spring Boot / Microservices** preparation, I would master the following **50 scenario questions**.

# 🔥 IDEMPOTENCY — MASTER SCENARIO QUESTION BANK

## A. Basic API / REST Scenarios

These are the most direct **"Idempotency"** questions.

### 1.

A user clicks the **"Place Order"** button twice and two orders are created. How would you prevent this?

➡️ **Idempotency**

### 2.

A client sends `POST /orders`, but the response is lost because of a network timeout. The client retries and a second order is created. How would you solve this?

➡️ **Idempotency**

### 3.

A payment API receives the same payment request twice. How do you make sure the customer is charged only once?

➡️ **Idempotency Key**

### 4.

The client doesn't know whether the previous request succeeded because the network connection was broken. Should it retry?

➡️ **Retry + Idempotency**

### 5.

A user double-clicks a **Pay Now** button. How would you prevent duplicate payment?

➡️ **Idempotency**

### 6.

A mobile application loses its internet connection immediately after submitting an order. When it reconnects, it sends the order again. How do you prevent duplicate orders?

➡️ **Idempotency**

### 7.

A REST API receives the same `POST` request multiple times because of client retries. How would you design the API?

➡️ **Idempotency Key**

### 8.

Your API gateway retries a failed request automatically. The backend creates duplicate records. How would you fix it?

➡️ **Idempotency**

### 9.

A load balancer retries a request after a timeout. The original request actually succeeded. What problem can occur and how do you solve it?

➡️ **Idempotency**

### 10.

A user refreshes the browser immediately after submitting a payment. The payment is created twice. What would you implement?

➡️ **Idempotency**

---

# B. Payment Scenarios 💳

These are **extremely important**.

### 11.

Payment service charges the customer successfully, but the response never reaches your application. Your application retries. How do you prevent double charging?

➡️ **Idempotency Key**

### 12.

Your payment gateway takes 10 seconds and your service times out after 5 seconds. You retry the payment. How do you know whether the first payment succeeded?

➡️ **Idempotency + Payment Status**

### 13.

A customer sees two charges for the same order. How would you investigate and prevent this?

➡️ **Idempotency**

### 14.

How would you design a payment API that can safely retry failed requests?

➡️ **Idempotency**

### 15.

A payment request is sent three times because of network instability. How should the payment service behave?

➡️ **Same Idempotency Key → Same logical operation**

### 16.

The client generates a new UUID every time it retries a payment. What is wrong with this design?

➡️ **Same key must be reused for the same logical operation**

### 17.

How would you design `POST /payments` so that it doesn't create duplicate payments?

➡️ **Idempotency Key + Unique Constraint**

### 18.

A payment request times out, but the payment gateway may have processed it. Should you blindly send another payment request?

➡️ **No → Retry using same idempotency key / query status**

### 19.

How would you design an idempotent money-transfer API?

➡️ **Idempotency Key + Transaction + Unique Constraint**

### 20.

A bank transfer request is accidentally submitted twice. How do you ensure the account is debited only once?

➡️ **Idempotency**

---

# C. Concurrent Requests 🔥

This is where interviewers go deeper.

### 21.

Two requests with the **same idempotency key arrive at exactly the same time**. How do you prevent both from executing?

➡️ **Unique DB constraint / atomic deduplication**

### 22.

Your code does:

```text
if (!exists(key)) {
    createPayment();
}
```

Two threads execute this simultaneously. What is the problem?

➡️ **Race condition / TOCTOU**

### 23.

How would you make idempotency thread-safe?

➡️ **Atomic insert + unique constraint**

### 24.

Two API instances receive the same request simultaneously. How can you guarantee only one executes the business operation?

➡️ **Distributed idempotency**

### 25.

Can you implement idempotency using only an in-memory `HashMap`?

➡️ **Not safely in a multi-instance application**

### 26.

Your Spring Boot application has 5 pods. Each pod maintains its own idempotency keys in memory. What problem occurs?

➡️ **Distributed state problem**

### 27.

Two requests have the same idempotency key but different payment amounts. What should happen?

➡️ **Reject the request**

A strong implementation stores a **request fingerprint/hash** along with the key. ([DevHippo][2])

### 28.

Request A with key `ABC123` is still processing. Request B arrives with the same key. What should happen?

➡️ **Do not execute twice; return appropriate in-progress/retry response**

Concurrent duplicate handling is an important senior-level follow-up. ([SkillVeris][3])

---

# D. Database + Idempotency 🗄️

### 29.

Would you use a database unique constraint to implement idempotency?

➡️ **Yes**

### 30.

Where would you store the idempotency key?

➡️ **Persistent store such as DB/Redis depending on requirements**

### 31.

Why isn't this enough?

```sql
SELECT * FROM payments
WHERE idempotency_key = ?;
```

followed by:

```sql
INSERT INTO payments ...
```

➡️ **Race condition**

### 32.

How would you make checking and storing an idempotency key atomic?

➡️ **Unique constraint / atomic operation**

### 33.

What happens if two servers try to insert the same idempotency key?

➡️ **One wins; unique constraint rejects the other**

### 34.

What information would you store with an idempotency key?

➡️ Typically:

```text
idempotency_key
request_hash
status
response
resource_id
created_at
expires_at
```

### 35.

Would you store the entire response against the idempotency key?

➡️ **Often yes for APIs where replaying the original response is required**

### 36.

How long should an idempotency key be stored?

➡️ **Depends on business/retry window; use an appropriate retention/TTL policy**

Don't claim there is one universal duration; real APIs define their own retention policy. For example, payment APIs explicitly document their own idempotency-key behavior. ([Amazon Developer][4])

---

# E. Microservices Scenarios

### 37.

Service A calls Service B. Service B successfully processes the request, but Service A times out. Service A retries. How do you prevent duplicate processing?

➡️ **Idempotency**

### 38.

Microservice A retries a request to microservice B three times. How should B handle it?

➡️ **Idempotent operation**

### 39.

An external payment service is slow and your service retries automatically. How do you make the retry safe?

➡️ **Same idempotency key**

### 40.

Service A creates an order and calls Payment Service. The payment call times out. What design would you use?

➡️ **Idempotency + retry/status check**

### 41.

A downstream service receives the same business command multiple times. How can it safely process them?

➡️ **Idempotent consumer/operation**

### 42.

How would you design a microservice so that retries don't create duplicate business operations?

➡️ **Idempotent APIs**

### 43.

An API Gateway retries a request to multiple backend instances. How should the backend handle duplicate requests?

➡️ **Distributed idempotency**

---

# F. Kafka / Messaging Scenarios 📨

This is **very important for your Java Microservices interviews**.

Kafka commonly uses at-least-once processing, so duplicate processing can occur; making the consumer idempotent is a standard solution. ([JavaTechOnline][5])

### 44.

Kafka delivers the same order event twice. How do you prevent inventory from being reduced twice?

➡️ **Idempotent Consumer**

### 45.

A Kafka consumer processes a payment event successfully but crashes before committing the offset. What happens when it restarts?

➡️ **Message may be delivered again**

### 46.

How should your Kafka consumer handle duplicate messages?

➡️ **Idempotent processing**

### 47.

A Kafka event contains:

```text
eventId = 12345
```

The same event arrives twice. What would you do?

➡️ **Store/check processed event ID**

### 48.

How would you prevent a Kafka consumer from charging a customer twice?

➡️ **Idempotent Consumer + event ID / transaction record**

### 49.

Kafka provides at-least-once delivery. How do you prevent duplicate business effects?

➡️ **Idempotent consumer**

### 50.

Your consumer receives:

```text
ORDER_CREATED
ORDER_CREATED
```

How should the Inventory Service behave?

➡️ **Process the business effect once**

---

# ⭐ The 10 MOST IMPORTANT ones

If the interviewer asks any of these, you should immediately think:

> **IDEMPOTENCY**

| #  | Scenario                                       | Think                     |
| -- | ---------------------------------------------- | ------------------------- |
| 1  | User double-clicks Pay                         | Idempotency               |
| 2  | Payment timeout + retry                        | Idempotency               |
| 3  | Same POST received twice                       | Idempotency               |
| 4  | Duplicate order                                | Idempotency               |
| 5  | Duplicate bank transfer                        | Idempotency               |
| 6  | Downstream timeout + retry                     | Idempotency               |
| 7  | Kafka duplicate event                          | Idempotent Consumer       |
| 8  | Consumer crashes before offset commit          | Idempotent Consumer       |
| 9  | Two same requests arrive simultaneously        | Idempotency + Concurrency |
| 10 | Lost response but operation may have succeeded | Idempotency               |

---

# 🧠 The MASTER PATTERN

When interviewer gives you a scenario, look for these words:

```text
        SCENARIO
           │
           ▼
   ┌─────────────────┐
   │ Same operation  │
   │ happens again?  │
   └────────┬────────┘
            │
           YES
            │
            ▼
      ┌────────────┐
      │   RETRY?   │
      └─────┬──────┘
            │
           YES
            │
            ▼
      ┌─────────────┐
      │ TIMEOUT /   │
      │ NETWORK     │
      │ FAILURE?    │
      └──────┬──────┘
             │
            YES
             │
             ▼
       ⭐ IDEMPOTENCY
```

Also watch for:

```text
duplicate
retry
timeout
double click
network failure
lost response
same request
same event
redelivery
at-least-once
reprocessing
replay
duplicate payment
duplicate order
duplicate webhook
consumer crash
gateway retry
concurrent request
```

These are strong **idempotency signals**.

---

# ⚠️ But don't confuse these concepts

This is where interviewers try to trap candidates.

### Scenario:

> "Downstream service is down. What should you use?"

Not automatically idempotency.

Possible answer:

```text
Timeout
+
Circuit Breaker
+
Limited Retry
+
Exponential Backoff
+
Jitter
```

But if they say:

> "You retry the downstream payment and it might already have succeeded."

Now:

```text
Retry
     +
Already processed?
     ↓
IDEMPOTENCY
```

---

### Scenario:

> "Kafka consumer is slow."

Not idempotency.

Answer:

```text
Consumer Lag
Partitions
Consumer Instances
Processing Time
DB/Downstream Bottleneck
```

But:

> "Kafka consumer receives the same message twice."

Now:

```text
IDEMPOTENT CONSUMER
```

---

### Scenario:

> "Two users update the same record."

Not automatically idempotency.

Usually:

```text
Optimistic Locking
OR
Pessimistic Locking
```

But:

> "The same command/request is submitted twice."

Now:

```text
IDEMPOTENCY
```

---

# 🔥 Senior-level Idempotency Flow

For your **5+ years Java Full Stack interview**, don't stop at:

> "I'll use an idempotency key."

That's only the beginning.

A stronger answer is:

```text
Client
   │
   │ Idempotency-Key: ABC123
   ▼
API
   │
   ▼
Idempotency Store
   │
   ├── ABC123 already COMPLETED
   │          │
   │          └── Return stored response
   │
   ├── ABC123 IN_PROGRESS
   │          │
   │          └── Don't execute again
   │
   └── ABC123 doesn't exist
              │
              ▼
        Atomically claim key
              │
              ▼
        Execute operation
              │
              ▼
        Store result
              │
              ▼
        Return response
```

The important senior-level details are:

**Idempotency Key → Unique Constraint → Request Fingerprint → Atomic Claim → Status → Stored Result → Replay → TTL**

Modern interview material specifically emphasizes that simply doing "check if key exists, then execute" has a race condition; atomic deduplication is the important part. ([InterviewsVector][6])

---

# 🏆 Your one-line memory trick

Whenever the interviewer says:

> **"What if the same operation happens again because of retry/network failure/duplicate delivery?"**

Your brain should immediately say:

# **IDEMPOTENCY**

Then expand:

> **Idempotency Key → Unique Constraint → Atomic Processing → Store Result → Return Same Result on Retry**

And for Kafka:

> **Event ID → Processed-event Store → Idempotent Consumer**

This gives you a reusable answer across **REST + Spring Boot + Microservices + Payments + Kafka + Distributed Systems + System Design**. ([Algoroq][7])

[1]: https://usegreenroom.app/blog/razorpay-backend-engineer-interview-questions?utm_source=chatgpt.com "Razorpay Backend Interview Questions (2026 Guide)"
[2]: https://devhippo.win/messaging-integration/rest/rest?utm_source=chatgpt.com "REST — Interview Q&A"
[3]: https://www.skillveris.com/interview-questions/restapi/idempotency-key-concurrency-and-replay?utm_source=chatgpt.com "Concurrent Requests With the Same Idempotency Key | SkillVeris"
[4]: https://developer.amazon.com/docs/amazon-pay-api-v2/v1-idempotency.html?utm_source=chatgpt.com "Idempotency | Amazon Pay"
[5]: https://javatechonline.com/java-microservices-interview-cheat-sheet-2026/?utm_source=chatgpt.com "Java Microservices Interview Cheat Sheet- 100 Quick-Fire Questions (2026) - JavaTechOnline"
[6]: https://www.interviewsvector.com/distributed-systems/modules/idempotency-exactly-once?utm_source=chatgpt.com "Idempotency & 'Exactly-Once' That Survives Contact | InterviewsVector"
[7]: https://www.algoroq.io/concepts/idempotency/?utm_source=chatgpt.com "Idempotency Explained: Designing Safe Retries in Distributed Systems | Algoroq"





