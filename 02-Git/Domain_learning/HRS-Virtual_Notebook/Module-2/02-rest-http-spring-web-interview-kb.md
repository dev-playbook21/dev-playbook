# Module 2 — REST / HTTP / Spring Web — Interview Knowledge Base

**Continuation of:** `01-spring-boot-fundamentals-interview-kb.md`  
**Status:** CLOSED / READY FOR INTERVIEW REVISION

---

## 0. Module Mental Model

```text
Client
  ↓
HTTP Request
  ↓
API Gateway
  ↓
Spring Boot Service
  ↓
Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
HandlerAdapter
  ↓
Controller
  ↓
DTO binding + Validation
  ↓
Service
  ├── Repository → Database
  └── Feign → Another Microservice
  ↓
Result / Exception
  ↓
HttpMessageConverter
  ↓
HTTP Response
```

### Core separation

```text
HTTP            → communication contract
Controller      → HTTP boundary
DTO             → API contract
Service         → business logic
Repository      → persistence
Feign           → service-to-service HTTP call
Exception layer → consistent error contract
```

---

# 1. HTTP FUNDAMENTALS

## 1.1 HTTP Request

An HTTP request contains:

```text
Method
Target / URI
Headers
Optional Body
```

Example:

```http
GET /hotels/42 HTTP/1.1
Host: api.example.com
Accept: application/json
Authorization: Bearer <token>
```

### Content-Type vs Accept

- `Content-Type` → representation being **sent**
- `Accept` → representation the client **accepts**

---

## 1.2 HTTP Methods

### GET
Retrieve a representation/resource.

- Safe
- Idempotent

### POST
Server processes submitted data.

Commonly used for creation.

- Not inherently idempotent

### PUT
Usually replacement/update semantics for the target resource.

- Idempotent

### PATCH
Partial modification.

- Idempotency depends on the operation.

### DELETE
Requests deletion of the target resource.

- Generally idempotent semantically.

### HEAD
Like GET for metadata/headers without a response body.

### OPTIONS
Describes supported communication options; also relevant to CORS/preflight.

---

## 1.3 Safe vs Idempotent

**Safe:** intended not to change server state as part of the requested operation.

**Idempotent:** repeating the same request has the same intended effect as making it once.

Idempotent does **not** mean identical responses every time.

Example:

```text
DELETE /hotels/42
```

First call may return `204`; a later call may return `404`. The intended state-changing effect is not multiplied.

### Interview trap

“GET is idempotent, so GET can never have side effects.”

Wrong. HTTP semantics describe intended method semantics; badly designed server code can still perform unwanted side effects.

---

# 2. HTTP STATUS CODES

## 2.1 Success

- `200 OK` → successful request with response representation
- `201 Created` → resource successfully created; often with `Location`
- `204 No Content` → successful request with no response body

## 2.2 Client/request problems

- `400 Bad Request` → malformed/invalid request or validation failure
- `401 Unauthorized` → authentication missing/invalid
- `403 Forbidden` → authenticated/request understood, but not authorized
- `404 Not Found` → target resource/endpoint not found
- `409 Conflict` → request conflicts with current resource state

Memory:

```text
401 → Who are you?
403 → You are known, but not allowed.
```

## 2.3 Server/upstream problems

- `500 Internal Server Error` → unexpected server-side failure
- `502 Bad Gateway` → gateway/proxy received an invalid upstream response
- `503 Service Unavailable` → service temporarily unavailable/overloaded

---

# 3. REST RESOURCE DESIGN

Prefer resource-oriented URIs:

```text
GET    /hotels
GET    /hotels/42
POST   /hotels
PUT    /hotels/42
PATCH  /hotels/42
DELETE /hotels/42
```

Avoid unnecessary CRUD-style action names such as:

```text
/getHotel
/createHotel
/deleteHotel
```

The URI identifies the target; the HTTP method expresses operation semantics.

---

# 4. STATELESSNESS

REST-style statelessness means each request contains the information necessary for the server to process it; the server does not depend on conversational session state stored from previous requests.

It does **not** mean:

```text
No database
No cache
No application state
```

A service can have persistent data and still expose stateless HTTP interactions.

---

# 5. SPRING MVC REQUEST LIFECYCLE

```text
Client
  ↓
HTTP Request
  ↓
Embedded Tomcat
  ↓
DispatcherServlet
  ↓
HandlerMapping
  ↓
HandlerAdapter
  ↓
Controller Method
  ↓
Argument Resolution
  ↓
DTO Deserialization
  ↓
Validation
  ↓
Service
  ↓
Repository / Feign
  ↓
Return Value
  ↓
HttpMessageConverter
  ↓
JSON Response
```

### Embedded Tomcat

Spring Boot commonly runs a web application using an embedded servlet container such as Tomcat.

It receives HTTP connections and hands requests into the Spring MVC pipeline.

---

# 6. DispatcherServlet

`DispatcherServlet` is the central Front Controller of Spring MVC.

Simplified:

```text
HTTP Request
     ↓
DispatcherServlet
     ↓
Find handler
     ↓
Invoke handler
     ↓
Process result
     ↓
Write response
```

It centralizes infrastructure such as:
- handler mapping
- argument resolution
- message conversion
- exception handling
- response processing

---

# 7. HandlerMapping vs HandlerAdapter

### HandlerMapping

Answers:

> Which handler/controller method should handle this request?

### HandlerAdapter

Answers:

> How should the selected handler be invoked?

Remember:

```text
HandlerMapping → finds
HandlerAdapter → invokes
```

---

# 8. CONTROLLER DESIGN

A controller is the HTTP boundary.

Example:

```java
@RestController
@RequestMapping("/hotels")
public class HotelController {

    @GetMapping("/{id}")
    public ResponseEntity<HotelResponse> getHotel(
            @PathVariable Long id) {

        return ResponseEntity.ok(
            hotelService.getHotel(id)
        );
    }
}
```

A controller should primarily handle:
- HTTP input
- parameter binding
- validation trigger
- delegation
- HTTP response construction

### Thin Controller Principle

```text
Controller
   ↓
Service
   ↓
Repository / Feign
```

Avoid putting large amounts of business logic, DB logic, or remote-call orchestration directly in controllers.

---

# 9. REQUEST PARAMETER BINDING

## @PathVariable

Resource identity/path data.

```text
GET /hotels/42
```

```java
@GetMapping("/{id}")
public HotelResponse get(@PathVariable Long id) {
    ...
}
```

## @RequestParam

Query parameters.

```text
GET /hotels?city=Delhi&page=0
```

Typical uses:
- filtering
- sorting
- pagination
- searching

## @RequestHeader

Reads an HTTP header.

```java
@RequestHeader("Authorization") String authorization
```

## @RequestBody

Binds structured request-body data to a Java object.

```http
POST /hotels
Content-Type: application/json

{
  "name": "Mountain View",
  "location": "Tehri"
}
```

```java
@PostMapping
public ResponseEntity<?> create(
        @Valid @RequestBody HotelRequest request) {
    ...
}
```

---

# 10. JSON ↔ JAVA OBJECTS

Spring MVC uses `HttpMessageConverter`s to convert HTTP representations to/from Java objects.

For JSON, Jackson is commonly used.

```text
Incoming JSON
     ↓
HttpMessageConverter
     ↓
Jackson
     ↓
Java DTO
```

Response:

```text
Java DTO
     ↓
Jackson
     ↓
JSON
```

---

# 11. @Controller vs @RestController

### @Controller

Used for MVC controllers and commonly associated with view rendering.

### @RestController

Conceptually:

```java
@Controller
@ResponseBody
```

Returned values are normally written directly to the HTTP response body.

For REST APIs, `@RestController` is the common choice.

---

# 12. DTOs

DTO = Data Transfer Object.

It defines an API-facing data contract without exposing the persistence entity directly.

```text
Client
  ↕
Request/Response DTO
  ↕
Service
  ↕
Entity
  ↕
Database
```

### Why DTOs?

- avoid leaking persistence details
- control exposed fields
- reduce accidental sensitive-data exposure
- decouple API contract from DB schema
- allow request/response shapes to differ

### Request DTO vs Response DTO

Use separate models when responsibilities differ.

```text
CreateHotelRequest
UpdateHotelRequest
HotelResponse
```

Generated IDs/timestamps, for example, should generally not be client-controlled merely because they exist on the response.

---

# 13. VALIDATION

Example:

```java
public class HotelRequest {

    @NotBlank
    private String name;

    @NotBlank
    private String location;
}
```

Controller:

```java
@PostMapping
public ResponseEntity<?> create(
        @Valid @RequestBody HotelRequest request) {
    ...
}
```

Flow:

```text
JSON
 ↓
DTO binding
 ↓
Bean Validation
 ↓
Valid → Service
Invalid → validation exception
```

## @Valid vs @Validated

- `@Valid` → standard Bean Validation trigger
- `@Validated` → Spring-specific variant, useful for features such as validation groups and method-level validation

---

# 14. THREE TYPES OF VALIDATION

### Input validation

Examples:
- name not blank
- age positive
- email valid

Usually near the API boundary.

### Business validation

Example:

```text
Hotel cannot be deleted while it has an active booking.
```

Domain/business layer.

### Authorization

Example:

```text
Only ADMIN can delete a hotel.
```

Security/authorization concern.

Do not mix these concepts.

---

# 15. ResponseEntity

`ResponseEntity<T>` gives explicit control over:

- status
- headers
- body

Example:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(response);
```

It is useful when explicit HTTP response control is required; not every method must use it.

---

# 16. CONTENT NEGOTIATION

The client can communicate acceptable representations using:

```http
Accept: application/json
```

The server chooses an appropriate supported representation.

Remember:

```text
Content-Type → what this message body is
Accept       → what representation the client accepts
```

---

# 17. EXCEPTION HANDLING

## Local vs Centralized

Local `try/catch` is appropriate for genuinely local recovery/translation.

Putting repetitive API error handling in every controller creates duplication.

Centralized handling:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    ...
}
```

---

# 18. @ExceptionHandler

Maps an exception to an HTTP response.

Example:

```java
@ExceptionHandler(ResourceNotFoundException.class)
public ResponseEntity<ErrorResponse> handleNotFound(...) {
    ...
}
```

---

# 19. @ControllerAdvice vs @RestControllerAdvice

### @ControllerAdvice

Centralized exception handling and other cross-controller behavior.

### @RestControllerAdvice

REST-oriented advice with response-body semantics.

For a REST API error layer, `@RestControllerAdvice` is commonly appropriate.

---

# 20. STABLE ERROR CONTRACT

Avoid returning random raw exception strings.

Example contract:

```json
{
  "timestamp": "...",
  "status": 404,
  "error": "Not Found",
  "message": "Hotel not found",
  "path": "/hotels/42",
  "traceId": "..."
}
```

Exact fields are an API design decision.

### Public vs Internal Information

Public response:
- safe
- stable
- useful to client

Internal logs/traces:
- detailed diagnostics
- stack traces
- downstream failure details
- trace/correlation identifiers

Do not expose secrets, credentials, or unnecessary internal infrastructure details.

---

# 21. EXCEPTION → HTTP MAPPING

Typical semantic mapping:

```text
Validation failure        → 400
Malformed request         → 400
Authentication failure    → 401
Authorization failure     → 403
Resource not found        → 404
State/uniqueness conflict → 409
Unexpected server failure → 500
```

Do not blindly map every exception to `500`.

---

# 22. EXCEPTION HANDLING ≠ RESILIENCE

### Exception handling

Answers:

> How should a failure be represented to the caller?

### Resilience

Answers:

> How should the system behave when a dependency fails?

Resilience mechanisms include:
- retry
- circuit breaker
- rate limiter
- timeout
- fallback

Project flow:

```text
Controller
   ↓
Service
   ↓
Feign
   ↓
Rating/Hotel Service
   ↓
Failure
   ↓
Resilience policy
   ↓
Fallback / propagated failure
   ↓
HTTP error contract
```

A retry is not automatically good. Consider idempotency, transient failure, bounded retries/backoff, and downstream load.

---

# 23. DOWNSTREAM MICROSERVICE FAILURE

Example:

```text
User Service
    ↓ Feign
Rating Service
    ↓
Unavailable
```

Possible behavior:
- timeout
- retry where appropriate
- circuit breaker
- fallback
- controlled error response

Do not blindly expose raw downstream exceptions.

---

# 24. COMPLETE HOTEL REVIEW SYSTEM FLOW

```text
Client
  ↓
API Gateway
  ↓
User Service
  ↓
Controller
  ↓
Service Layer
  ├── Rating Service via Feign
  └── Hotel Service via Feign
  ↓
Aggregation
  ↓
Response DTO
  ↓
HTTP JSON Response
```

Supporting infrastructure:
- Eureka → service discovery
- Config Server → centralized configuration
- Resilience4j → resilience
- Zipkin → distributed tracing
- MySQL → User Service
- PostgreSQL → Hotel Service
- MongoDB → Rating Service

### Docker networking lesson

Inside Docker:

```text
localhost
```

means the **current container**.

Therefore:

```text
http://localhost:7054
```

inside User Service does not mean Config Server.

Inter-container communication uses Docker service names:

```text
http://config-server:7054
http://service-registry:8761/eureka/
http://zipkin:9411
```

This was a real debugging lesson in the project.

---

# 25. INTERVIEW ATTACK — CORE QUESTIONS

## HTTP

1. What is HTTP?
2. Components of an HTTP request?
3. URI vs HTTP method?
4. GET vs POST?
5. PUT vs PATCH?
6. Is DELETE idempotent?
7. Safe vs idempotent?
8. Can GET have side effects?
9. Content-Type vs Accept?
10. 401 vs 403?
11. 404 vs 409?
12. 500 vs 502 vs 503?
13. Why 201 instead of 200?
14. What does stateless REST mean?

## Spring MVC

15. What happens after a request reaches Spring Boot?
16. What is DispatcherServlet?
17. Why Front Controller?
18. What does HandlerMapping do?
19. What does HandlerAdapter do?
20. How does `@GetMapping` get resolved?
21. How does `@RequestBody` work?
22. What is HttpMessageConverter?
23. How does JSON become Java?
24. How does Java become JSON?
25. `@Controller` vs `@RestController`?
26. What is content negotiation?

## Controller / DTO

27. Why keep controllers thin?
28. Why use DTOs instead of entities?
29. Request DTO vs response DTO?
30. `@PathVariable` vs `@RequestParam`?
31. When use `@RequestHeader`?
32. Why use `ResponseEntity`?
33. Where should business validation live?

## Validation

34. What does `@Valid` do?
35. `@Valid` vs `@Validated`?
36. Input validation vs business validation?
37. Is authorization validation?
38. What happens when DTO validation fails?

## Exception Handling

39. How does Spring handle exceptions?
40. What is `@ExceptionHandler`?
41. What is `@ControllerAdvice`?
42. What is `@RestControllerAdvice`?
43. Why centralized exception handling?
44. What should an API error response contain?
45. Should stack traces be returned?
46. Exception handling vs logging?
47. Exception handling vs resilience?
48. How would you handle a downstream Feign failure?

---

# 26. CROSS-QUESTION TREES

## Tree A — @RequestBody

```text
@RequestBody
  ↓
How is JSON converted?
  ↓
HttpMessageConverter
  ↓
Which converter?
  ↓
Jackson JSON converter
  ↓
How is validation triggered?
  ↓
@Valid / Bean Validation
  ↓
What if invalid?
  ↓
Validation exception
  ↓
@RestControllerAdvice
  ↓
400 response
```

## Tree B — 401 vs 403

```text
401 vs 403
  ↓
Authentication vs Authorization
  ↓
Who are you? vs Are you allowed?
  ↓
OAuth2/JWT
  ↓
Claims/scopes/roles
  ↓
Security configuration
```

## Tree C — Controller to DB

```text
HTTP
 ↓
Tomcat
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
HandlerAdapter
 ↓
Controller
 ↓
DTO
 ↓
Validation
 ↓
Service
 ↓
Repository
 ↓
JPA/Hibernate
 ↓
Database
```

Later interviewer branches:
- transactions
- persistence context
- lazy loading
- N+1
- connection pooling
- SQL/indexes

## Tree D — Controller to Microservice

```text
HTTP
 ↓
Controller
 ↓
Service
 ↓
Feign
 ↓
Service Discovery
 ↓
Target Service
 ↓
HTTP response
 ↓
Feign deserialization
 ↓
Service
 ↓
Controller response
```

Later branches:
- Eureka
- load balancing
- timeouts
- retries
- circuit breakers
- fallback
- tracing

---

# 27. 30-SECOND INTERVIEW ANSWER

> “In Spring Boot REST APIs, the request first reaches the embedded servlet container and then enters DispatcherServlet, which acts as the Front Controller. Spring uses HandlerMapping to find the appropriate controller method and HandlerAdapter to invoke it. Request data is bound through annotations such as `@PathVariable`, `@RequestParam`, and `@RequestBody`; JSON conversion is handled through HttpMessageConverters, commonly using Jackson. Validation can be triggered with `@Valid`. The controller delegates business logic to the service layer, which can access a repository or call another microservice through Feign. Results are converted back to JSON, while centralized `@RestControllerAdvice` can provide a consistent error contract.”

---

# 28. 2-MINUTE PROJECT ANSWER

> “In my Hotel Review System, the client reaches the API Gateway and then the relevant Spring Boot microservice. Inside the service, the request enters the embedded Tomcat server and Spring MVC's DispatcherServlet. HandlerMapping resolves the request to the controller method. The controller accepts request parameters or DTOs, and validation is applied at the API boundary where required.
>
> I keep the controller thin and delegate business logic to the service layer. Depending on the operation, the service either talks to its own database through the persistence layer or calls another microservice through OpenFeign. For example, User Service can aggregate data from Rating Service and Hotel Service.
>
> The result is converted into the API response representation, normally JSON. For errors, centralized exception handling provides a consistent HTTP error contract instead of exposing raw exceptions. For downstream failures, exception handling and resilience are separate concerns: Resilience4j can control retries, circuit breaking, rate limiting and fallback behavior, while the API layer decides how the resulting failure is represented to the client.
>
> A practical issue I encountered while Dockerizing the system was that `localhost` inside a container referred to that container itself. The services therefore had to communicate using Docker service names such as `config-server`, `service-registry`, and `zipkin`. That gave me practical understanding of the difference between local-host configuration and container-to-container networking.”

---

# 29. COMMON INTERVIEW TRAPS

### Trap 1
“POST always creates a resource.”

**Correction:** POST is a general processing method; creation is a common use case.

### Trap 2
“PUT and PATCH are the same.”

**Correction:** PUT commonly expresses replacement semantics; PATCH expresses partial modification.

### Trap 3
“401 means permission denied.”

**Correction:** 401 concerns authentication; 403 concerns authorization.

### Trap 4
“Stateless means the server cannot store data.”

**Correction:** Stateless HTTP means each request is independently processable; databases/caches can still exist.

### Trap 5
“DTO is just an entity copy.”

**Correction:** DTO defines a communication contract and should be designed around API needs.

### Trap 6
“Exception handling and Resilience4j are the same.”

**Correction:** Exception handling shapes failure responses; resilience controls behavior around dependency failures.

### Trap 7
“Controller should contain all logic because it receives the request.”

**Correction:** Controllers should primarily handle the HTTP boundary and delegate business logic.

### Trap 8
“localhost means the machine.”

**Correction:** Inside Docker, localhost means the current container.

### Trap 9
“Retry every failed request.”

**Correction:** Retry must consider idempotency, failure type, backoff and downstream load.

### Trap 10
“Return stack trace to help the client debug.”

**Correction:** Keep detailed diagnostics in internal logs/traces; return a safe and stable public error contract.

---

# 30. PROJECT STATUS BOUNDARY

## ✅ Actually implemented / experienced

- Spring Boot REST APIs
- Controllers
- DTO-based API boundaries
- Request/response handling
- Validation
- Feign-based service calls
- User Service aggregation
- Resilience4j
- OAuth2/JWT security
- Eureka
- Config Server
- Zipkin
- Docker networking
- MySQL/PostgreSQL/MongoDB integration
- Centralized configuration
- Practical `localhost` vs container DNS debugging

## 🔵 General interview knowledge

- Full HTTP semantics
- Safe/idempotent distinctions
- Detailed Spring MVC internals
- Content negotiation
- HandlerMapping / HandlerAdapter internals
- General HTTP status semantics

## 🟡 Planned / later modules

- Deep JPA/Hibernate internals
- Transactions and isolation
- Advanced persistence
- Deep Eureka internals
- Deep Feign internals
- Deep Resilience4j internals
- Advanced API Gateway
- Production reverse proxy / TLS
- CI/CD and Kubernetes

## 🔴 Do not claim unless actually implemented later

- Kubernetes production deployment
- Kafka production integration
- GraphQL implementation
- WebFlux production implementation
- AWS production deployment
- CI/CD pipeline implementation

---

# 31. RECALL GATE

Before declaring this module fully digested, explain without notes:

### HTTP
- safe vs idempotent
- PUT vs PATCH
- 401 vs 403
- 404 vs 409
- Content-Type vs Accept
- statelessness

### Spring MVC
- Tomcat → DispatcherServlet → HandlerMapping → HandlerAdapter
- `@PathVariable`
- `@RequestParam`
- `@RequestBody`
- HttpMessageConverter
- Jackson
- `@Controller` vs `@RestController`

### API design
- thin controller
- DTO vs Entity
- request DTO vs response DTO
- validation layers
- `ResponseEntity`

### Errors
- `@ExceptionHandler`
- `@RestControllerAdvice`
- stable error contract
- public response vs internal diagnostics
- exception handling vs resilience

### Project
- explain one complete request through the Hotel Review System
- explain User Service aggregation
- explain Feign failure path
- explain the Docker `localhost` problem
- explain why container service names are used

---

# 32. ONE-LINE MEMORY MAP

```text
HTTP semantics
→ Spring MVC pipeline
→ Controller boundary
→ DTO + validation
→ Service logic
→ Repository / Feign
→ Response conversion
→ Centralized errors
→ Resilience for downstream failures
→ Project-specific Docker/networking reality
```

**Module 2 is frozen.**
