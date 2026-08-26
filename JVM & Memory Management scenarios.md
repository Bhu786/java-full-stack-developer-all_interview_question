Yes — **only scenario-based questions**, no theory/definition questions.

Below is a focused **JVM + Memory scenario-only master list**.

# JVM & MEMORY — SCENARIO-BASED INTERVIEW QUESTIONS

## 🔥 Production Memory Problems

1. Your Java application is running fine for 2 hours, but memory keeps increasing and eventually the application crashes. How would you investigate?

2. Your Spring Boot application suddenly throws `OutOfMemoryError: Java heap space` in production. What would you do?

3. Memory reaches 90%, GC runs, but memory still remains around 85%. What would you investigate?

4. Memory reaches 90%, Full GC runs, and memory drops to 30%. Is this a memory leak? How would you determine the real problem?

5. Your application memory increases during traffic spikes but returns to normal after traffic decreases. What would you investigate?

6. Memory increases during traffic spikes and remains high even after traffic drops. What could be happening?

7. Your application works normally after restart, but after 24–48 hours it again crashes because of memory. How would you troubleshoot?

8. Your application crashes immediately after deployment with an OOM error. What would you check?

9. Your application crashes only during peak traffic. How would you determine whether the problem is heap, GC, threads, database or something else?

10. Production reports slow APIs and high memory simultaneously. How would you investigate?

---

## 🔥 Garbage Collection Scenarios

11. Your application is spending most of its CPU time in GC. How would you troubleshoot?

12. Full GC is happening every few seconds and API latency has increased. What would you check?

13. Young GC is happening extremely frequently. What could be causing it?

14. Young GC is normal, but Old Generation keeps increasing. What would you investigate?

15. Old Generation becomes full and Full GC cannot free much memory. What does this suggest?

16. GC pauses suddenly increase from milliseconds to several seconds. How would you investigate?

17. Application becomes unresponsive whenever Full GC occurs. How would you troubleshoot it?

18. GC frequency increases after a new release. What would you compare between the old and new versions?

19. Heap usage is high but GC is not able to reclaim much memory. What would you investigate?

20. GC is running continuously but application still eventually throws OOM. How would you diagnose the issue?

21. CPU reaches 90%, GC activity is also very high, but application traffic hasn't increased. What could be happening?

22. After increasing the heap size, GC frequency decreases but application still becomes slower over time. What would you investigate?

23. You changed the GC configuration and production latency became worse. How would you troubleshoot?

24. G1 GC is producing unexpectedly long pauses in your application. What would you investigate?

25. Your application has a very large heap and strict latency requirements. How would you decide whether to change the GC configuration?

---

# 🔥 Memory Leak Scenarios

26. A `static List` keeps growing every time an API is called. What problem can this cause and how would you fix it?

27. A cache continuously grows because entries are never removed. How would you diagnose and fix the memory problem?

28. Your application has an in-memory `HashMap` containing millions of objects. Memory continuously increases. What would you investigate?

29. You removed objects from a collection, but JVM memory doesn't immediately decrease. How would you determine whether there is actually a leak?

30. Heap usage increases after every deployment, even though traffic remains similar. What would you investigate?

31. A listener is registered whenever a request is processed but is never deregistered. What memory problem could occur?

32. A singleton object keeps references to thousands of request objects. How would you diagnose this?

33. A `ThreadLocal` contains a large object in a thread pool. What could happen over time?

34. Your application creates temporary objects very quickly, and memory/GC usage is extremely high. How would you investigate?

35. A third-party library appears to retain objects even after requests finish. How would you prove it?

36. Two heap dumps taken six hours apart show that the same object type has increased dramatically. What would you investigate next?

37. Your application memory grows slowly but never crashes. How would you determine whether it is a leak or normal JVM behavior?

38. Memory grows after every batch job execution and never returns to the previous level. How would you troubleshoot?

39. A cache was introduced in the latest release and memory problems started afterward. How would you verify whether the cache is responsible?

40. An application has stable traffic but memory usage continuously increases. What evidence would you collect?

---

# 🔥 Heap Dump / Debugging Scenarios

41. Production JVM is close to OOM. How would you investigate without guessing?

42. You have taken a heap dump. What would you look for first?

43. Heap dump shows millions of `String` objects. How would you investigate?

44. Heap dump shows millions of `byte[]` objects. What possible application problems would you investigate?

45. Heap dump shows one class retaining several GB of memory. How would you find the reason?

46. You find a large object in a heap dump. How would you determine what is preventing it from being garbage collected?

47. Two heap dumps show increasing retained memory. How would you compare them?

48. MAT shows a huge dominator tree. How would you use it to find the memory leak?

49. Heap dump shows a large collection retained by a static field. How would you fix the problem?

50. Heap dump doesn't show an obvious leak, but container memory continues increasing. What would you investigate next?

51. Production application is about to crash from OOM. What JVM evidence would you collect before restarting it?

52. You cannot reproduce the memory problem locally. How would you diagnose it in production?

---

# 🔥 OOM Scenarios

53. Application throws `OutOfMemoryError: Java heap space`. How would you identify the root cause?

54. Application throws `OutOfMemoryError: Metaspace`. What would you investigate?

55. Application throws `OutOfMemoryError: unable to create native thread`. What would you check?

56. Application throws `OutOfMemoryError: Direct buffer memory`. What would you investigate?

57. Application throws `GC overhead limit exceeded`. How would you troubleshoot it?

58. Application is killed by Kubernetes with `OOMKilled`, but there is no Java `OutOfMemoryError`. How would you investigate?

59. JVM heap is only 60%, but the Kubernetes container has reached its memory limit. Where could the remaining memory be going?

60. Increasing `-Xmx` makes your Kubernetes pod get killed more frequently. Why could this happen?

61. Application throws OOM only when processing large files. How would you troubleshoot?

62. Application throws OOM only for one API endpoint. How would you identify the problem?

63. Application throws OOM when fetching millions of database records. How would you fix the code?

64. Application throws OOM when returning a large JSON response. How would you redesign the API?

65. Application throws OOM during large file upload. How would you redesign file processing?

---

# 🔥 Heap / Native Memory Scenarios

66. JVM heap usage is normal, but the Java process RSS keeps increasing. What would you investigate?

67. Kubernetes memory usage is increasing, but JVM heap metrics look normal. What could be consuming memory?

68. Your application uses Netty and process memory keeps increasing. What would you investigate?

69. Your application uses large direct buffers and eventually throws a memory error. How would you troubleshoot?

70. Your application creates thousands of threads and eventually cannot create another thread. What would you investigate?

71. Increasing thread count improves throughput initially but eventually causes memory problems. Why?

72. Heap is stable but native memory keeps increasing. How would you determine what is consuming it?

73. Application uses JNI/native libraries and process memory keeps growing. How would you investigate?

---

# 🔥 Thread / Stack Memory Scenarios

74. Your application throws `StackOverflowError`. What would you investigate?

75. A recursive method works with small input but throws `StackOverflowError` with large input. How would you fix it?

76. Your application creates thousands of threads and crashes with native-memory exhaustion. How would you fix it?

77. Thread count continuously increases in production. How would you determine whether threads are leaking?

78. Your thread pool has an unbounded queue and downstream service becomes slow. What could happen to JVM memory?

79. Your application creates thousands of `CompletableFuture` tasks during traffic spikes. Memory suddenly increases. How would you investigate?

80. A slow downstream service causes thousands of requests to wait. How can this eventually become a JVM memory problem?

81. Increasing the thread pool size appears to fix latency temporarily, but memory usage then increases dramatically. Why?

82. Your application has many blocked threads and high memory usage. How would you investigate the relationship?

---

# 🔥 Spring Boot + JVM Memory Scenarios

83. A Spring Boot application gradually consumes more memory after every API request.

84. `@Cacheable` was added and memory usage suddenly increased. How would you investigate?

85. A Spring Boot endpoint loads 5 million database records into a `List` and the application crashes. How would you fix it?

86. Hibernate loads thousands of entities into the persistence context during a batch operation. Memory grows continuously. How would you solve it?

87. A long-running `@Transactional` operation processes thousands of records and causes high memory usage. What would you investigate?

88. A Spring Boot application accepts a 2-GB file upload and crashes. How would you redesign the upload?

89. A REST endpoint returns millions of database records and causes OOM. How would you redesign it?

90. An asynchronous Spring Boot process creates tasks faster than workers can consume them. Memory continuously increases. How would you fix it?

91. An executor queue keeps growing because downstream processing is slow. What would you do?

92. Application memory increases after enabling detailed application logging. How would you investigate?

93. A Spring Boot application uses an in-memory cache with no size limit. What could happen under production traffic?

94. Application memory increases after enabling a new monitoring/observability library. How would you investigate?

---

# 🔥 Production Investigation Scenarios

95. You receive an alert: **Java heap usage > 90%**. What do you do first?

96. You receive an alert: **GC pause time suddenly increased**. What do you investigate?

97. You receive an alert: **Old Generation usage continuously increasing**. What would you check?

98. You receive an alert: **Container memory > 90%, but JVM heap is only 50%**. What would you investigate?

99. Application latency increased and CPU is normal, but memory is high. What would you investigate?

100. Application latency increased, CPU is high, and GC is high. How would you distinguish the root cause?

101. Application restarted automatically because of OOM. What information would you collect before making changes?

102. You deployed a new version and memory consumption doubled. How would you compare the old and new versions?

103. Memory usage is high only on one pod while other pods are normal. What would you investigate?

104. One Kubernetes pod repeatedly gets OOMKilled while other identical pods don't. How would you troubleshoot?

105. Application memory problem happens only under production traffic and cannot be reproduced locally. What approach would you take?

---

# 🏆 MASTER 15 — MUST KNOW

If the interviewer asks **any JVM/Memory scenario**, these 15 cover the most important patterns:

1. **Heap continuously grows → investigate memory leak**
2. **Full GC doesn't reduce Old Gen → retained objects/leak**
3. **Java heap space OOM → heap allocation/retention**
4. **Metaspace OOM → class metadata/class-loader issue**
5. **Native thread OOM → too many threads/native memory**
6. **Container OOMKilled + normal heap → native/off-heap/container memory**
7. **RSS grows + heap stable → investigate native/off-heap**
8. **High GC + high CPU → allocation/GC pressure**
9. **Slow API + long GC pauses → GC/heap investigation**
10. **Static collection grows → possible memory leak**
11. **Unbounded cache grows → possible memory leak**
12. **Large DB result causes OOM → pagination/streaming/batching**
13. **Large file causes OOM → streaming instead of loading into memory**
14. **Thousands of waiting requests → thread/queue/memory pressure**
15. **Can't reproduce locally → production metrics + heap dump/JFR/GC evidence**

### The interview answer pattern

For almost every one of these scenarios, think:

**Detect → Identify Heap/Native → Check GC → Check Memory Trend → Heap Dump/JFR/GC Logs → Find Retained Objects/Root Cause → Fix → Monitor**

That's the **scenario-only JVM/Memory bank**; I deliberately left out standalone questions like *"What is Heap?"*, *"What is Metaspace?"*, etc.


# JVM & MEMORY — ADDITIONAL SCENARIOS (Missing Topics)

## 🔥 CodeCache & JIT Scenarios

106. Application runs fine for a while but then throws `OutOfMemoryError: CodeCache` and JIT compilation stops. What would you investigate?

107. After this error, application throughput suddenly drops even though CPU and heap look normal. Why could this happen?

108. Your microservice has thousands of dynamically generated classes/lambdas. Over time, CodeCache usage keeps increasing. How would you fix it?

109. You increase `-XX:ReservedCodeCacheSize` and the problem goes away temporarily but returns after a few days. What would you investigate next?

---

## 🔥 Container / cgroup Ergonomics Scenarios

110. JVM heap defaults to a much smaller size than expected inside a Docker container, even though `-Xmx` isn't set. What would you check?

111. Container CPU limit is set to 1 core, but JVM is spawning many GC and JIT threads, causing throttling. How would you fix this?

112. Same Docker image behaves differently on two Kubernetes clusters — one runs fine, one OOMKills constantly. What environment difference would you check first?

113. You migrate from cgroup v1 to cgroup v2 nodes and JVM memory behavior changes. What would you investigate?

114. You set container memory limit to 1Gi and `-Xmx` to 900Mi, but the pod still gets OOMKilled. Where is the remaining memory going?

---

## 🔥 Reference Type Scenarios

115. You implement a cache using `WeakHashMap` expecting entries to be automatically cleared, but memory keeps growing anyway. What would you investigate?

116. Objects implementing `finalize()` are piling up and not getting collected promptly. What's happening and how would you fix it?

117. You use `SoftReference` for a cache expecting the JVM to clear it under memory pressure, but OOM still happens. Why could this occur?

118. A `PhantomReference`-based cleanup mechanism isn't running as expected — objects stay in memory longer than intended. How would you debug it?

---

## 🔥 Virtual Threads (Project Loom) Scenarios

119. After migrating from platform threads to virtual threads, thread count in thread dumps explodes into the hundreds of thousands. Is this a problem? How would you evaluate it?

120. Your application uses virtual threads with a `synchronized` block wrapping a blocking I/O call, and you see unexpected memory/CPU behavior. What would you investigate?

121. Memory usage per-request seems lower with virtual threads, but overall heap churn (allocation rate) increased. How would you explain and investigate this?

122. A library uses `ThreadLocal` heavily, and after switching to virtual threads, memory usage spikes. Why, and how would you fix it?

---

## 🔥 GC Log Analysis (Practical) Scenarios

123. Given a GC log showing Young GC pause times steadily increasing from 20ms to 200ms over a few hours, what would you look for in the log?

124. A GC log shows "Full GC" entries with very little memory reclaimed each time. What does this indicate and what's your next step?

125. You compare GC logs before and after a deployment — allocation rate has doubled. How would you trace this back to code changes?

126. GC logs show frequent "to-space exhausted" (G1) events. What do these mean and how would you address them?

---

## 🔥 JVM Tuning Trade-off Scenarios

127. You're tuning JVM for a payment processing service (low-latency, strict SLA) vs a nightly batch job (high-throughput, no latency constraint). How would your GC choice and heap sizing differ?

128. Increasing heap size reduces GC frequency but increases pause duration during Full GC. How would you decide the right heap size for a latency-sensitive service?

129. You're asked to reduce P99 latency caused by GC pauses without adding more memory. What tuning options would you explore?

130. Your team wants to migrate from Parallel GC to G1 or ZGC. What factors would you evaluate before recommending the switch?

---

# 🏆 UPDATED MASTER LIST — Add These 6 Patterns

16. **CodeCache OOM → too many dynamically generated/compiled methods**
17. **Container heap mismatch → cgroup detection/JVM ergonomics issue**
18. **WeakReference/SoftReference not clearing as expected → misunderstanding of reachability**
19. **Virtual threads + ThreadLocal/synchronized → hidden memory/pinning issues**
20. **GC log shows shrinking reclaim per Full GC → real leak, not tuning issue**
21. **Latency vs throughput GC trade-off → workload-driven GC/heap decision**

With these added, your bank now covers **130 scenarios** and closes the gaps for senior/staff-level rounds — especially CodeCache OOM and container/cgroup ergonomics, which come up a lot in current (2025-26) interviews given how common containerized Java deployments are now.
