Java 17 features and uses 
For a Java 17 enterprise project, interviewers usually don't ask only "What are the new features of Java 17?". They often ask where you used Java 17 in your project, why you chose a feature, what alternatives existed, and what benefits you got.

Here are 60 Java 17-focused interview questions with concise answers.


---

1. What is Java 17?

Answer: Java 17 is the 17th version of Java and an LTS (Long-Term Support) release. It provides performance improvements, security enhancements, new language features like Records, Sealed Classes, Pattern Matching, Text Blocks, and JVM improvements.


---

2. Why did your project use Java 17?

Answer:

Long-term support

Better JVM performance

Improved garbage collection

Reduced boilerplate with Records

Better readability using Text Blocks

More secure and stable than Java 8/11



---

3. Why not Java 21?

Answer: At project start, Java 17 was the company's approved LTS version. Enterprise applications typically upgrade only after sufficient testing and vendor support.


---

4. Which Java 17 features did you actually use?

Answer:

Records

Text Blocks

Pattern Matching for instanceof

Switch Expressions

Helpful NullPointerException messages

Improved G1 Garbage Collector



---

5. Which Java 17 feature did you use the most?

Answer: Records for DTOs because they reduce boilerplate and are immutable.


---

6. What are Records?

Answer: Records are immutable data carrier classes that automatically generate constructors, accessors, equals(), hashCode(), and toString().


---

7. Why use Records instead of normal classes?

Answer:

Less code

Immutable by default

Easy maintenance

Better readability

Ideal for DTOs



---

8. Where did you use Records?

Answer: For request and response DTOs, especially where data only needed to be transferred between layers.


---

9. Can Records have methods?

Answer: Yes. Records can contain custom methods, static methods, and validation in the compact constructor.


---

10. Are Records immutable?

Answer: Yes. Record components are final.


---

11. Can a Record extend another class?

Answer: No.


---

12. Can a Record implement interfaces?

Answer: Yes.


---

13. Can Records have constructors?

Answer: Yes, including compact constructors for validation.


---

14. Can Records be serialized?

Answer: Yes.


---

15. Are Records suitable for JPA entities?

Answer: No. JPA entities require mutable state and a no-argument constructor.


---

16. What are Sealed Classes?

Answer: Sealed classes restrict which classes can extend or implement them.


---

17. Why use Sealed Classes?

Answer: To control inheritance and improve maintainability.


---

18. Difference between final and sealed?

Answer:

final: no subclass allowed.

sealed: only specified subclasses allowed.



---

19. Which keyword is used with Sealed Classes?

Answer: permits


---

20. What are the permitted subclasses?

Answer: Classes explicitly listed after permits.


---

21. What are the three subclass modifiers?

Answer:

final

sealed

non-sealed



---

22. Did you use Sealed Classes in your project?

Answer: Yes, for modeling restricted business hierarchies (or state honestly if you did not).


---

23. What is Pattern Matching for instanceof?

Answer: It combines type checking and casting into one step.


---

24. Why is Pattern Matching better?

Answer: It removes explicit casting and makes code cleaner.


---

25. What was the old way?

Answer: Use instanceof followed by manual casting.


---

26. Did Pattern Matching improve performance?

Answer: Its main benefit is readability and reduced boilerplate rather than significant performance gains.


---

27. What are Text Blocks?

Answer: Multi-line string literals written using triple quotes.


---

28. Why use Text Blocks?

Answer: To write SQL, JSON, XML, or HTML more cleanly.


---

29. Where did you use Text Blocks?

Answer: For SQL queries and JSON templates.


---

30. What are Switch Expressions?

Answer: A modern form of switch that returns a value and uses ->.


---

31. Why are Switch Expressions better?

Answer: Less code and no accidental fall-through.


---

32. Difference between old and new switch?

Answer: Old switch uses case with break; new switch uses -> and can return values.


---

33. Did you use Switch Expressions?

Answer: Yes, for mapping status values and business logic.


---

34. What are Helpful NullPointerExceptions?

Answer: Java 17 shows which object reference was null, making debugging easier.


---

35. Why are Helpful NPEs useful?

Answer: They reduce debugging time by pointing to the exact null reference.


---

36. Which Garbage Collector did you use?

Answer: G1 Garbage Collector.


---

37. Why G1 GC?

Answer: It offers lower pause times and good performance for large applications.


---

38. What is the advantage of G1 over Parallel GC?

Answer: G1 prioritizes predictable pause times.


---

39. Did you tune the JVM?

Answer: We generally used the default G1 settings; JVM tuning was handled only when performance issues were observed.


---

40. What JVM improvements came with Java 17?

Answer: Better performance, improved memory management, and enhanced diagnostics.


---

41. Is Java 17 backward compatible?

Answer: Mostly yes, but removed or deprecated APIs may require code changes.


---

42. What migration challenges did you face?

Answer: Updating dependencies and ensuring all libraries supported Java 17.


---

43. Which Spring Boot version supports Java 17?

Answer: Spring Boot 3.x requires Java 17 or later.


---

44. Can Java 17 run Java 8 code?

Answer: In most cases yes, if it doesn't rely on removed internal APIs or incompatible libraries.


---

45. Did you use Streams differently in Java 17?

Answer: No major syntax changes; we continued using the Stream API introduced in Java 8.


---

46. Did Java 17 change Lambdas?

Answer: No.


---

47. Did Java 17 change Collections?

Answer: No significant changes.


---

48. Did Java 17 change Multithreading?

Answer: No major language changes; virtual threads were introduced later in Java 21.


---

49. What are the biggest advantages of Java 17?

Answer:

LTS

Better performance

Better security

Records

Sealed Classes

Pattern Matching

Text Blocks



---

50. What are the limitations of Records?

Answer: They cannot extend other classes, are immutable, and are not suitable for JPA entities.


---

51. Why are Records immutable?

Answer: Immutability improves thread safety and prevents accidental modification.


---

52. Which Java 17 feature reduced the most code?

Answer: Records.


---

53. Which feature improved readability?

Answer: Text Blocks and Pattern Matching.


---

54. Which feature improved maintainability?

Answer: Sealed Classes.


---

55. Which feature helped debugging?

Answer: Helpful NullPointerException messages.


---

56. Which feature would you recommend for every project?

Answer: Records for DTOs and Text Blocks for multi-line SQL or JSON.


---

57. Which Java 17 feature did your team adopt first?

Answer: Records, because they were easy to introduce without affecting business logic.


---

58. If your interviewer asks, "What Java 17 feature gave the biggest business benefit?" what would you say?

Answer: Records reduced boilerplate code, improved code readability, and made DTOs immutable, which reduced maintenance effort.


---

59. If your interviewer asks, "Did Java 17 improve your application's performance?" what would you say?

Answer: Yes, we observed better JVM efficiency and stability. However, application performance depends on many factors such as database queries, caching, and application design, not just the Java version.


---

60. Which Java 17 features did you not use?

Answer: If true for your project: "We mainly used Records, Text Blocks, Pattern Matching, and Switch Expressions. We did not use every Java 17 feature because we only adopt features that provide clear value to our codebase."

These questions are among the most common Java 17 interview topics for developers with around 3 years of experience, especially when interviewing for Spring Boot backend roles.



# Java 17 — Interview Q&A (80+ Questions)

## SECTION 1: Why Java 17 / LTS Basics (Most Asked)

**Q1. Why did your project use Java 17, not 8 or 11?**
A: Java 17 is LTS (Long Term Support), released Sep 2021. Companies skip non-LTS versions (9,10,12-16) kyunki unka support 6 months me khatam ho jata hai. LTS releases (8, 11, 17, 21) get extended support (Oracle: 8 years). Java 17 was the first LTS after 11 with 4+ years of new features bundled — records, sealed classes, pattern matching, better performance, stronger security.

**Q2. What is LTS? Difference between LTS and non-LTS?**
A: LTS = Long Term Support version, patched/updated for years (security patches, bug fixes). Non-LTS releases every 6 months, support ends when next version drops. Enterprises never use non-LTS in production — instability + no long-term patch risk.

**Q3. Java 8 vs Java 11 vs Java 17 — what changed for enterprise use?**
| Version | Key Enterprise Changes |
|---|---|
| Java 8 | Lambdas, Streams, Optional, Date-Time API |
| Java 11 | var (local var type inference), new HTTP Client, removed Java EE modules |
| Java 17 | Records, Sealed classes, Pattern Matching, Text Blocks, strong encapsulation of internals, better GC (ZGC/G1 improvements) |

**Q4. Why did Spring Boot 3.x mandate Java 17?**
A: Spring Boot 3 needs Jakarta EE 9+ (jakarta.* namespace) and leverages Java 17 features internally (records for DTOs, pattern matching) plus GraalVM native image support which needs Java 17 baseline.

**Q5. Is Java 17 backward compatible with Java 8 code?**
A: Mostly yes for standard code, but internal JDK APIs (sun.misc.*, reflection into java.lang internals) are strongly encapsulated in 17 — old code using such hacks (common in libraries like older Lombok, Mockito versions) breaks. Migration needs `--add-opens` JVM flags sometimes.

---

## SECTION 2: Records

**Q6. What is a Record in Java 17?**
A: Immutable data carrier class — compiler auto-generates constructor, getters, equals(), hashCode(), toString(). Replaces boilerplate POJO/DTO code.
```java
public record UserDTO(String name, String email, int age) {}
```

**Q7. Why use records instead of Lombok @Data?**
A: Records are native to JVM (no annotation processor dependency, no build-time bytecode generation risk), immutable by design, and give compile-time safety. Lombok is external, adds build complexity, sometimes IDE issues.

**Q8. Can a record extend a class?**
A: No — records implicitly extend `java.lang.Record`, and Java doesn't allow multiple inheritance of state. But records CAN implement interfaces.

**Q9. Can a record have additional methods?**
A: Yes — you can add custom methods, static methods, and compact constructors for validation.
```java
public record UserDTO(String name, String email) {
    public UserDTO {
        if (email == null || !email.contains("@")) 
            throw new IllegalArgumentException("Invalid email");
    }
}
```

**Q10. Are record fields mutable?**
A: No — all fields are `private final` implicitly. Immutability by default.

**Q11. Can you use records as Entity classes in JPA/Hibernate?**
A: No — JPA entities need a no-arg constructor and mutable fields for lazy loading/proxying. Records used for DTOs, API request/response bodies, not persistence entities.

**Q12. Where did you use records in your project (StayHub example)?**
A: Used for API response DTOs (e.g., `BookingResponse`, `RoomAvailabilityDTO`) and for Kafka event payloads — immutability ensures event data isn't accidentally mutated during processing pipeline.

**Q13. Does record support equals/hashCode customization?**
A: You can override them manually, but then you lose the auto-generated ones for those methods (compiler won't generate if you define your own).

---

## SECTION 3: Sealed Classes/Interfaces

**Q14. What is a sealed class?**
A: Restricts which classes can extend/implement it. Gives controlled inheritance — you explicitly declare permitted subclasses.
```java
public sealed interface PaymentMethod permits CreditCard, UPI, NetBanking {}
```

**Q15. Why use sealed classes over normal inheritance?**
A: Enables exhaustive pattern matching (compiler knows all subtypes), better domain modeling (like enums but with data), prevents unauthorized extension by external code — improves API design safety.

**Q16. What are the rules for sealed class subclasses?**
A: Each permitted subclass must be declared `final`, `sealed`, or `non-sealed`.

**Q17. Real use case in enterprise project?**
A: Used for modeling booking status transitions or payment types — e.g., `sealed interface BookingEvent permits BookingCreated, BookingCancelled, BookingModified` — combined with switch pattern matching for exhaustive event handling in Kafka consumers.

**Q18. Sealed vs Enum — when to use which?**
A: Enum = fixed set of constants, no state variation. Sealed classes = fixed set of types but each can carry different data/behavior. Use sealed when subtypes need different fields.

---

## SECTION 4: Pattern Matching

**Q19. What is Pattern Matching for instanceof (Java 16, stable in 17)?**
A: Eliminates explicit casting after instanceof check.
```java
// Old
if (obj instanceof String) { String s = (String) obj; }
// Java 17
if (obj instanceof String s) { System.out.println(s.length()); }
```

**Q20. Why is this useful in enterprise code?**
A: Reduces boilerplate, avoids ClassCastException risk, cleaner exception handling / visitor-pattern-like logic when parsing polymorphic API payloads.

**Q21. What is Switch Pattern Matching (preview in 17)?**
A: Allows pattern matching directly in switch statements (finalized in later versions, preview in 17):
```java
static String describe(Object obj) {
    return switch (obj) {
        case Integer i -> "Int: " + i;
        case String s -> "String: " + s;
        default -> "Unknown";
    };
}
```

**Q22. Is switch pattern matching production-ready in Java 17?**
A: It's a preview feature in 17 (needs `--enable-preview` flag) — finalized fully in Java 21. Interviewers often test if you know this distinction.

---

## SECTION 5: Text Blocks

**Q23. What are Text Blocks (finalized Java 15, used heavily in 17 projects)?**
A: Multi-line string literals using `"""`, avoiding escape-character clutter.
```java
String json = """
    {
        "name": "%s",
        "role": "%s"
    }
    """.formatted(name, role);
```

**Q24. Where did you use text blocks?**
A: SQL queries in JdbcTemplate/native queries, JSON payload mocking in tests, multi-line log messages, and formatting prompt templates for LangChain4j/OpenAI API calls in the AI chatbot feature.

**Q25. Any gotchas with text blocks?**
A: Trailing whitespace and indentation matter — compiler strips "incidental" whitespace based on the closing `"""` position. Common interview trick question.

---

## SECTION 6: JVM / Performance / GC

**Q26. What GC improvements came in Java 17?**
A: G1 GC (default) improved further; ZGC and Shenandoah GC became more stable (still not default) — very low pause-time garbage collectors suited for large heap microservices.

**Q27. Why does GC choice matter for microservices?**
A: Low-latency APIs (e.g., booking search) need low pause times — ZGC/G1 minimize stop-the-world pauses vs old CMS (removed in 14).

**Q28. Is CMS GC available in Java 17?**
A: No — CMS was removed in Java 14. G1 is default; ZGC/Shenandoah are production-ready alternatives for large heaps.

**Q29. What is strong encapsulation of JDK internals (JEP 403)?**
A: Java 17 blocks illegal reflective access to internal JDK APIs (`sun.misc.Unsafe` etc.) by default — no more `--illegal-access=permit` fallback. Improves security, breaks old libraries reflecting into JDK internals.

**Q30. How does this affect legacy library upgrades?**
A: Old Lombok/Mockito/ByteBuddy versions relying on reflection hacks fail on 17 — must upgrade to versions JDK-17 compatible, or add `--add-opens` flags as a stopgap.

---

## SECTION 7: Other Key JEPs / Features

**Q31. What is Enhanced NullPointerException (Java 14+, used in 17)?**
A: JVM shows exactly which variable was null in the stack trace instead of generic NPE — huge debugging time saver in production logs.
```
Cannot invoke "String.length()" because "user.name" is null
```

**Q32. What is the Foreign Function & Memory API (incubator in 17)?**
A: Lets Java call native code (C libraries) without JNI — safer, easier native interop. Still incubator/preview in 17, finalized later.

**Q33. What happened to the Applet API in Java 17?**
A: Deprecated for removal (JEP 398) — Applets are legacy/unused technology, irrelevant to modern web apps.

**Q34. What is deprecation of Security Manager (JEP 411)?**
A: Security Manager marked deprecated for removal — modern apps use container-level security (Docker, K8s, cloud IAM) instead of JVM-level sandboxing.

**Q35. What's new with random number generators (JEP 356)?**
A: New `RandomGenerator` interface with pluggable algorithms (`Xoshiro256PlusPlus` etc.) — better statistical quality, more algorithm choices than old `java.util.Random`.

**Q36. Does Java 17 support macOS/AArch64 (Apple Silicon)?**
A: Yes (JEP 391) — native macOS/AArch64 port added, relevant if your dev team uses M1/M2 Macs.

---

## SECTION 8: var, Local Type Inference (Java 11 carryover, still asked)

**Q37. What is `var` and is it dynamic typing?**
A: `var` is local variable type inference — type still determined at compile time by compiler, NOT dynamic typing like JS/Python. `var list = new ArrayList<String>();` → compiler infers `ArrayList<String>`.

**Q38. When should you avoid `var`?**
A: When it hurts readability — e.g., `var x = getData();` doesn't tell reader the type. Best for obvious right-hand-side types (constructors, builders).

---

## SECTION 9: Migration & Compatibility (Heavily Asked in Enterprise Interviews)

**Q39. What challenges did you face migrating from Java 8/11 to 17?**
A: Common real answers to prepare:
- Deprecated/removed APIs (`javax.xml.bind` removed — needed explicit JAXB dependency)
- Reflection-based libraries breaking due to strong encapsulation
- Third-party dependency version bumps (Lombok, Mockito, Hibernate)
- Module system (JPMS) conflicts if using `module-info.java`

**Q40. What is JPMS (Java Platform Module System)? Did you use it?**
A: Introduced in Java 9, allows explicit module boundaries (`module-info.java`). Most enterprise apps stay on classpath (unnamed module) rather than adopting full JPMS — mention this honestly if asked, interviewers respect practical answers.

**Q41. How do you handle `--add-opens` in production?**
A: Add JVM flags in startup scripts / Dockerfile / deployment YAML when legacy reflection-based code needs internal access, e.g.:
```
--add-opens java.base/java.lang=ALL-UNNAMED
```

**Q42. Is Java 17 faster than Java 8?**
A: Yes — improved JIT compilation, better default GC, reduced memory footprint, faster startup (helps K8s pod scaling/cold starts).

---

## SECTION 10: Rapid-Fire (One-Liners, Common in Screening Rounds)

| # | Question | Answer |
|---|---|---|
| 43 | Java 17 release date? | Sept 2021 |
| 44 | Is Java 17 free for commercial use? | Oracle JDK 17 free under NFTC license until next LTS+1 year; OpenJDK always free |
| 45 | Default GC in Java 17? | G1 GC |
| 46 | Records replace which pattern? | Boilerplate POJO/DTO/Value Object pattern |
| 47 | Sealed class keyword? | `sealed`, `permits`, `non-sealed` |
| 48 | Pattern matching removes need for? | Explicit casting after instanceof |
| 49 | Text block delimiter? | `"""` (triple quote) |
| 50 | Java 17 successor LTS? | Java 21 |
| 51 | Can interfaces be sealed? | Yes |
| 52 | Can records implement interfaces? | Yes |
| 53 | Can records be generic? | Yes — `record Pair<T>(T first, T second) {}` |
| 54 | Is `var` allowed for instance fields? | No, only local variables |
| 55 | Which JEP added strong encapsulation? | JEP 403 |
| 56 | NPE improvement JEP? | JEP 358 (Helpful NPEs) |
| 57 | Deprecated for removal in 17? | Security Manager, Applet API |
| 58 | Can you compile Java 8 code on JDK 17? | Yes generally, with source/target flags, minus removed APIs |
| 59 | Is `javax.xml.bind` (JAXB) bundled in 17? | No — removed since Java 11, needs external dependency |
| 60 | Spring Boot version needing Java 17 minimum? | Spring Boot 3.x |

---

## SECTION 11: Scenario / Project-Specific Questions (StayHub Relevant)

**Q61. In your microservices, where exactly did Java 17 features add value?**
A: Records for immutable Kafka event DTOs (prevents accidental mutation mid-pipeline) + sealed interfaces for booking state machine (compiler-enforced exhaustive handling of all booking statuses) + text blocks for readable native SQL/JSON in the AI chatbot prompt construction.

**Q62. Did upgrading to Java 17 cause any production issue? How did you resolve it?**
A: Good honest answer pattern: "Faced reflection access warnings/errors from an older library (e.g. Mockito/Lombok version), resolved by upgrading dependency versions and adding targeted `--add-opens` flags in Docker entrypoint, tested thoroughly in staging before prod rollout."

**Q63. How do records interact with Jackson (JSON serialization) in Spring Boot?**
A: Jackson 2.12+ supports records natively (via `ParameterNamesModule` or built-in from 2.12+) — auto-maps JSON fields to record canonical constructor, works out-of-box in Spring Boot 3 for REST DTOs.

**Q64. Can you use Lombok with records?**
A: Records already generate what Lombok's @Data would — using both is redundant/conflicting. If validation needed, use compact constructor instead of Lombok.

**Q65. How does pattern matching help in your Kafka consumer logic?**
A: When consuming polymorphic events (sealed hierarchy of event types), switch-based pattern matching lets you handle each event type exhaustively and safely — compiler warns if a new event type isn't handled.

---

## SECTION 12: Comparison Table Questions (Interviewer Favorite)

**Q66. Record vs Class — full comparison**

| Aspect | Record | Regular Class |
|---|---|---|
| Mutability | Immutable | Mutable (unless coded otherwise) |
| Boilerplate | Auto-generated | Manual/Lombok |
| Inheritance | Cannot extend classes | Can extend |
| Use case | DTO, Value Object | Entity, business logic |

**Q67. Sealed Class vs Abstract Class**

| Aspect | Sealed Class | Abstract Class |
|---|---|---|
| Subclass control | Explicit permits list | Anyone can extend |
| Exhaustiveness | Compiler-checked in switch | Not enforced |
| Use case | Fixed domain hierarchy | Open extension point |

**Q68. Java 8 Streams vs Java 17 — anything new in Streams?**
A: No major Streams API overhaul in 17 itself, but `toList()` convenience method added in Java 16 (`stream.toList()` instead of `.collect(Collectors.toList())`) — commonly used now.

---

## SECTION 13: Tricky / Conceptual Traps

**Q69. Can a sealed class have subclasses in a different package?**
A: Only if they're in the same module, OR explicitly use `permits` with fully qualified names across packages within the same module.

**Q70. Do records support inheritance among themselves?**
A: No — records are implicitly final, cannot be extended by other records or classes.

**Q71. What happens if you don't list all permitted subclasses' modifiers correctly?**
A: Compilation error — every direct subclass of a sealed class MUST be `final`, `sealed`, or `non-sealed`, no exceptions.

**Q72. Is pattern matching in instanceof scoped conditionally?**
A: Yes — the pattern variable's scope follows flow analysis:
```java
if (obj instanceof String s && s.length() > 5) { ... } // valid, s is in scope
```

**Q73. Why can't records have instance field declarations outside the header?**
A: Records are meant to be transparent, immutable carriers — all state must be declared in the record header (canonical components) for equals/hashCode/toString consistency.

**Q74. Is Java 17 fully free like older JDK 8 (before Oracle's licensing change)?**
A: Oracle JDK 17 is free for production use under NFTC (No-Fee Terms and Conditions) — different from JDK 11's restrictive Oracle licensing that pushed many enterprises to OpenJDK/Amazon Corretto/Temurin.

**Q75. What JDK distribution do enterprises commonly use for 17?**
A: Amazon Corretto, Eclipse Temurin (AdoptOpenJDK successor), Azul Zulu — free, production-supported alternatives to Oracle JDK.

---

## Quick Revision Summary (30-second answer if asked "Java 17 me kya naya hai?")
1. **Records** — immutable DTOs, no boilerplate
2. **Sealed classes** — controlled inheritance hierarchies
3. **Pattern matching (instanceof)** — no explicit casting
4. **Text blocks** — multi-line strings
5. **Strong encapsulation** — JDK internals locked down
6. **Helpful NPEs** — precise null-cause in stack trace
7. **LTS** — long-term support, industry standard for enterprise apps
8. **Foundation for Spring Boot 3 / Jakarta EE**


