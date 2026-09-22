# Spring Boot Fundamentals — Interview Knowledge Base

**Scope:** Spring Core + Spring Boot concepts directly relevant to the Hotel Review System.  
**Goal:** concise notes that are deep enough to defend in interviews.

## 1. Core Mental Model

```text
Spring
└── IoC Container
    └── ApplicationContext
        ├── Bean definitions
        ├── Dependency resolution
        ├── Lifecycle
        └── Configuration

Spring Boot
├── Spring + opinionated defaults
├── Auto-configuration
├── Starters
├── Externalized configuration
└── Application startup
```

- **IoC:** control of object creation/configuration moves from application code to the container.
- **DI:** a mechanism/pattern for supplying dependencies.
- **Bean:** object managed by the Spring IoC container.
- **ApplicationContext:** central Spring container managing bean definitions, instances, dependencies and lifecycle.

## 2. Spring vs Spring Boot

**Spring:** underlying ecosystem: IoC/DI, AOP, MVC, transactions, data access, security, etc.

**Spring Boot:** builds on Spring and reduces configuration/boilerplate using starters, auto-configuration, externalized configuration and convenient startup.

**Interview:** Boot does not replace Spring; Boot simplifies building/running Spring applications.

## 3. IoC + DI

Without Spring:
```java
HotelRepository repository = new HotelRepository();
HotelService service = new HotelService(repository);
```

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

**Key distinction:** IoC = principle; DI = mechanism/pattern used to implement IoC.

## 4. Dependency Injection

### Constructor injection — preferred for required dependencies
- dependencies are explicit
- supports `final` fields
- object can be constructed in a valid dependency state
- easy unit testing
- failure is detected during construction/startup

### Setter injection
Reasonable for genuinely optional/changeable dependencies.

### Field injection
Works but hides dependencies and makes plain-Java construction/testing less convenient.

**Cross-question:** Why constructor injection?  
**Answer:** It makes mandatory dependencies explicit, supports immutability and straightforward unit testing.

## 5. Beans + Stereotypes

A Spring Bean is an object instantiated/configured/managed by the IoC container.

```text
@Component
├── @Service
├── @Repository
└── @Controller
```

- `@Component`: generic component.
- `@Service`: service-layer stereotype.
- `@Repository`: persistence stereotype and participates in persistence exception translation.
- `@Controller`: MVC controller stereotype.

**Trap:** `@Service` does not itself create the object. The container creates/manages it.

## 6. Component Scanning

Spring scans configured packages for candidate components and registers bean definitions.

Typical Boot entry point:
```java
@SpringBootApplication
public class HotelServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(HotelServiceApplication.class, args);
    }
}
```

At a high level, `@SpringBootApplication` combines configuration, auto-configuration enablement and component scanning.

If a component is outside the relevant scan boundary, it may not be discovered automatically.

**Cross-questions:** What happens if a dependency is missing? → startup/dependency-resolution failure such as an unsatisfied dependency.  
How are multiple implementations handled? → e.g. `@Primary` or `@Qualifier`.

## 7. `@Configuration` vs `@Bean`

```java
@Configuration
class AppConfig {
    @Bean
    SomeClient someClient() {
        return new SomeClient();
    }
}
```

- `@Configuration`: source of bean definitions.
- `@Bean`: registers/manages the returned object as a bean.

Use explicit `@Bean` especially for third-party classes or custom construction.

Deep point: `@Configuration` has special inter-bean semantics; calls between `@Bean` methods can be routed through the container. Do not generalize this to every `@Bean` method in an ordinary component.

## 8. Bean Lifecycle

Simplified:
```text
Bean definition
→ instantiation
→ dependency injection
→ post-processing / aware callbacks
→ initialization
→ ready
→ destruction at context shutdown
```

`@PostConstruct` is an initialization callback. It is not literally the first operation after Java construction.

**Deep interview:** `BeanPostProcessor`s are extension points around bean initialization and are involved in Spring infrastructure/proxying.

## 9. Bean Scopes

Default/common scope: **singleton**.

Spring singleton means one managed instance **per ApplicationContext**, not one object globally.

`prototype`: a new instance is created when requested from the container.

Web scopes include request/session where applicable.

**Trap:** Spring singleton ≠ GoF Singleton pattern.

## 10. Auto-Configuration

Conceptual chain:
```text
Classpath/dependencies
       +
Environment/properties
       +
Conditional checks
       ↓
Auto-configuration
       ↓
Infrastructure beans
```

Conditions can depend on class presence, bean presence, properties and other conditions.

Auto-configuration is **conditional**, not blind magic. If a user-defined bean satisfies a condition such as `@ConditionalOnMissingBean`, the relevant auto-configuration can back off.

Debugging: `--debug` shows auto-configuration decisions; Actuator can expose conditions when enabled.

## 11. Starters

A starter is a convenient dependency descriptor/bundle.

```text
Starter
→ relevant dependencies on classpath
→ auto-configuration conditions
→ infrastructure
→ feature
```

**Trap:** a starter primarily brings dependencies; it is not itself the complete auto-configuration mechanism.

## 12. Externalized Configuration

Spring Boot supports YAML/properties, environment variables, command-line arguments, external files and other property sources.

Goal:
```text
same artifact + different runtime configuration = different environment
```

Example:
```yaml
url: ${CONFIG_SERVER_URL:http://localhost:7054}
```

Means: use `CONFIG_SERVER_URL` if supplied; otherwise use the default.

Spring Boot applies a defined property-source precedence; higher-precedence sources can override lower-precedence values.

## 13. Profiles

Typical:
```text
application.yml
application-dev.yml
application-prod.yml
```

Activation:
```text
SPRING_PROFILES_ACTIVE=dev
```

Profiles can also control components/configuration with `@Profile`.

**Trap:** profiles are configuration selection, not secret management.

## 14. `@Value` vs `@ConfigurationProperties`

`@Value`:
```java
@Value("${hotel.timeout}")
private int timeout;
```

Good for small/simple individual values.

`@ConfigurationProperties`:
```java
@ConfigurationProperties(prefix = "hotel.client")
class HotelClientProperties {
    private Duration timeout;
    private int retries;
}
```

Preferred for cohesive/hierarchical configuration because it provides typed binding, relaxed binding and metadata support.

Key differences:
- `@ConfigurationProperties`: structured/type-safe, relaxed binding, metadata.
- `@Value`: simple injection and supports SpEL.

## 15. Configuration Precedence

Understand the principle rather than memorizing a giant list:

> Multiple property sources exist; Spring Boot applies a defined order and higher-precedence/later sources can override earlier values.

For exact precedence, use the version-specific Spring Boot reference.

## 16. `spring.application.name`

Example:
```yaml
spring:
  application:
    name: hotel-service
```

Provides the application's logical identity used by Spring Cloud infrastructure and related mechanisms.

In this project it matters for:
- Config Server lookup
- service discovery identity
- consistent service naming

Keep names consistent: `hotel-service`, `rating-service`, `user-service`.

## 17. Config Server

Project flow:
```text
Git config repository
       ↓
Config Server
       ↓
User / Hotel / Rating services
```

Conceptually, configuration is resolved using application identity + active profile, e.g. `hotel-service + dev`.

**Config management ≠ secret management.** DB passwords, OAuth secrets and API keys need appropriate secret handling and should not casually be committed to Git.

## 18. Docker Configuration — Actual Project Experience

Local:
```text
CONFIG_SERVER_URL=http://localhost:7054
```

Container:
```text
CONFIG_SERVER_URL=http://config-server:7054
```

Inside a container:
```text
localhost → current container
config-server → Config Server container via Docker network DNS
```

**Interview-quality explanation:** the problem was the runtime network namespace/hostname, not Spring itself. `localhost:7054` referred to the User Service container; `config-server:7054` addressed the Config Server container.

This is a strong practical example connecting Spring configuration and Docker networking.

## 19. Actuator

Actuator provides production-oriented monitoring/management endpoints.

Examples:
- `/actuator/health`
- `/actuator/info`
- `/actuator/metrics`

It can also expose diagnostic information such as configuration properties and auto-configuration conditions when enabled.

**Important:** process alive ≠ service healthy ≠ business operation healthy.

## 20. Project Mapping

```text
Spring Boot
    ↓
ApplicationContext
    ↓
Controller
    ↓
Service
    ↓
Repository → PostgreSQL
    ↓
Feign client → other microservice
```

Configuration path:
```text
application.yml
→ Config Server import
→ Git-backed config
→ active profile
→ environment variables
→ runtime configuration
```

## 21. Interview Cross-Question Trees

### IoC
What is IoC?
→ What is DI?
→ Who creates the object?
→ What is a Bean?
→ What is ApplicationContext?
→ What happens during startup?
→ What if dependency resolution fails?

### `@Service`
What is `@Service`?
→ Is it different from `@Component`?
→ How is it discovered?
→ What is component scanning?
→ Who creates it?
→ What scope does it have?
→ What if two implementations exist?

### `@Bean`
Why use `@Bean`?
→ `@Component` vs `@Bean`?
→ Why third-party classes?
→ What does `@Configuration` do?
→ What happens between `@Bean` methods?
→ What about `proxyBeanMethods=false`?

### Auto-configuration
What is auto-configuration?
→ What triggers it?
→ What are conditional annotations?
→ What if I define my own bean?
→ How do I customize it?
→ How do I debug it?
→ What role do starters play?

### Configuration
Why externalize configuration?
→ Profiles?
→ Environment variables?
→ Property precedence?
→ `${X:default}`?
→ `@Value` vs `@ConfigurationProperties`?
→ Config Server?
→ Secrets?
→ Config Server unavailable?

### Docker
Why did localhost fail?
→ container network namespace?
→ Docker DNS?
→ service-name resolution?
→ host port vs container port?
→ OCI deployment implications?

## 22. High-Value Interview Traps

1. Boot replaces Spring → **false**.
2. IoC = DI → **not precisely**.
3. `@Service` creates the object → **container does**.
4. Singleton = globally one object → **one per ApplicationContext**.
5. Auto-configuration = everything automatically → **conditional**.
6. Starter = auto-configuration → **starter mainly supplies dependencies**.
7. Profile = security → **no**.
8. Config Server = secret manager → **not inherently**.
9. localhost = laptop → **not inside a container**.
10. Actuator health = whole system healthy → **not necessarily**.

## 23. 30-Second Interview Answer

> Spring Boot is built on Spring and simplifies application development through auto-configuration, starters and externalized configuration. At the core is Spring's IoC container, represented through an ApplicationContext, which creates and manages beans and resolves dependencies. In our Hotel Review System, Spring manages controllers, services, repositories and infrastructure clients, while constructor injection makes dependencies explicit. Profiles and external configuration allow the same service artifact to run in different environments, and Config Server centralizes service configuration.

## 24. 2-Minute Project Answer

> Each service in our Hotel Review System is a Spring Boot application. During startup, Spring Boot creates the application context and registers configuration and component beans. Controllers, services and repositories are discovered through component scanning, while dependencies are primarily injected through constructors.
>
> Spring Boot also applies conditional auto-configuration based on the classpath and environment. We use profiles and externalized configuration so the same service artifact can run in different environments. Our services import configuration from a centralized Config Server backed by a Git configuration repository.
>
> Docker changes the networking context. `localhost:7054` inside the User Service container refers to that container itself, so Config Server is addressed as `config-server:7054` through the Docker network. This demonstrates how Spring configuration and runtime networking interact.

## 25. Status

### ✅ Actually implemented/experienced
- Spring Boot services
- IoC/DI
- Controller → Service → Repository
- Constructor injection
- Profiles
- `application.yml`
- Environment variables
- Config Server
- Git-backed configuration
- Docker runtime configuration
- Docker `localhost` vs service-name issue

### 🔵 General interview knowledge
- Detailed bean lifecycle
- scopes beyond singleton
- auto-configuration conditions
- `@ConfigurationProperties`
- `@Primary` / `@Qualifier`
- detailed property precedence
- Actuator internals

### 🟡 Planned/deeper later
- production secret management
- OCI-specific health/monitoring

### 🔴 Never claim
Anything not actually implemented merely because it appears in this KB.

## 26. Recall Gate

Before moving on, explain without notes:
- Spring vs Spring Boot
- IoC vs DI
- ApplicationContext
- Bean
- Constructor injection
- Component scanning
- stereotypes
- `@Configuration` / `@Bean`
- lifecycle
- singleton vs prototype
- auto-configuration
- starters
- profiles
- property resolution
- `@Value` vs `@ConfigurationProperties`
- Config Server
- secrets vs configuration
- Actuator
- Docker localhost vs service name
- actual service startup/configuration flow

**Completion standard:** explain the mechanism, justify the choice, and connect it to the project — not textbook wording.

## Primary References

- Spring Framework — Component Scanning / Managed Components
- Spring Framework — `@Bean` / `@Configuration`
- Spring Boot — Externalized Configuration
- Spring Boot — Profiles
- Spring Boot — Auto-configuration

**Module 1 is frozen.**
