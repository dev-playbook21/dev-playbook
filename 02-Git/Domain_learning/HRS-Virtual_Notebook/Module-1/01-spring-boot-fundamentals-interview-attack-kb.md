# Module 1 — Spring Boot Fundamentals
## High-Probability Interview Attack KB
### Deep interviewer-oriented companion to `01-spring-boot-fundamentals-interview-kb.md`

**Purpose:** This file is NOT a second theory notebook.  
It is an **interview attack map**: the questions most likely to be used to test whether a candidate actually understands Spring/Spring Boot rather than only knowing annotations.

**Module status:** Interview Attack KB — CREATED  
**Use with:** `01-spring-boot-fundamentals-interview-kb.md`

---

# 0. How This Attack KB Was Built

There is no public, reliable dataset containing the exact frequency of every question asked by every interviewer. Therefore, no fake percentages are used here.

Instead, the priority model is based on:

1. Recurrence across current Spring/Spring Boot interview-prep material.
2. Questions repeatedly appearing in experienced-developer interview discussions.
3. Official Spring/Spring Boot mechanisms that naturally generate follow-up questions.
4. The user's actual Hotel Review System implementation.
5. Questions that expose the difference between:
   - memorized annotation definitions
   - working knowledge
   - mechanism-level understanding
   - production/debugging experience.

Current interview sources repeatedly emphasize IoC/DI, beans, lifecycle, scopes, component scanning, `@Configuration`/`@Bean`, auto-configuration, starters, configuration, and AOP. Current material also increasingly probes **how the mechanism works and what breaks**, rather than accepting one-line annotation definitions.

Official Spring documentation confirms the underlying mechanisms: DI is a form of IoC; `ApplicationContext` manages beans and dependencies; Boot auto-configuration is conditional; profiles segregate configuration; and externalized configuration has defined property-source precedence. Spring AOP is proxy/interception-oriented and commonly underlies framework features. citeturn0search2turn0search14turn0search1turn0search6turn0search0turn0search3

---

# 1. Interviewer Probability Model

Use these labels as **priority bands**, not statistical probabilities.

## 🔴 P0 — Must-answer cold

Questions that are extremely common foundations or natural opening questions.

If you cannot defend these, the interviewer can immediately conclude that your Spring knowledge is mostly usage-level.

```text
Spring vs Spring Boot
IoC vs DI
What is a Spring Bean?
What is ApplicationContext?
How does dependency injection work?
Why constructor injection?
@Component vs @Service vs @Repository vs @Controller
What is component scanning?
What does @SpringBootApplication do?
What is auto-configuration?
What are starters?
What are profiles?
How does externalized configuration work?
@Value vs @ConfigurationProperties
What is Actuator?
```

## 🟠 P1 — Very high-value follow-ups

Usually appear when the interviewer wants to move from vocabulary to mechanism.

```text
Bean lifecycle
Bean scopes
Singleton vs prototype
@Primary vs @Qualifier
@Configuration vs @Bean
How auto-configuration actually decides
What happens when you define your own bean?
How property precedence works
What happens when a dependency is missing?
How Config Server fits into configuration
How Spring discovers a bean
```

## 🟡 P2 — Depth probes

These are commonly used to distinguish someone who has built Spring applications from someone who only followed tutorials.

```text
BeanPostProcessor
Aware callbacks
Proxy creation
AOP relationship with Spring
Why self-invocation matters
Prototype injected into singleton
@Configuration proxyBeanMethods
Conditional auto-configuration
Debugging auto-configuration
Configuration failure modes
Config Server startup behavior
```

## 🔵 P3 — Specialist / advanced branches

Know the concepts, but do not let these consume disproportionate preparation time before the higher-priority areas are solid.

```text
Custom auto-configuration
Custom starter
AOT processing
FactoryBean
custom BeanFactoryPostProcessor
advanced scope/proxy behavior
deep ApplicationContext internals
```

---

# 2. The Interviewer Mindset

A typical weak answer:

> "`@Autowired` injects dependencies."

A stronger answer:

> "Spring's IoC container resolves the dependency and supplies it to the bean. With constructor injection, the dependency is available when the object is created, making the required dependency explicit and the object easier to construct and test."

The interviewer then asks:

```text
Who resolves it?
How is the bean discovered?
What if there are two candidates?
When does this happen?
What if the dependency is missing?
What scope does the bean have?
Is the injected object a proxy?
```

This is the **attack pattern** to prepare for:

```text
Definition
   ↓
Mechanism
   ↓
Lifecycle
   ↓
Failure
   ↓
Trade-off
   ↓
Project example
   ↓
Production implication
```

Do not memorize isolated answers. Prepare the chain.

---

# 3. ATTACK #1 — Spring vs Spring Boot

## Q1. What is Spring?

### Minimum answer

Spring is a Java application framework/ecosystem centered around capabilities such as IoC/DI, AOP, web development, data access, transactions, and integration.

### Follow-up

**Q: Then what does Spring Boot add?**

Boot provides conventions and conveniences around Spring applications, including auto-configuration, starters, externalized configuration support, and convenient application startup.

### Follow-up

**Q: Does Spring Boot replace Spring?**

No.

```text
Spring
  ↓
Core framework/ecosystem

Spring Boot
  ↓
Opinionated application setup + runtime convenience
```

### Attack

**Q: Can you use Spring without Spring Boot?**

Yes. Spring Framework can be configured and run without Spring Boot, although Boot significantly reduces configuration and setup work.

### Project mapping

Your Hotel Review System services are Spring Boot applications, but the underlying concepts being used—IoC, DI, AOP, MVC, etc.—belong to the Spring ecosystem.

### Trap

❌ "Spring is old and Boot is the newer replacement."

Correct distinction:

> Boot builds on Spring; it is not a replacement for the framework.

---

# 4. ATTACK #2 — IoC vs DI

## Q2. What is IoC?

Inversion of Control means control over object creation/configuration is moved from application code toward a container/framework.

## Q3. What is DI?

Dependency Injection is a concrete mechanism/pattern through which dependencies are supplied to an object.

Official Spring documentation explicitly describes DI as a specialized form of IoC. citeturn0search2

### Strong interview answer

> "IoC is the broader principle: the application does not control all dependency creation itself. DI is one way Spring implements that principle by supplying required dependencies to managed objects."

### Attack

**Q: Show the difference without Spring.**

```java
HotelRepository repository = new HotelRepository();
HotelService service = new HotelService(repository);
```

The application code controls construction.

With Spring:

```java
@Service
class HotelService {
    private final HotelRepository repository;

    HotelService(HotelRepository repository) {
        this.repository = repository;
    }
}
```

Spring resolves and supplies the dependency.

### Attack

**Q: Why is this better?**

Do not answer only "loose coupling."

Explain:

```text
Dependency creation
        ↓
moved outside business class
        ↓
implementation can be substituted
        ↓
testing becomes easier
        ↓
configuration/composition becomes centralized
```

### Deeper attack

**Q: Is DI only useful for testing?**

No.

Testing is one benefit. Other benefits include composition, separation of concerns, substitutability, centralized configuration, and lifecycle management.

---

# 5. ATTACK #3 — What Is a Bean?

## Q4. What is a Spring Bean?

A bean is an object instantiated, configured, and managed by the Spring IoC container.

Official Spring documentation defines beans in this container-managed sense. citeturn0search2

### Attack

**Q: Is every Java object a Spring Bean?**

No.

```text
new Hotel()
```

creates an ordinary Java object.

A Spring-managed object is a bean when the container manages its definition/lifecycle.

### Attack

**Q: Who creates the bean?**

The IoC container.

### Attack

**Q: What does "managed" mean?**

Depending on configuration, Spring can manage:
- instantiation
- dependency injection
- lifecycle callbacks
- post-processing
- scopes
- proxying/interception
- destruction callbacks

### Trap

❌ "`@Service` creates the bean."

Better:

> `@Service` marks a class as a component candidate; the Spring container ultimately creates and manages the bean.

---

# 6. ATTACK #4 — ApplicationContext

## Q5. What is ApplicationContext?

`ApplicationContext` is Spring's central IoC container abstraction used to instantiate, configure, and assemble beans.

It extends `BeanFactory` and adds application-oriented capabilities such as AOP integration, event publication, and message-resource handling. citeturn0search2turn0search14

### Attack

**Q: BeanFactory vs ApplicationContext?**

Safe interview answer:

```text
BeanFactory
→ fundamental IoC container functionality

ApplicationContext
→ BeanFactory capabilities
+ richer application features
```

Do not claim that BeanFactory and ApplicationContext are completely unrelated containers.

### Attack

**Q: Which one do normal Spring Boot applications use?**

ApplicationContext.

---

# 7. ATTACK #5 — Dependency Injection Types

## Q6. What are the types of dependency injection?

Common forms:

```text
Constructor injection
Setter injection
Field injection
```

## Q7. Which do you prefer?

Constructor injection for required dependencies.

### Why?

```text
Explicit dependency
+ possible final field
+ valid construction
+ easier unit testing
+ fail-fast startup
```

### Attack

**Q: Is field injection invalid?**

No. It works, but it hides dependencies and makes ordinary Java construction/testing less convenient.

### Attack

**Q: When can setter injection make sense?**

For genuinely optional or changeable dependencies.

### Attack

**Q: Why not constructor injection with 15 dependencies?**

This is a design smell.

Do not solve an overly coupled class by blindly changing injection style.

Instead investigate whether the class has too many responsibilities/dependencies.

---

# 8. ATTACK #6 — @Component / @Service / @Repository / @Controller

## Q8. Difference?

They are stereotype annotations used for component discovery and semantic layering.

```text
@Component
├── @Service
├── @Repository
└── @Controller
```

### `@Component`

Generic component.

### `@Service`

Service-layer semantic stereotype.

### `@Repository`

Persistence-layer semantic stereotype; Spring also associates repository components with persistence exception translation.

### `@Controller`

MVC controller stereotype.

### Attack

**Q: Is @Service technically required for Spring to create the bean?**

Not strictly.

A generic `@Component` can also be discovered.

The advantage is semantic clarity and conventional architecture.

### Attack

**Q: What does @Repository add?**

One important framework behavior is persistence exception translation.

Do not say "Repository is only naming."

---

# 9. ATTACK #7 — Component Scanning

## Q9. How does Spring find your `@Service`?

At a high level:

```text
Application startup
      ↓
Component scanning
      ↓
Candidate classes discovered
      ↓
Bean definitions registered
      ↓
Container creates/manages beans
```

### Attack

**Q: What determines where scanning occurs?**

The component-scan configuration associated with the application.

`@SpringBootApplication` includes component scanning behavior.

### Attack

**Q: Why does moving a package sometimes break the application?**

If the class moves outside the relevant scan boundary, Spring may no longer discover it automatically.

Then:

```text
Controller
   ↓
required Service
   ↓
NoSuchBeanDefinition / UnsatisfiedDependency
```

### Attack

**Q: How would you diagnose it?**

Check:
1. package structure
2. component annotation
3. scan configuration
4. active configuration/profile
5. duplicate/multiple bean candidates
6. startup error

---

# 10. ATTACK #8 — @SpringBootApplication

## Q10. What does @SpringBootApplication do?

High-level answer:

It is a convenience annotation combining:

```text
@Configuration
@EnableAutoConfiguration
@ComponentScan
```

### Attack

**Q: Which one scans your components?**

`@ComponentScan`.

**Q: Which one enables Boot auto-configuration?**

`@EnableAutoConfiguration`.

**Q: Which one makes the class a configuration source?**

`@Configuration`.

### Trap

Do not claim that `@SpringBootApplication` itself is a completely new independent mechanism unrelated to these annotations.

---

# 11. ATTACK #9 — @Configuration vs @Bean

## Q11. Why use @Bean if we already have @Component?

Because sometimes you need explicit control over object construction, especially for classes you do not own.

Example:

```java
@Configuration
class AppConfig {

    @Bean
    SomeThirdPartyClient client() {
        return new SomeThirdPartyClient(...);
    }
}
```

### Attack

**Q: Can I annotate a third-party class with @Component?**

Not if you do not control the source.

Use a configuration class with `@Bean`.

### Attack

**Q: What does @Configuration mean?**

It identifies a source of bean definitions.

### Deep attack

**Q: Are calls between @Bean methods ordinary Java calls?**

This depends on how the configuration class is processed/proxied.

Do not give an absolute "always intercepted" answer without discussing configuration proxy semantics.

For interview depth, know that full `@Configuration` classes have special inter-bean method semantics; lightweight configuration patterns such as `proxyBeanMethods=false` change that behavior.

---

# 12. ATTACK #10 — Bean Lifecycle

## Q12. Explain Spring Bean lifecycle.

A useful simplified model:

```text
Bean definition
   ↓
Instantiation
   ↓
Dependency injection
   ↓
Aware callbacks / post-processing
   ↓
Initialization callbacks
   ↓
Bean ready for use
   ↓
Context shutdown
   ↓
Destruction callbacks
```

### Common initialization callbacks

```text
@PostConstruct
InitializingBean.afterPropertiesSet()
custom init method
```

Do not memorize this as a simplistic fixed list without understanding that post-processors and infrastructure can intervene.

### Attack

**Q: Is @PostConstruct called immediately after constructor?**

No.

Dependencies and container processing occur around bean construction/initialization.

### Attack

**Q: What is BeanPostProcessor?**

An extension mechanism allowing Spring to process bean instances before and/or after initialization.

It is important because Spring infrastructure can use post-processing to add behavior such as proxying.

### Attack

**Q: Why do proxies matter?**

Because many framework features are implemented around intercepted method calls.

Examples include:
- transactions
- caching
- security-related interception
- custom AOP

Spring AOP is specifically designed around cross-cutting concerns and method execution interception. citeturn0search3turn0search13

---

# 13. ATTACK #11 — Bean Scopes

## Q13. What is the default Spring bean scope?

Singleton.

Important:

> Spring singleton means one bean instance per ApplicationContext.

It does NOT mean one object globally across the JVM or application universe.

### Prototype

A new instance is created when requested from the container.

### Web scopes

Examples:
- request
- session

where the relevant web context exists.

### Attack

**Q: Singleton vs GoF Singleton?**

GoF Singleton controls object creation at the class/design level.

Spring singleton is a container-level scope.

---

# 14. ATTACK #12 — Prototype Injected into Singleton

This is a strong depth question.

Suppose:

```text
Singleton A
   ↓
Prototype B
```

If B is injected normally into A, A does not magically receive a new B every time A's method executes.

Why?

```text
Singleton A
→ created once
→ dependency injected during its creation
→ that injected reference remains
```

Spring documentation explicitly calls out the lifecycle mismatch problem between singleton and non-singleton beans. citeturn0search15

### Possible solutions

Depending on design:
- `ObjectProvider`
- scoped proxy
- method injection
- lookup mechanisms

Do not memorize the solution before understanding the lifecycle mismatch.

---

# 15. ATTACK #13 — Auto-Configuration

## Q14. What is Spring Boot auto-configuration?

Spring Boot attempts to configure application infrastructure automatically based on classpath dependencies and conditions.

Official Boot documentation describes it as automatic configuration based on added jar dependencies, and emphasizes that it is non-invasive and can back off when user configuration exists. citeturn0search1

### Mental model

```text
Classpath
   +
Environment
   +
Conditional rules
   ↓
Auto-configuration candidates
   ↓
Matching configuration
   ↓
Beans/infrastructure
```

### Attack

**Q: Is auto-configuration unconditional?**

No.

It is conditional.

### Attack

**Q: What kind of conditions?**

Examples:

```text
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnProperty
```

### Attack

**Q: What happens if I define my own DataSource?**

Relevant auto-configuration can back off when its conditions are no longer satisfied, such as `@ConditionalOnMissingBean`.

Official Boot documentation explicitly uses this as the model for non-invasive auto-configuration. citeturn0search1

---

# 16. ATTACK #14 — How Auto-Configuration Is Discovered

## Q15. Where does modern Spring Boot find auto-configuration candidates?

For current Spring Boot generations, auto-configuration candidates are listed through:

```text
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
```

The official Boot documentation describes this mechanism for current auto-configuration. citeturn0search5

### Critical interview trap

Older tutorials may answer:

```text
spring.factories
```

That is historical terminology for older Boot versions.

For modern Boot, know `AutoConfiguration.imports`.

### Attack

**Q: How do you debug why auto-configuration did or did not apply?**

Use Boot's debugging/diagnostic facilities, including the condition evaluation report exposed by debug startup/Actuator tooling when configured.

---

# 17. ATTACK #15 — Starter vs Auto-Configuration

## Q16. What is a starter?

A starter is a convenient dependency descriptor/bundle that brings a typical set of dependencies onto the classpath.

### Important chain

```text
Starter
   ↓
Dependencies
   ↓
Classpath changes
   ↓
Auto-configuration conditions match
   ↓
Infrastructure is configured
```

### Trap

❌ "Starter itself creates the beans."

Better:

> The starter primarily provides the dependency set; Boot auto-configuration reacts to what is available and configured.

---

# 18. ATTACK #16 — Externalized Configuration

## Q17. Why externalize configuration?

Because the same application artifact should be able to run with different runtime configuration.

Example:

```text
Same JAR
  ↓
dev config
test config
prod config
```

Spring Boot supports properties/YAML, environment variables, command-line arguments, external files and other property sources. It defines an ordering in which higher-precedence sources can override lower-precedence values. citeturn0search0

### Example

```yaml
CONFIG_SERVER_URL: ${CONFIG_SERVER_URL:http://localhost:7054}
```

Meaning:

```text
if CONFIG_SERVER_URL exists
    use it
else
    use localhost default
```

### Attack

**Q: Why is this useful in Docker?**

Same image can run with container-specific environment variables without rebuilding the application.

---

# 19. ATTACK #17 — Profiles

## Q18. What is a Spring Profile?

A mechanism for segregating configuration/components so that they are active only in selected environments.

Example:

```text
application.yml
application-dev.yml
application-prod.yml
```

Spring Boot's profile system can also restrict `@Component`, `@Configuration`, and `@ConfigurationProperties` beans with `@Profile`. citeturn0search6

### Attack

**Q: Are profiles security?**

No.

Profiles select configuration/beans.

They do not replace:
- secret management
- authorization
- encryption
- access control

### Attack

**Q: Which profile wins if multiple profiles are active?**

Spring Boot applies its documented ordering rules; later profile-specific configuration can override earlier values.

Do not invent a simplistic "prod always wins" rule.

---

# 20. ATTACK #18 — @Value vs @ConfigurationProperties

## Q19. Difference?

### @Value

Useful for individual/simple values.

```java
@Value("${hotel.timeout}")
private int timeout;
```

### @ConfigurationProperties

Useful for structured configuration.

```java
@ConfigurationProperties(prefix = "hotel.client")
class HotelClientProperties {
    private Duration timeout;
    private int retries;
}
```

### Why prefer ConfigurationProperties for larger config groups?

```text
typed binding
structured object
relaxed binding
metadata support
cohesive configuration model
```

### Attack

**Q: Does @Value support SpEL?**

Yes.

Do not falsely claim that `@ConfigurationProperties` and `@Value` are interchangeable.

---

# 21. ATTACK #19 — Property Precedence

## Q20. What happens if the same property exists in multiple places?

Spring Boot has a defined `PropertySource` precedence. Higher-precedence sources can override lower-precedence values. Exact ordering should be checked against the Boot version in use rather than memorized from an outdated blog. citeturn0search0

### Interview-safe approach

Explain the principle first:

```text
multiple sources
      ↓
defined precedence
      ↓
higher precedence wins
```

Then name the specific source order only if asked.

---

# 22. ATTACK #20 — Config Server

## Q21. Why use Config Server?

Centralize service configuration rather than duplicating environment-specific configuration across every service.

Project model:

```text
Git config repository
        ↓
Config Server
        ↓
User / Hotel / Rating Services
```

### Attack

**Q: Is Config Server a secret manager?**

No.

Configuration centralization and secret management are separate concerns.

Sensitive values require appropriate secret-management practices.

### Attack

**Q: What determines which config a service receives?**

Conceptually:

```text
application identity
+
profile
+
configuration repository
```

For this project:

```text
user-service + dev
hotel-service + dev
rating-service + dev
```

---

# 23. ATTACK #21 — Config Server Failure

## Q22. What happens if Config Server is unavailable during startup?

The exact result depends on how configuration import/failure behavior is configured.

Possible outcomes:
- startup failure
- optional import allowing startup to continue
- missing properties causing later failure

### Important project lesson

Your real debugging case was:

```text
User Service
   ↓
Config Server URL
   ↓
localhost:7054
   ↓
wrong network namespace
   ↓
connection refused
```

The fix was not "change Spring magic."

It was:

```text
localhost
→ current container

config-server
→ Config Server container
```

This is an excellent practical interview example.

---

# 24. ATTACK #22 — Actuator

## Q23. What is Spring Boot Actuator?

Actuator provides production-oriented monitoring and management endpoints.

Examples:

```text
/actuator/health
/actuator/info
/actuator/metrics
```

### Attack

**Q: Does health = application is fully healthy?**

Not necessarily.

A process can be alive while:
- a dependency is failing
- business operations are broken
- latency is unacceptable
- only part of the system is unhealthy

Use the distinction:

```text
Liveness
Readiness
Dependency health
Business health
```

depending on the deployment/health model.

### Security

Do not expose sensitive actuator endpoints publicly without deliberate security configuration.

---

# 25. ATTACK #23 — AOP

## Q24. What is AOP?

Aspect-Oriented Programming modularizes cross-cutting concerns that affect multiple classes/operations.

Examples:
- transactions
- logging
- security interception
- caching

Spring AOP complements OOP and primarily works around method execution join points on Spring-managed beans. citeturn0search3turn0search13

### Attack

**Q: Why does Spring care about proxies?**

Because framework behavior can be applied around method calls.

Conceptually:

```text
Caller
  ↓
Proxy
  ↓
Advice
  ↓
Target method
```

### Critical follow-up

**Q: What is self-invocation?**

If a method in a bean calls another method on `this`, that internal call can bypass the Spring proxy.

Therefore proxy-based advice may not execute as expected.

This becomes important later for:
- `@Transactional`
- `@Cacheable`
- security annotations
- custom AOP

Do not over-expand this module, but know the mechanism.

---

# 26. ATTACK #24 — Why Your Resilience4j Configuration Is Relevant

Your project uses Spring AOP as part of resilience interception.

Therefore an interviewer can ask:

```text
What does @Retry do?
↓
How is it intercepted?
↓
Does Spring use proxies?
↓
What happens on self-invocation?
↓
What is the fallback?
↓
What if the downstream call is non-idempotent?
```

This is a bridge from Module 1 into later Resilience4j depth.

The correct strategy is:

> Understand the mechanism now; defer detailed Resilience4j policy semantics to its dedicated module.

---

# 27. ATTACK #25 — Multiple Implementations

## Q25. What if Spring finds two beans implementing the same interface?

Example:

```java
interface PaymentClient {}
class StripeClient implements PaymentClient {}
class RazorpayClient implements PaymentClient {}
```

Injection becomes ambiguous unless Spring can resolve a preferred candidate.

Common tools:

```text
@Primary
@Qualifier
```

### Strong answer

> If multiple candidates match the dependency type, Spring needs additional resolution information. `@Primary` marks a preferred candidate, while `@Qualifier` selects a specific candidate.

### Follow-up

**Q: Which is better?**

Depends on the design.

- `@Primary` → default/preferred implementation
- `@Qualifier` → explicit selection

---

# 28. ATTACK #26 — Missing Dependency

## Q26. What happens if a required dependency cannot be resolved?

Typical startup path:

```text
ApplicationContext startup
        ↓
Dependency resolution
        ↓
No suitable bean
        ↓
UnsatisfiedDependency / related bean-creation failure
        ↓
Application startup fails
```

### Interview lesson

Spring normally prefers failing early rather than running with a required dependency silently absent.

---

# 29. ATTACK #27 — Constructor Injection and Circular Dependencies

## Q27. Can Spring have circular dependencies?

Circular dependencies can occur.

Example:

```text
A → B
B → A
```

Constructor-based circular dependencies are especially problematic because neither object can be fully constructed without the other.

### Interview-safe answer

> Circular dependencies are usually a design smell. Constructor injection makes many such cycles fail fast rather than hiding them.

Do not present `@Lazy` as the default solution. First question the architecture.

---

# 30. ATTACK #28 — Spring Singleton and Thread Safety

## Q28. If Spring creates one singleton bean, is it thread-safe?

No.

Singleton scope concerns instance count, not thread safety.

If multiple request threads access mutable shared state inside a singleton bean:

```text
Singleton
   +
shared mutable fields
   ↓
possible race conditions
```

Typical service beans should be designed to be stateless where practical.

### Very important interview distinction

```text
Singleton scope ≠ thread-safe
```

---

# 31. ATTACK #29 — Why Not Store Request Data in a Singleton Service?

Because a singleton service may serve many requests concurrently.

Bad:

```java
@Service
class UserService {
    private User currentUser;
}
```

Request-specific mutable state can leak between concurrent requests.

Better:

```java
public UserResponse getUser(Long id) {
    User user = ...
    ...
}
```

Keep request-local state in method scope or appropriate request-scoped structures.

---

# 32. ATTACK #30 — Auto-Configuration Back-Off

## Q30. Explain "back off."

Suppose Boot wants to auto-configure a bean under:

```text
@ConditionalOnMissingBean
```

If the application already defines the relevant bean:

```text
user-defined bean exists
        ↓
condition fails
        ↓
auto-configuration backs off
```

This is one of the strongest explanations of why Boot feels convenient without being completely controlling.

---

# 33. ATTACK #31 — Custom Auto-Configuration

## Q31. How would you create your own auto-configuration?

At high level:

```text
Auto-configuration class
        ↓
conditional annotations
        ↓
register configuration
        ↓
list class in AutoConfiguration.imports
        ↓
package in a library/starter
```

Current Boot documentation describes `@AutoConfiguration`, conditional annotations such as `@ConditionalOnClass` and `@ConditionalOnMissingBean`, and the `AutoConfiguration.imports` mechanism. citeturn0search5

### Priority

Know conceptually.

Do not spend major preparation time implementing a custom starter before P0/P1 topics are fluent.

---

# 34. ATTACK #32 — What Happens at Application Startup?

This is a powerful "everything together" question.

### Strong conceptual answer

```text
main()
 ↓
SpringApplication.run(...)
 ↓
environment/configuration prepared
 ↓
ApplicationContext created
 ↓
configuration + component scanning
 ↓
auto-configuration conditions evaluated
 ↓
bean definitions
 ↓
bean creation
 ↓
dependency resolution/injection
 ↓
post-processing/proxies
 ↓
embedded server starts for web app
 ↓
application ready
```

Do not pretend this is a literally exact internal call stack. It is the correct conceptual lifecycle.

### Project mapping

```text
UserServiceApplication.main()
        ↓
Spring Boot startup
        ↓
Config Server/config data
        ↓
ApplicationContext
        ↓
controllers/services/repositories/Feign/etc.
        ↓
Hikari/Hibernate
        ↓
Tomcat
        ↓
Eureka registration
        ↓
service ready
```

Your historical startup logs showed Tomcat, Hikari, Hibernate and service infrastructure initialization, making this a strong project-specific explanation.

---

# 35. ATTACK #33 — Debugging a Bean That "Doesn't Exist"

## Q33. My `@Service` bean is not found. What do you check?

Use a disciplined tree:

```text
1. Is the class annotated?
        ↓
2. Is it inside component scan?
        ↓
3. Is the module/class actually on classpath?
        ↓
4. Is a profile excluding it?
        ↓
5. Is the bean conditional?
        ↓
6. Is there a circular dependency?
        ↓
7. Are multiple candidates causing ambiguity?
        ↓
8. Did application startup fail earlier?
```

This is much stronger than:

> "I would restart the application."

---

# 36. ATTACK #34 — Debugging Auto-Configuration

## Q34. A dependency is present but expected Boot behavior is missing. What do you check?

```text
Dependency/classpath
      ↓
Auto-configuration candidate
      ↓
Conditions
      ↓
Existing user-defined beans
      ↓
Properties
      ↓
Profile
      ↓
Excluded auto-configuration
      ↓
Condition evaluation report
```

This is exactly the mindset interviewers want from a backend engineer: diagnose the mechanism instead of randomly adding annotations.

---

# 37. ATTACK #35 — Debugging Configuration

## Q35. My property is defined but Spring uses another value. What do you check?

```text
Property key spelling
        ↓
Active profile
        ↓
Property source
        ↓
Property precedence
        ↓
Environment variable
        ↓
Command-line argument
        ↓
External config
        ↓
Config Server
        ↓
Placeholder/default
```

Official Boot documentation emphasizes that configuration comes from multiple property sources and follows a defined precedence order. citeturn0search0

---

# 38. ATTACK #36 — Debugging Docker + Spring Configuration

## Q36. It works locally but fails inside Docker. What is your first suspicion?

Do not automatically blame Spring.

Check:

```text
hostname
port
container DNS
environment variables
network membership
published vs internal ports
localhost semantics
config-server availability
database hostname
```

### Your real case

```text
Local:
localhost:7054

Docker:
config-server:7054
```

This is a very strong interview story because it demonstrates diagnosis of a runtime environment difference.

---

# 39. TOP 15 QUESTIONS TO MASTER FIRST

If you only have one revision session, do these:

```text
1. Spring vs Spring Boot
2. IoC vs DI
3. What is a Bean?
4. ApplicationContext
5. Why constructor injection?
6. @Component / @Service / @Repository / @Controller
7. Component scanning
8. @SpringBootApplication
9. @Configuration vs @Bean
10. Bean lifecycle
11. Singleton vs prototype
12. Auto-configuration
13. Starter vs auto-configuration
14. Profiles + externalized configuration
15. @Value vs @ConfigurationProperties
```

Then immediately add:

```text
16. Auto-configuration back-off
17. @Primary vs @Qualifier
18. Config Server
19. Actuator
20. AOP/proxy basics
```

---

# 40. TOP 10 "INTERVIEWER CAN GO DEEP HERE" CHAINS

## Chain 1

```text
IoC
→ DI
→ Bean
→ ApplicationContext
→ lifecycle
→ BeanPostProcessor
→ proxy
→ AOP
```

## Chain 2

```text
@Autowired
→ candidate resolution
→ multiple beans
→ @Primary
→ @Qualifier
→ circular dependency
```

## Chain 3

```text
@Component
→ component scanning
→ package boundary
→ bean definition
→ instantiation
→ dependency injection
```

## Chain 4

```text
@SpringBootApplication
→ @Configuration
→ @EnableAutoConfiguration
→ @ComponentScan
```

## Chain 5

```text
Auto-configuration
→ classpath
→ conditions
→ @ConditionalOnClass
→ @ConditionalOnMissingBean
→ back-off
→ debugging
```

## Chain 6

```text
Starter
→ dependency graph
→ classpath
→ auto-configuration
→ beans
```

## Chain 7

```text
Profiles
→ property resolution
→ precedence
→ environment variables
→ external config
→ Config Server
```

## Chain 8

```text
@ConfigurationProperties
→ binding
→ typed configuration
→ metadata
→ validation
```

## Chain 9

```text
Singleton
→ concurrency
→ mutable state
→ thread safety
→ stateless service design
```

## Chain 10

```text
Spring config
→ Docker
→ localhost
→ container DNS
→ service name
→ network
→ production deployment
```

---

# 41. PROJECT-SPECIFIC "PROVE YOU BUILT IT" QUESTIONS

These are especially valuable because your project gives you real answers.

## Q1. Why did Config Server work locally but fail in Docker?

Answer:

```text
Local process:
localhost → host machine

Container:
localhost → current container

Therefore:
localhost:7054 ≠ Config Server container

Use:
config-server:7054
```

## Q2. Why didn't you hardcode the Docker hostname into local config?

Because the same application needs different runtime environments.

Use external configuration:

```text
CONFIG_SERVER_URL
```

with an appropriate local default.

## Q3. What happens if Config Server itself is unavailable?

Explain the configured import/fail-fast/optional behavior rather than giving a universal answer.

## Q4. Why use profiles?

To select environment-specific configuration/beans without rebuilding the application artifact.

## Q5. Why not commit DB passwords into Git config?

Configuration centralization does not make secrets safe. Secrets should be handled separately.

## Q6. Why constructor injection in your services?

Required dependencies are explicit, immutable where practical, testable, and resolved at construction.

## Q7. Why use `@Bean` for something instead of `@Component`?

When explicit construction/configuration is required or the class is external/third-party.

## Q8. What would you inspect if a bean suddenly disappeared?

Use the debugging tree from Attack #33.

---

# 42. RED-FLAG ANSWERS TO DELETE FROM YOUR VOCABULARY

Avoid these:

> "Spring automatically does everything."

> "IoC and DI are the same."

> "`@Autowired` creates the object."

> "Singleton means only one object in the whole application forever."

> "Auto-configuration means Spring creates every required bean unconditionally."

> "Starter is the auto-configuration."

> "Profiles are for security."

> "Config Server stores secrets safely."

> "Actuator health means the whole system is healthy."

> "Spring uses reflection for everything."

> "localhost means my laptop even inside Docker."

> "Retry always improves reliability."

These answers invite deeper questioning and often reveal shallow understanding.

---

# 43. THE ANSWER FORMULA

For most Spring interview questions, use:

```text
1. Definition
2. Mechanism
3. Why
4. Trade-off / failure
5. Project example
```

Example:

### "Why constructor injection?"

```text
Definition:
Inject dependency through constructor.

Mechanism:
Container resolves dependency and supplies it during bean creation.

Why:
Explicit required dependencies, testability, immutability.

Failure/trade-off:
Circular dependencies become visible; too many constructor dependencies may reveal excessive class responsibility.

Project:
Our service classes receive required collaborators through constructors.
```

This format is difficult to break with follow-up questions.

---

# 44. FINAL MODULE-1 INTERVIEW READINESS TEST

You are **not** done merely because you can answer:

> "What is IoC?"

You are done when you can survive:

```text
What is IoC?
   ↓
How is it implemented?
   ↓
What is DI?
   ↓
Who creates the bean?
   ↓
How is the bean discovered?
   ↓
What if there are two candidates?
   ↓
What if the dependency is missing?
   ↓
What is the lifecycle?
   ↓
Can it be proxied?
   ↓
What is the scope?
   ↓
Is singleton thread-safe?
   ↓
How does Boot configure it?
   ↓
What if auto-configuration conflicts?
   ↓
How would you debug it?
   ↓
How did this appear in your project?
```

That is the standard you should use for future modules.

---

# 45. MODULE 1 — FINAL ATTACK CHECKLIST

### P0 — Know cold

- [ ] Spring vs Spring Boot
- [ ] IoC vs DI
- [ ] Bean
- [ ] ApplicationContext
- [ ] Constructor injection
- [ ] Stereotypes
- [ ] Component scanning
- [ ] `@SpringBootApplication`
- [ ] `@Configuration` / `@Bean`
- [ ] Auto-configuration
- [ ] Starters
- [ ] Profiles
- [ ] Externalized configuration
- [ ] `@Value` vs `@ConfigurationProperties`
- [ ] Actuator

### P1 — Defend deeply

- [ ] Bean lifecycle
- [ ] BeanPostProcessor
- [ ] Bean scopes
- [ ] singleton vs prototype
- [ ] multiple candidates
- [ ] `@Primary` / `@Qualifier`
- [ ] auto-configuration conditions
- [ ] auto-configuration back-off
- [ ] property precedence
- [ ] Config Server

### P2 — Understand mechanism

- [ ] proxies
- [ ] AOP
- [ ] self-invocation
- [ ] prototype-in-singleton problem
- [ ] circular dependencies
- [ ] startup lifecycle
- [ ] auto-configuration debugging
- [ ] configuration debugging

### Project proof

- [ ] Explain Config Server architecture
- [ ] Explain local vs Docker configuration
- [ ] Explain `localhost` failure
- [ ] Explain service-name DNS
- [ ] Explain constructor injection in your services
- [ ] Explain why configuration is externalized
- [ ] Explain what is implemented vs merely known

---

# 46. SOURCE / EVIDENCE NOTES

## Primary technical authority

Spring Framework documentation:
- IoC container / DI / beans / ApplicationContext citeturn0search2turn0search14
- AOP citeturn0search3turn0search13

Spring Boot documentation:
- Auto-configuration citeturn0search1
- Auto-configuration implementation/discovery citeturn0search5
- Externalized configuration and property precedence citeturn0search0
- Profiles citeturn0search6

## Interview-pattern calibration

Current 2026 interview-prep sources repeatedly surface:
- IoC / DI
- ApplicationContext
- beans
- scopes
- autowiring
- `@Configuration` / `@Bean`
- component scanning
- stereotypes
- lifecycle
- auto-configuration
- common Boot/JPA/security/microservices topics. citeturn0search7turn0search8turn0search10turn0search12turn0search16

A current experienced-developer-oriented pattern also emphasizes that interviewers increasingly probe the mechanism behind annotations and failure modes rather than accepting one-line definitions. citeturn0search10turn0search18

---

# 47. Final Mental Model

```text
Spring
│
├── IoC Container
│    ├── Bean definitions
│    ├── Dependency resolution
│    ├── Lifecycle
│    ├── Scopes
│    └── Post-processing / proxies
│
└── Framework capabilities
     ├── AOP
     ├── MVC
     ├── Transactions
     └── Data/Security integrations

Spring Boot
│
├── Auto-configuration
├── Starters
├── Externalized configuration
├── Profiles
├── Embedded runtime
└── Production tooling

Project
│
├── Config Server
├── Eureka
├── Feign
├── Resilience4j
├── Zipkin
└── Docker
     └── localhost ≠ another container
```

**Module 1 Interview Attack KB — COMPLETE.**
