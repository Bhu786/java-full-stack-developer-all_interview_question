Bilkul. Main **isi DOCX ke 50 questions** ko base bana kar answers ko **real enterprise Spring Boot + Microservices project** ke context mein frame kar raha hoon. Source mein 50 questions fundamentals se lekar production troubleshooting tak cover hain. 

Main answers ko aise rakh raha hoon ki interview mein aap **ratta hua theoretical answer nahi**, balki **real project mein kaise use kiya** ye explain kar sako.

# Kafka Interview Q&A — Enterprise Project Version

## 1. Kafka kya hai aur microservices mein kyun use karte ho?

**Answer:**

Kafka ek **distributed event streaming platform** hai jo services ke beech asynchronous communication ke liye use hota hai.

Mere project mein Booking Service directly Notification, Payment aur Inventory Service ko call karne ke bajay Kafka mein `BookingCreated` ya `BookingConfirmed` event publish karti hai.

Isse:

* services loosely coupled rehti hain
* asynchronous processing hoti hai
* multiple services same event independently consume kar sakti hain
* traffic spike handle karna easier hota hai
* failed consumer baad mein event process kar sakta hai

Example:

```text
Booking Service
      |
      | BookingConfirmed
      v
   Kafka Topic
   /    |     \
  /     |      \
Payment Notification Inventory
```

Yehi document mein Booking → Notification/Payment/Inventory flow ke liye explain kiya gaya hai. 

---

# 2. Kafka architecture ke main components kya hain?

Main components:

* **Producer** → message/event publish karta hai
* **Broker** → Kafka server jo records store karta hai
* **Topic** → events ka logical channel
* **Partition** → topic ka parallel/logical segment
* **Consumer** → messages consume karta hai
* **Consumer Group** → consumers ka group jo partitions ko divide karta hai
* **Offset** → partition ke andar message position
* **KRaft** → modern Kafka mein cluster metadata/coordination ke liye

Example:

```text
Booking Service
      |
   Producer
      |
      v
booking-events
 ├── Partition 0
 ├── Partition 1
 └── Partition 2
      |
   Consumers
```

Source bhi Broker, Topic, Partition, Producer, Consumer, Consumer Group, KRaft aur Offset ko core components ke roop mein list karta hai. 

---

# 3. Topic aur Partition mein difference?

**Topic logical channel hai, partition us topic ka parallel log segment hai.**

Example:

```text
Topic: booking-events

Partition 0
Partition 1
Partition 2
```

Agar mere paas 3 partitions hain, to messages parallelism ke liye different partitions mein ja sakte hain.

Important:

> **Ordering only partition level par guarantee hoti hai.**

Agar mujhe same booking ke events ordered chahiye:

```text
BookingCreated
BookingConfirmed
BookingCancelled
```

to `bookingId` ko key banaunga, jisse same booking ke events same partition mein jayenge. 

---

# 4. Kafka itna fast kyun hai?

Kafka ke major performance reasons:

1. Sequential disk writes
2. Append-only log
3. Page cache
4. Zero-copy
5. Batching
6. Compression

Kafka har message ke liye random disk operation karne ke bajay sequential append karta hai.

Producer multiple records ko batch mein bhej sakta hai.

Source specifically sequential disk I/O, zero-copy, page cache aur batching/compression ko Kafka performance ke reasons batata hai. 

---

# 5. `producer.send()` internally kya karta hai?

Jab Spring Boot mein:

```java
kafkaTemplate.send("booking-topic", bookingId, event);
```

karte hain, broadly:

```text
Object
 ↓
Serializer
 ↓
Partitioner
 ↓
RecordAccumulator
 ↓
Producer Sender
 ↓
Kafka Broker
 ↓
ACK
```

Producer event ko serialize karta hai, partition select karta hai, batch mein rakhta hai aur broker ko send karta hai.

Source mein serializer → partitioner → accumulator → sender → broker acknowledgment ka flow diya gaya hai. 

---

# 6. `acks` kya hota hai?

`acks` decide karta hai producer ko broker se kitni acknowledgment chahiye.

### `acks=0`

Broker acknowledgement wait nahi karta.

Fast hai but data loss risk hai.

### `acks=1`

Leader acknowledgement deta hai.

Performance aur durability ka balance.

### `acks=all`

Required in-sync replicas acknowledgement deti hain.

Critical business events ke liye safer.

Enterprise example:

```text
PaymentCompleted
BookingConfirmed
```

jaise critical events mein strong durability configuration prefer karunga.

Source critical Booking/Payment events ke liye `acks=all` discuss karta hai. 

---

# 7. Producer idempotence kya hai?

Producer retry ke wajah se duplicate records create hone ka risk hota hai.

Idempotent producer:

```properties
enable.idempotence=true
```

Kafka producer ID aur sequence numbers ke through duplicate retry records detect karne mein help karta hai.

Interview mein important distinction:

> **Producer idempotence duplicate producer retries ko handle karta hai; ye business-level duplicate processing ko completely solve nahi karta.**

Consumer side par bhi idempotency implement karni padti hai.

Source producer ID aur sequence number based duplicate detection explain karta hai. 

---

# 8. Kafka message key ka role kya hai?

Key partition selection ke liye important hai.

Example:

```java
kafkaTemplate.send(
    "booking-events",
    bookingId,
    event
);
```

Agar `bookingId` key hai, same booking ke events same partition mein jaane ka basis milta hai.

Example:

```text
bookingId=101
 CREATED
 CONFIRMED
 CANCELLED
```

Same partition → ordered processing.

Source bhi `orderId`/`bookingId` ko partition key ke example ke roop mein use karta hai. 

---

# 9. `batch.size` aur `linger.ms` kya hain?

### `batch.size`

Producer ek batch mein kitna data collect kar sakta hai.

### `linger.ms`

Producer batch ko fill hone ke liye kitna wait karega.

Example:

High-volume notification events:

```text
10 messages aaye
↓
batch collect
↓
single request
↓
Kafka
```

Higher batching → better throughput but potentially slightly higher latency.

Source bhi isi throughput-vs-latency trade-off ko explain karta hai. 

---

# 10. Producer retry se ordering kaise affect hoti hai?

Suppose:

```text
Message A → fail
Message B → success
Message A → retry
```

To ordering issue aa sakta hai depending on producer configuration.

Modern Kafka mein idempotence enable karna aur appropriate `max.in.flight.requests.per.connection` configuration use karna important hai.

Source specifically retries + in-flight requests ke ordering issue ko discuss karta hai. 

---

# 11. Consumer Group kaise kaam karta hai?

Suppose:

```text
Topic
 P0
 P1
 P2
```

Consumer group:

```text
Consumer-1 → P0
Consumer-2 → P1
Consumer-3 → P2
```

Same consumer group mein ek partition ek time par ek consumer ko assigned hota hai.

Isliye consumer group **horizontal scaling** provide karta hai.

Source exactly partition assignment aur load balancing explain karta hai. 

---

# 12. 3 partitions aur 5 consumers hain. Kya hoga?

Only **3 consumers actively consume** karenge.

```text
P0 → C1
P1 → C2
P2 → C3

C4 → idle
C5 → idle
```

Isliye:

> Consumer instances ko blindly increase karne se throughput nahi badhega; partitions bhi sufficient hone chahiye.



---

# 13. Consumer offset commit strategies?

Main strategies:

### Auto commit

Kafka automatically offsets commit karta hai.

Simple but critical processing ke liye risk ho sakta hai.

### `commitSync()`

Processing complete hone ke baad manually commit.

Reliable but blocking.

### `commitAsync()`

Non-blocking commit.

Higher throughput but failure handling carefully design karna padta hai.

Enterprise booking flow mein:

```text
Consume event
 ↓
Validate
 ↓
DB operation
 ↓
Success
 ↓
Commit offset
```

Ye **at-least-once + idempotent processing** approach ke liye useful hai. 

---

# 14. Consumer rebalancing kya hai?

Rebalancing tab hoti hai jab consumer group membership ya partition assignment change hota hai.

Example:

```text
C1 → P0
C2 → P1
C3 → P2
```

C2 crash:

```text
C1 → P0
C3 → P2
C1/C3 → P1 redistribution
```

Production mein unnecessary rebalance minimize karna important hai.

Source new consumer joining, consumer failure aur partition changes ko rebalance triggers batata hai. 

---

# 15. `max.poll.interval.ms` kya karta hai?

Ye maximum time define karta hai jo consumer `poll()` calls ke beech le sakta hai.

Suppose consumer ko ek event process karne mein bahut time lag gaya:

```text
poll()
 ↓
long DB/API processing
 ↓
poll() delayed
```

Agar allowed interval exceed ho gaya, Kafka consumer ko unhealthy maan kar rebalance kar sakta hai.

Source long-running processing ke case mein `max.poll.interval.ms` tuning discuss karta hai. 

---

# 16. `session.timeout.ms` vs `heartbeat.interval.ms` vs `max.poll.interval.ms`

Simple interview answer:

**heartbeat.interval.ms**

> Consumer coordinator ko kitni frequently heartbeat bhejta hai.

**session.timeout.ms**

> Kitne time tak heartbeat na aaye to consumer dead maana jaaye.

**max.poll.interval.ms**

> Do `poll()` calls ke beech maximum allowed processing interval.



---

# 17. Kafka ordering guarantee kaise deta hai?

Kafka:

> **Per-partition ordering guarantee karta hai, globally nahi.**

Example:

```text
Partition 0:
101 CREATED
101 CONFIRMED
101 CANCELLED
```

Ye order preserve ho sakta hai.

Lekin:

```text
P0 → Booking A
P1 → Booking B
```

ke beech global order guarantee nahi hai.

Isliye related events ke liye same key use karte hain. 

---

# 18. Replication Factor kya hai?

Replication factor batata hai ki partition ki kitni copies cluster mein maintain hongi.

Example:

```text
RF = 3

Broker 1 → Leader
Broker 2 → Replica
Broker 3 → Replica
```

Agar Broker 1 fail ho gaya, replica leader ban sakta hai.

Production mein critical data ke liye RF=3 common choice hai. 

---

# 19. Leader aur ISR kya hai?

Har partition ka ek **leader** hota hai.

Producer writes normally leader ko jaati hain.

Baaki replicas leader ke data ko replicate karte hain.

**ISR = In-Sync Replicas**

Matlab replicas jo leader ke sufficiently synchronized hain.

Leader fail hone par ISR mein se eligible replica leader ban sakta hai. 

---

# 20. Partition leader crash ho gaya to?

Kafka controller failure detect karta hai.

Then eligible ISR replica ko new leader elect kiya ja sakta hai.

```text
Before:

Broker1 → Leader
Broker2 → ISR
Broker3 → ISR

Broker1 DOWN

After:

Broker2 → New Leader
Broker3 → Replica
```

Production durability ke liye unclean leader election carefully configure karte hain. 

---

# 21. Kafka ke delivery semantics?

Three semantics:

### At-most-once

Message lost ho sakta hai, duplicate nahi.

### At-least-once

Message duplicate ho sakta hai, but processing retry ke through loss avoid kiya jaata hai.

### Exactly-once

Processing semantics ko aise coordinate kiya jata hai ki duplicate/loss ko controlled transactional semantics ke through avoid kiya ja sake.

Source in teen semantics ko explicitly define karta hai. 

---

# 22. Booking → Payment → Notification mein exactly-once kaise?

Ye **tricky interview question** hai.

Real world mein:

```text
DB transaction
+
Kafka publish
```

do separate systems hain.

Agar DB successful hua but Kafka publish fail hua:

```text
DB = success
Kafka = failure
```

inconsistency ho sakti hai.

Isliye **Outbox Pattern** useful hai:

```text
Booking DB Transaction
       |
       +---- Booking
       |
       +---- Outbox Event
                  |
             Publisher/CDC
                  |
                Kafka
```

Source specifically outbox table + CDC/publisher approach explain karta hai. 

---

# 23. Kafka Transactions API kya karta hai?

Kafka transactions multiple Kafka operations ko atomic transaction mein perform karne mein help karti hain.

Common APIs:

```java
initTransactions()
beginTransaction()
commitTransaction()
```

Consumer side:

```properties
isolation.level=read_committed
```

use karne se transactional committed records consume kiye ja sakte hain.



---

# 24. Consumer duplicate messages ko kaise handle karoge?

**Idempotent consumer** design karunga.

Example:

```text
eventId = ABC123
```

DB:

```text
processed_events
----------------
ABC123
```

Event dobara aaya:

```text
ABC123 already exists
        ↓
Ignore
```

Ya business operation ko idempotent bana sakte hain using unique constraints/UPSERT.

Source `processed_events`, unique constraint aur idempotent DB operations suggest karta hai. 

---

# 25. Consumer processing fail ho jaye to retry kaise?

Enterprise approach:

```text
Main Topic
    ↓
Consumer
    ↓
Failure
    ↓
Retry
    ↓
Retry Topic
    ↓
Failure
    ↓
DLQ/DLT
```

Spring Kafka mein:

* `DefaultErrorHandler`
* `ExponentialBackOff`
* `DeadLetterPublishingRecoverer`

use kar sakte hain.



---

# 26. Poison Pill message kya hai?

Poison pill ek aisa message hai jo repeatedly process/deserialize nahi ho pa raha.

Example:

```text
Expected JSON
{
 "bookingId": 101
}

Received malformed data
```

Consumer baar-baar same message process karta rahe to partition block ho sakta hai.

Solution:

```text
Retry
 ↓
Retry limit reached
 ↓
DLT
 ↓
Next message
```



---

# 27. Consumer Lag kya hai?

Simple:

> Producer kitne messages produce kar chuka hai aur consumer kitna consume/commit kar chuka hai, uska difference consumer lag hai.

Example:

```text
Latest offset = 10000
Consumer offset = 8000

Lag = 2000
```

High lag means consumer production rate ke saath keep up nahi kar raha.

Monitor:

* Kafka consumer group tools
* Prometheus
* Grafana
* Kafka metrics



---

# 28. Booking confirm hone ke baad 3 services ko event kaise doge?

Same Kafka topic:

```text
booking-events
```

Then different consumer groups:

```text
booking-events
      |
      +---- payment-group
      |
      +---- notification-group
      |
      +---- inventory-group
```

Important:

> Same consumer group nahi.

Different consumer groups hone se har service ko event independently milta hai.



---

# 29. Saga Pattern mein Kafka ka role?

Saga distributed transaction ko multiple local transactions mein divide karta hai.

Example:

```text
OrderCreated
     ↓
Inventory Reserved
     ↓
Payment Completed
     ↓
Booking Confirmed
     ↓
Notification Sent
```

Agar payment fail:

```text
PaymentFailed
     ↓
StockReleased
```

Ye **compensating action** hai.

Kafka yahan reliable event bus ke role mein aa sakta hai. 

---

# 30. Same `orderId` ko multiple services update karte hain. Race condition?

`orderId` ko Kafka key bana sakte hain.

```text
key = orderId
```

Same order ke events same partition mein route honge.

Isse events sequentially process karne ka mechanism milta hai.

But interview mein ek important point:

> Kafka ordering concurrency ko reduce/manage karta hai, lekin database-level concurrent updates ke liye optimistic locking/versioning bhi required ho sakti hai depending on design.

Source same key ko natural concurrency control ke context mein explain karta hai. 

---

# 31. Notification Service 10 minutes down ho gayi. Messages lost?

Normally **not immediately**, assuming messages are still within topic retention and consumer offsets are maintained.

```text
Kafka
 ↓
messages retained
 ↓
Notification DOWN
 ↓
10 min
 ↓
Notification UP
 ↓
resume from committed offset
```

Source retention ke through isi scenario ko explain karta hai. 

---

# 32. Kafka mein PII data kaise handle karoge?

Email/phone jaise sensitive data ko blindly events mein publish nahi karunga.

Approach:

* sensitive fields minimize karo
* encryption where required
* ACLs
* authentication/authorization
* schema governance
* passwords/tokens kabhi event payload mein nahi

Example:

Instead of:

```json
{
  "email": "user@gmail.com",
  "password": "..."
}
```

event mein business identifier bhejna better ho sakta hai:

```json
{
  "bookingId": 101,
  "userId": 5001
}
```

Source PII masking/encryption, ACLs aur sensitive credentials ko avoid karne ki baat karta hai. 

---

# 33. Kafka Connect kab use karoge?

Jab external system aur Kafka ke beech data move karna ho without writing a lot of custom integration code.

Example:

```text
PostgreSQL
    ↓
Debezium CDC
    ↓
Kafka
    ↓
Elasticsearch
```

Use cases:

* DB → Kafka
* Kafka → Elasticsearch
* Kafka → external systems

Source Debezium CDC aur Elasticsearch sink ka example deta hai. 

---

# 34. Schema Evolution kaise handle karoge?

Suppose current event:

```json
{
  "bookingId": 101,
  "amount": 5000
}
```

New requirement:

```json
{
  "bookingId": 101,
  "amount": 5000,
  "discountCode": "NEW50"
}
```

Schema Registry + Avro/Protobuf jaise schema formats use karke compatibility manage kar sakte hain.

New field ko optional/default value ke saath add karna backward compatibility maintain karne mein help karta hai.

Source backward, forward aur full compatibility modes discuss karta hai. 

---

# 35. Consumer throughput kaise improve karoge?

Main check karunga:

1. Enough partitions?
2. Enough consumers?
3. Consumer processing slow hai?
4. DB bottleneck?
5. External API slow?
6. `max.poll.records`
7. Fetch configuration

Agar processing independent hai to controlled parallel processing bhi use kar sakte hain.

Source partition count, fetch settings, `max.poll.records` aur async/parallel processing suggest karta hai. 

---

# 36. Producer throughput kaise improve karoge?

Tune:

```properties
batch.size
linger.ms
compression.type
buffer.memory
```

Compression:

```text
snappy
lz4
```

use kar sakte hain.

Trade-off:

> Throughput improve karte waqt latency aur CPU usage ko bhi monitor karna hota hai.



---

# 37. Topic mein kitne partitions rakhoge?

Fixed answer nahi hai.

Factors:

* expected throughput
* producer throughput
* consumer throughput
* required parallelism
* number of consumers
* future scaling

Load testing ke basis par decide karunga.

Source partition sizing ko throughput aur consumer parallelism ke basis par decide karne ko kehta hai. 

---

# 38. `acks=all` but `min.insync.replicas=1` hai. Safe hai?

**Not sufficiently safe.**

Agar RF=3 hai aur:

```properties
acks=all
min.insync.replicas=1
```

to effectively only leader ke available hone par write accept ho sakta hai.

Better durability ke liye common configuration:

```text
RF = 3
min.insync.replicas = 2
acks = all
```

Source isi relationship ko specifically highlight karta hai. 

---

# 39. Consumer group mein ek consumer bahut slow hai?

Us consumer ke assigned partition ka lag increase karega.

Example:

```text
C1 → P0 → normal
C2 → P1 → slow
C3 → P2 → normal
```

P1 ka lag continuously increase karega.

Root causes check karunga:

* DB slow
* external API slow
* GC
* CPU
* insufficient resources
* inefficient business logic



---

# 40. Same consumer group ke 2 consumers same partition read kar sakte hain?

**No, same group ke andar ek partition ek time par ek consumer ko assigned hota hai.**

Agar multiple services ko same event independently chahiye:

```text
Topic
 |
 +-- group-A
 |
 +-- group-B
 |
 +-- group-C
```



---

# 41. `producer.send()` synchronous hai ya asynchronous?

Default:

> **Asynchronous**

Example:

```java
kafkaTemplate.send("booking-topic", event);
```

Immediately return kar sakta hai.

Agar synchronous confirmation chahiye to future wait kar sakte ho, but production code mein generally unnecessary blocking avoid karte hain.

Source `send()` ke asynchronous behavior aur callback approach ko explain karta hai. 

---

# 42. Consumer processing ke beech crash ho gaya?

Suppose:

```text
Consume
 ↓
DB update SUCCESS
 ↓
Consumer crashes
 ↓
Offset commit nahi hua
```

Restart hone ke baad same message dobara process ho sakta hai.

Isliye:

> **DB operation idempotent hona chahiye.**

Example:

```text
eventId = ABC123
```

Already processed hai → duplicate ko ignore.

Source exactly DB-success/offset-failure scenario ko explain karta hai. 

---

# 43. Kafka vs RabbitMQ?

Simple interview answer:

**Kafka:**

* distributed log
* high throughput
* retention
* replay
* event streaming
* multiple consumer groups

**RabbitMQ:**

* traditional message broker
* queue-oriented
* routing patterns
* acknowledgement
* request/task style messaging ke liye useful

Source Kafka ko log-based persistent/replayable aur RabbitMQ ko queue-based routing-oriented system ke roop mein compare karta hai. 

---

# 44. "Aapne Kafka kaha use kiya? End-to-end flow explain karo."

**Ye answer interview mein bahut important hai.**

Aap bol sakte ho:

> "Mere Booking microservices flow mein booking successfully create hone ke baad hum BookingConfirmed event generate karte hain. DB transaction aur event consistency maintain karne ke liye Outbox Pattern use kiya. Outbox event Kafka ke `booking-events` topic mein publish hota hai, aur `bookingId` ko key ke roop mein use kiya, jisse same booking ke events same partition mein maintain hote hain. Payment, Notification aur Inventory services separate consumer groups ke through event consume karti hain. Notification processing successful hone ke baad offset commit karta hai. Failure ke case mein retry aur DLT mechanism use hota hai."

Ye source ke end-to-end StayHub/Booking flow ke closely aligned hai. 

---

# 45. ZooKeeper vs KRaft?

Modern Kafka mein **KRaft** architecture use hota hai.

Old architecture:

```text
Kafka
  |
ZooKeeper
```

Modern:

```text
Kafka
  |
KRaft
```

KRaft Kafka ke andar metadata/consensus management provide karta hai, so separate ZooKeeper dependency remove hoti hai.



---

# 46. Kafka mein JSON message kaise publish/consume karte ho?

Spring Boot mein producer side:

```properties
spring.kafka.producer.value-serializer=
org.springframework.kafka.support.serializer.JsonSerializer
```

Consumer side:

```properties
spring.kafka.consumer.value-deserializer=
org.springframework.kafka.support.serializer.JsonDeserializer
```

Source mein exactly ye Spring Kafka serializer/deserializer configuration diya hai. 

---

# 47. Message size 20 MB hai. Kya karoge?

20 MB message Kafka mein directly bhejna generally avoid karunga.

Better architecture:

```text
Large File
   ↓
S3/Object Storage
   ↓
Kafka
   ↓
Only file reference/URL
```

Example event:

```json
{
  "bookingId": 101,
  "documentUrl": "s3://bucket/..."
}
```

Source bhi large payload ke case mein broker/producer limits tune karne ya external storage use karne ka approach deta hai. 

---

# 48. High traffic kaise handle karoge?

Kafka ka major scaling mechanism:

```text
Increase Partitions
        +
Increase Consumers
```

Example:

```text
12 partitions
12 consumers
```

tak parallel processing possible ho sakti hai, assuming workload and assignment support it.

Source high traffic ke liye partitions aur consumer instances increase karne ko suggest karta hai. 

---

# 49. Kafka ko secure kaise karoge?

Production Kafka mein:

### Authentication

SASL

### Encryption

SSL/TLS

### Authorization

ACLs

Example:

```text
Notification Service
      |
      | authorized read
      v
booking-events
```

Payment service ko sirf required topics ka access diya ja sakta hai.

Source SSL/TLS, SASL aur ACL-based security mention karta hai. 

---

# 50. Production Kafka issue jo tumne solve kiya?

**Ye answer interview mein sabse genuine lagna chahiye.**

Example:

> "Production mein humne observe kiya ki Notification Service ka consumer lag continuously increase ho raha tha. Monitoring mein dekha ki email provider ko synchronous call ki wajah se ek message process karne mein zyada time lag raha tha. Consumer messages consume kar raha tha but processing throughput producer rate se kam tha. Humne processing ko asynchronous worker/thread-pool based approach mein optimize kiya, consumer instances scale kiye aur required partitions tune kiye. Uske baad consumer lag significantly reduce hua aur notification processing near real-time ho gayi."

Source mein exactly isi type ka production scenario diya gaya hai: slow third-party email API → growing consumer lag → asynchronous processing/thread pools → scaling → lag reduction. 

---

# 🔥 Interview ke liye sabse important 15

Aapke **3-year Spring Boot + Microservices** profile ke liye main in questions ko highest priority dunga:

| Priority | Question                         |
| -------- | -------------------------------- |
| ⭐⭐⭐⭐⭐    | Kafka kya hai aur kyun?          |
| ⭐⭐⭐⭐⭐    | Topic vs Partition               |
| ⭐⭐⭐⭐⭐    | Consumer Group                   |
| ⭐⭐⭐⭐⭐    | Kafka mein ordering              |
| ⭐⭐⭐⭐⭐    | Producer/Consumer flow           |
| ⭐⭐⭐⭐⭐    | `acks`                           |
| ⭐⭐⭐⭐⭐    | Offset commit                    |
| ⭐⭐⭐⭐⭐    | Consumer lag                     |
| ⭐⭐⭐⭐⭐    | Retry + DLT                      |
| ⭐⭐⭐⭐⭐    | Idempotency                      |
| ⭐⭐⭐⭐⭐    | Kafka vs RabbitMQ                |
| ⭐⭐⭐⭐⭐    | Outbox Pattern                   |
| ⭐⭐⭐⭐⭐    | Multiple consumer groups         |
| ⭐⭐⭐⭐⭐    | Kafka down/service down scenario |
| ⭐⭐⭐⭐⭐    | Production issue/debugging       |

### Ek important baat

Aapke DOCX mein **50 questions hain**, lekin interview mein interviewer usually isolated definitions nahi poochta. Wo chain mein cross-question karta hai:

```text
Kafka kya hai?
      ↓
Topic kya hai?
      ↓
Partition kyun?
      ↓
Ordering kaise?
      ↓
Consumer group kya?
      ↓
Consumer crash?
      ↓
Offset commit?
      ↓
Duplicate message?
      ↓
Idempotency?
      ↓
Retry?
      ↓
DLT?
      ↓
Consumer lag?
      ↓
Production mein kaise debug kiya?
```

Is chain ko confidently explain kar paana **50 answers ratne se zyada important** hai. Source ka second half bhi specifically code, production, debugging aur architecture-oriented questions par focus karta hai. 

**Aapke interview ke liye best approach:** in 50 answers ko ab **“Interviewer Question → Aapka 30–60 sec spoken answer → Cross-question → Cross-question ka answer → Real project example”** format mein prepare karna.
