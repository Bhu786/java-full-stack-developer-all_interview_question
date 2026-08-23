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
