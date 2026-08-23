# 1
Haan, **question ko dekhte hi tumhe identify karna hai ki ye kis concept par hai.**

Image wala question basically ye hai:

> **"A Kafka consumer crashes while processing a message before committing its offset. When the consumer restarts, which message will it receive first?"**

### Simple Hindi mein question ka matlab

**"Consumer ek message (`m1`) process kar raha tha, processing ke beech mein crash ho gaya, aur usne offset commit nahi kiya. Restart hone ke baad Kafka sabse pehle kaunsa message dega?"**

Options agar hote to likely:

```text
A. m1
B. m2
C. m3
D. No message
```

✅ **Answer: A. m1**

### Is question ko dekhte hi trick

Bas ye 3 cheezein dekho:

```text
Consumer crash?
      ↓
Offset committed?
      ↓
NO
      ↓
Same message again
```

So:

**Crash + Offset NOT committed = Message reprocessed**

Ye question actually **Kafka Consumer Offset / Offset Commit / Consumer Crash / Message Redelivery** concept test kar raha hai.
==========================================================================================================================================


Haan, is scenario ka main point **Kafka ka offset + consumer crash** samajhna hai. Simple example se dekho:

### Pehle situation

Kafka topic mein 3 messages hain:

```text
Partition 0

offset 0 → m1
offset 1 → m2
offset 2 → m3
```

Consumer messages ko sequence mein process karta hai:

```text
m1 → m2 → m3
```

Ab consumer **m1 process kar raha tha**, lekin m1 process karte time **exception aa gayi aur consumer crash ho gaya**.

### Ab important question

**Consumer restart hone ke baad sabse pehle kaunsa message milega?**

👉 **m1**

Kyun?

Kafka mein consumer ka **offset commit** hota hai.

Suppose:

```text
m1 → processing started
      ↓
   ERROR ❌
      ↓
consumer crash
```

m1 successfully process nahi hua, isliye uska offset **commit nahi hua**.

Kafka ke perspective se:

```text
m1 = abhi bhi consume nahi hua
```

Isliye restart ke baad:

```text
Consumer restart
      ↓
Kafka last committed offset dekhta hai
      ↓
m1 dobara deta hai
```

### Ekdum simple real-life example

Socho tumhare paas 3 tasks hain:

```text
Task 1 = m1
Task 2 = m2
Task 3 = m3
```

Tumne Task 1 start kiya:

```text
Task 1 → kaam karte waqt computer crash ❌
```

Tumne Task 1 complete hone ka record save hi nahi kiya.

Computer restart hua.

System bolega:

> "Last completed task ka record nahi hai, toh Task 1 se dobara start karo."

Exactly Kafka mein bhi:

```text
m1 → processing ❌ → offset commit nahi hua
                    ↓
                 CRASH
                    ↓
              Consumer restart
                    ↓
                 m1 again
```

### Image ka ye sentence kya keh raha hai?

> **"No offset was committed before the crash"**

Matlab:

**Crash hone se pehle consumer ne kisi message ka successful offset save/commit nahi kiya tha.**

Isliye:

> **"last committed offset is still −1"**

`-1` ka simple meaning yahan hai:

**abhi tak kuch bhi successfully commit nahi hua.**

Therefore:

```text
Restart
   ↓
m1
   ↓
m2
   ↓
m3
```

### Interview mein short answer

**Q: If a Kafka consumer crashes while processing m1 before committing its offset, which message will it receive after restart?**

**Answer:**
👉 **m1 again**, because its offset was not committed before the crash. Kafka resumes from the last committed offset, so the uncommitted message is reprocessed.

**Main concept yaad rakho:**

> 🔑 **Kafka mein committed offset = "yahan tak successfully process kar liya."**
> Agar offset commit nahi hua → **message dobara mil sakta hai.**
