# Module 2 — REST / HTTP / Spring Web
## High-Probability Interview Attack KB
### Deep interviewer-oriented companion to `02-rest-http-spring-web-interview-kb.md`

**Purpose:** This is the interview-attack layer, not a duplicate theory chapter.

It focuses on the questions that repeatedly appear in Spring Boot REST interviews, the follow-ups interviewers use to expose shallow understanding, the traps, and the exact depth expected from someone who has actually built REST microservices.

**Evidence rule:** There is no reliable public dataset giving exact percentages for interviewer question frequency. Therefore this KB uses priority bands based on recurring interview patterns plus current official Spring documentation. No fabricated percentages are used.

---

# 0. PRIORITY MODEL

## 🔴 P0 — Must answer cold

```text
HTTP request/response
HTTP methods
safe vs idempotent
PUT vs PATCH
status codes
Content-Type vs Accept
statelessness
@RestController
@PathVariable vs @RequestParam
@RequestBody
DispatcherServlet
DTO vs Entity
validation
ResponseEntity
global exception handling
```

## 🟠 P1 — Very high-value follow-ups

```text
Spring MVC request lifecycle
HandlerMapping vs HandlerAdapter
HttpMessageConverter
Jackson
content negotiation
@Valid vs @Validated
@ControllerAdvice vs @RestControllerAdvice
structured error responses
downstream service failure
```

## 🟡 P2 — Depth probes

```text
argument resolution
message converter selection
validation exception differences
method validation
ProblemDetail / RFC 9457
request mapping ambiguity
media-type errors
exception-resolution flow
```

## 🔵 P3 — Specialist branches

```text
custom converters
advanced content negotiation
custom HandlerMethodArgumentResolver
deep MVC infrastructure
functional endpoints
advanced error-response customization
```

Do not spend P3 time before P0/P1 is fluent.

---

# 1. INTERVIEWER ATTACK PATTERN

A weak candidate answers:

> "`@RequestBody` converts JSON into an object."

A strong candidate can survive:

```text
How?
 ↓
Who performs conversion?
 ↓
Which abstraction?
 ↓
Which converter?
 ↓
How is the converter selected?
 ↓
What if Content-Type is unsupported?
 ↓
What if JSON is malformed?
 ↓
What if validation fails?
 ↓
What exception is raised?
 ↓
How does the API return a stable error?
```

That is the standard we are targeting.

---

# 2. ATTACK — WHAT IS REST?

## Q1. What is REST?

REST is an architectural style for designing networked applications around resources, representations, standard HTTP semantics, stateless interactions, and a uniform interface.

### Follow-up

**Q: Is every HTTP API RESTful?**

No.

An API can use HTTP without following REST principles consistently.

### Follow-up

**Q: What makes your API REST-oriented?**

Discuss:

```text
resource-oriented URIs
HTTP method semantics
representations
stateless requests
standard HTTP status codes
```

Do not claim “we use JSON, therefore it is REST.”

---

# 3. ATTACK — WHAT IS AN HTTP REQUEST?

## Q2. Explain an HTTP request.

Conceptually:

```text
Method
Target / URI
Headers
Optional body
```

Example:

```http
GET /hotels/42 HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Bearer <token>
```

### Follow-up

**Q: Is the body mandatory?**

No.

Some methods commonly carry bodies, but HTTP requests can have no body.

### Follow-up

**Q: Does GET never have a body?**

Do not give a simplistic “HTTP forbids it” answer.

The important API-design point is that GET request bodies are not generally useful/reliably supported for normal resource retrieval semantics, so query parameters are typically used for filters/search criteria.

---

# 4. ATTACK — GET VS POST

## Q3. GET vs POST?

### GET

Used for retrieval.

Semantically:
- safe
- idempotent

### POST

Used to submit/process data.

Commonly:
- create resources
- trigger operations
- submit commands

POST is not inherently idempotent.

### Follow-up

**Q: Why is POST generally non-idempotent?**

Because repeating it may create multiple effects/resources.

Example:

```text
POST /orders
```

could create two orders if sent twice.

### Follow-up

**Q: Can POST be made idempotent?**

At the application/API level, yes, for example through an idempotency key and server-side deduplication.

Do not confuse HTTP's method semantics with an application's extra idempotency mechanism.

---

# 5. ATTACK — PUT VS PATCH

## Q4. PUT vs PATCH?

### PUT

Usually represents replacement semantics for the target resource.

### PATCH

Represents partial modification.

Example:

```http
PUT /users/42
```

could represent the complete intended representation.

```http
PATCH /users/42
```

could change only:

```json
{
  "email": "new@example.com"
}
```

### Follow-up

**Q: Is PATCH idempotent?**

Not inherently. It depends on the patch operation.

Example:

```text
increment balance by 10
```

is not idempotent if repeated.

Whereas:

```text
set status = ACTIVE
```

can be idempotent.

### Trap

Never say:

> “PUT updates and PATCH updates partially, so they are basically the same.”

The semantic distinction matters for API design.

---

# 6. ATTACK — SAFE VS IDEMPOTENT

## Q5. What is a safe HTTP method?

A safe method is intended not to change server state as part of the requested operation.

## Q6. What is idempotency?

Repeating the same request has the same intended effect as performing it once.

### Classic trap

> “Idempotent means same response every time.”

Wrong.

Example:

```text
DELETE /hotels/42
```

First request:

```text
204
```

Later request:

```text
404
```

The responses can differ while the intended state-changing effect is not repeatedly multiplied.

### Follow-up

**Q: Can a GET endpoint accidentally change a database?**

Yes, badly designed server code can perform side effects.

But that does not change the intended HTTP semantics of GET.

---

# 7. ATTACK — DELETE IDEMPOTENCY

## Q7. Is DELETE idempotent?

Generally yes in HTTP semantics.

But this does **not** require every repeated DELETE request to return the same status.

```text
DELETE /hotels/42

1st → 204
2nd → 404
```

The important point is the resulting intended state, not identical response bodies/statuses.

---

# 8. ATTACK — STATUS CODES

## Q8. 200 vs 201 vs 204?

```text
200 → successful request with response representation
201 → resource successfully created
204 → successful request with no response body
```

### Follow-up

**Q: Why return 201 after POST?**

Because the operation created a resource and HTTP has a specific status for that outcome.

A `Location` header may identify the newly created resource.

---

# 9. ATTACK — 400 / 401 / 403 / 404 / 409

## Q9. Explain the differences.

### 400

Request is invalid/malformed or fails request-level validation.

### 401

Authentication is missing/invalid.

```text
Who are you?
```

### 403

Request understood, but authorization is insufficient.

```text
You are known, but not allowed.
```

### 404

Requested resource/endpoint was not found.

### 409

Request conflicts with the current state.

Examples:
- uniqueness conflict
- invalid state transition
- concurrency/state conflict

### Interview trap

Do not say:

> “401 means permission denied.”

That is the common confusion.

---

# 10. ATTACK — 500 VS 502 VS 503

## Q10. Difference?

### 500

Unexpected failure inside the server handling the request.

### 502

A gateway/proxy received an invalid response from an upstream server.

### 503

Service is currently unavailable, overloaded, or temporarily unable to serve.

### Microservice follow-up

If:

```text
Gateway
  ↓
User Service
  ↓
Rating Service
```

and Rating Service fails, the correct status depends on where and how the failure is handled.

Do not mechanically return `500` for every downstream problem.

---

# 11. ATTACK — CONTENT-TYPE VS ACCEPT

## Q11. Difference?

```text
Content-Type
→ media type of the representation being sent

Accept
→ media types the client is willing to receive
```

Example:

```http
Content-Type: application/json
Accept: application/json
```

### Follow-up

**Q: What happens if Content-Type is unsupported?**

Spring MVC can reject the request with an HTTP media-type-related error rather than successfully deserializing it.

### Follow-up

**Q: What if the server cannot produce a representation acceptable to the client?**

That can result in `406 Not Acceptable`.

This is where content negotiation becomes relevant.

---

# 12. ATTACK — STATELESS REST

## Q12. What does stateless mean?

Each request contains the information necessary for the server to process it; the server does not rely on conversational session state stored from previous requests.

### Trap

> “Stateless means the server cannot store anything.”

Wrong.

A stateless API can still use:

```text
database
cache
files
message broker
```

The restriction is on dependency on client-specific conversational state between requests.

### Project answer

JWT-based requests can carry authentication information without requiring the server to maintain the same traditional session state for every client interaction.

---

# 13. ATTACK — @RestController

## Q13. `@Controller` vs `@RestController`?

`@RestController` is effectively a convenience combination of controller semantics with response-body semantics.

Conceptually:

```text
@Controller
+
@ResponseBody
```

### Follow-up

**Q: Why does that matter?**

A REST controller normally returns data that should be serialized into the HTTP response rather than resolving a server-side view.

---

# 14. ATTACK — @PathVariable VS @RequestParam

## Q14. Difference?

### `@PathVariable`

Identifies part of the resource path.

```text
GET /hotels/42
```

```java
@PathVariable Long id
```

### `@RequestParam`

Represents query parameters.

```text
GET /hotels?city=Delhi&page=0
```

```java
@RequestParam String city
```

### Mental model

```text
PathVariable → WHICH resource?
RequestParam → HOW to filter/search/query?
```

Not an absolute law, but a useful API-design rule.

---

# 15. ATTACK — @RequestHeader

## Q15. Why use `@RequestHeader`?

To access HTTP header values.

Example:

```java
@RequestHeader("Authorization")
String authorization
```

### Follow-up

**Q: Should you manually parse JWT in every controller?**

No.

Authentication/security concerns should generally be handled by the security infrastructure rather than duplicated in every controller.

---

# 16. ATTACK — @RequestBody

## Q16. What exactly happens with `@RequestBody`?

Spring MVC reads the request body and deserializes it into the declared Java type through an `HttpMessageConverter`. citeturn0search12turn0search0

Conceptually:

```text
HTTP body
   ↓
HttpMessageConverter
   ↓
JSON converter
   ↓
Jackson
   ↓
Java DTO
```

### Follow-up

**Q: Is `@RequestBody` itself the JSON parser?**

No.

It tells Spring MVC that the method argument should be bound from the request body. The message-conversion infrastructure performs the actual conversion.

---

# 17. ATTACK — HTTP MESSAGE CONVERTERS

## Q17. What is `HttpMessageConverter`?

An abstraction used by Spring MVC to read/write HTTP message bodies.

Official Spring documentation describes converters as responsible for reading and writing request/response bodies. citeturn0search0

### Follow-up

**Q: Why not just use Jackson directly?**

Because Spring MVC provides a general abstraction that supports multiple representations and media types.

```text
Controller
   ↓
HTTP message abstraction
   ↓
appropriate converter
   ↓
representation
```

### Follow-up

**Q: Which converter handles JSON?**

For current Spring Framework generations, the exact Jackson converter naming differs by major version. In Spring Framework 6.2, `MappingJackson2HttpMessageConverter` is the familiar JSON converter; current Spring Framework documentation also documents `JacksonJsonHttpMessageConverter` for the newer Jackson 3-based setup. citeturn0search3turn0search0

### Interview rule

Do not blindly recite a converter class name without knowing which Spring version your project uses.

---

# 18. ATTACK — HOW DOES SPRING CHOOSE A CONVERTER?

## Q18. How does Spring decide which converter to use?

Conceptually:

```text
Java target type
+
request/response media type
+
available converters
        ↓
compatible converter
        ↓
read/write representation
```

For incoming requests, `Content-Type` is important.

For responses, acceptable media types from `Accept` participate in content negotiation.

### Follow-up

**Q: What if no suitable converter exists?**

The request/response cannot be converted and Spring can produce an appropriate media-type/message-conversion error.

---

# 19. ATTACK — JSON SERIALIZATION VS DESERIALIZATION

## Q19. What is serialization?

```text
Java object
   ↓
JSON
```

## Q20. What is deserialization?

```text
JSON
   ↓
Java object
```

### Interview trap

Candidates often reverse these two.

Memory:

```text
SERIALIZE   → send object OUT
DESERIALIZE → bring data INTO object
```

---

# 20. ATTACK — DISPATCHERSERVLET

## Q21. What is DispatcherServlet?

The central Front Controller of Spring MVC.

It receives the request into the Spring MVC pipeline and coordinates handler resolution, invocation, argument/return-value processing, message conversion, and exception handling.

Spring's MVC documentation explicitly identifies `DispatcherServlet` as a core part of the MVC architecture. citeturn0search1

### Follow-up

**Q: Why Front Controller?**

Instead of every controller implementing its own complete request-processing infrastructure, requests enter a common centralized pipeline.

---

# 21. ATTACK — HANDLERMAPPING VS HANDLERADAPTER

## Q22. Difference?

```text
HandlerMapping
→ determines the handler

HandlerAdapter
→ invokes the selected handler
```

### Attack

**Q: Why do we need an adapter?**

Because Spring MVC supports different handler/controller styles and needs a common invocation abstraction.

Do not say:

> “HandlerAdapter finds the controller.”

That is HandlerMapping's job.

---

# 22. ATTACK — COMPLETE MVC REQUEST LIFECYCLE

## Q23. Walk me through one request.

Strong answer:

```text
Client
 ↓
HTTP request
 ↓
Servlet container / Tomcat
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
HandlerAdapter
 ↓
argument resolution
 ↓
@RequestBody conversion if required
 ↓
validation
 ↓
controller
 ↓
service
 ↓
repository / Feign
 ↓
return value
 ↓
message conversion
 ↓
HTTP response
```

Spring MVC's documented architecture covers DispatcherServlet, annotated controllers, data binding, message conversion, validation, and error responses as parts of the web stack. citeturn0search1

### This is a P0 answer.

You should be able to draw it on a whiteboard without notes.

---

# 23. ATTACK — CONTROLLER VS SERVICE

## Q24. Why should a controller be thin?

Because the controller is an HTTP boundary, not the main location for business logic.

Good:

```text
Controller
 ↓
Service
 ↓
Repository / Feign
```

Bad:

```text
Controller
 ├── business rules
 ├── database logic
 ├── remote calls
 ├── retry logic
 └── huge transformations
```

### Follow-up

**Q: What exactly belongs in a controller?**

Generally:
- request binding
- validation trigger
- delegation
- HTTP-specific response handling

---

# 24. ATTACK — DTO VS ENTITY

## Q25. Why not return JPA entities directly?

Because persistence models and API contracts have different responsibilities.

DTOs help:
- hide persistence details
- control exposed fields
- avoid sensitive data leakage
- prevent API contracts from being tightly coupled to DB schema
- support different request/response models

### Follow-up

**Q: Isn't DTO just boilerplate?**

It can add mapping overhead, but that is often a deliberate architectural trade-off.

The correct answer is not:

> “DTO is always better.”

Instead:

> “For a public or evolving API, DTO separation often provides valuable contract and persistence decoupling. For very small internal APIs, direct entity exposure may sometimes be acceptable, but the trade-offs should be deliberate.”

---

# 25. ATTACK — REQUEST DTO VS RESPONSE DTO

## Q26. Why separate them?

Because the client-controlled input and server-controlled output usually have different semantics.

Example:

```text
CreateHotelRequest
→ name
→ location

HotelResponse
→ id
→ name
→ location
→ createdAt
```

The client should not automatically control generated fields.

### Follow-up

**Q: Can one DTO be used for both?**

Technically yes.

Architecturally, it depends on the API.

Do not turn a guideline into an absolute law.

---

# 26. ATTACK — VALIDATION

## Q27. How does validation work for `@RequestBody`?

Example:

```java
@PostMapping
public ResponseEntity<?> create(
        @Valid @RequestBody HotelRequest request) {
    ...
}
```

Conceptually:

```text
HTTP JSON
 ↓
DTO binding
 ↓
Bean Validation
 ↓
valid → controller
invalid → validation exception
```

Spring MVC supports Bean Validation on `@RequestBody` parameters annotated with `@Valid` or `@Validated`. citeturn0search5turn0search12

---

# 27. ATTACK — @Valid VS @Validated

## Q28. Difference?

`@Valid` is the standard Bean Validation trigger.

`@Validated` is Spring's variant and supports Spring-specific validation scenarios such as validation groups.

### Current-depth nuance

Modern Spring MVC distinguishes individual argument validation from method validation.

Depending on the method signature and constraints, validation can result in:

```text
MethodArgumentNotValidException
```

or:

```text
HandlerMethodValidationException
```

Spring's current documentation explicitly describes both cases. citeturn0search7

### Interview advantage

Knowing this is deeper than simply memorizing:

> "`@Valid` gives 400."

---

# 28. ATTACK — WHERE SHOULD VALIDATION HAPPEN?

## Q29. Input validation vs business validation?

### Input validation

```text
@NotBlank
@Email
@Min
```

Concerned with the shape/constraints of incoming data.

### Business validation

```text
Cannot cancel an already-completed booking.
```

Concerned with domain rules.

### Authorization

```text
Only ADMIN can delete.
```

Concerned with permission.

These are different layers.

---

# 29. ATTACK — EXCEPTION HANDLING

## Q30. How would you handle exceptions globally?

Use:

```java
@RestControllerAdvice
```

with:

```java
@ExceptionHandler
```

Spring documents `@ControllerAdvice` / `@RestControllerAdvice` as mechanisms for applying exception handlers across controllers. citeturn0search6

Conceptual flow:

```text
Exception
 ↓
Exception resolver / handler
 ↓
@ExceptionHandler
 ↓
structured response
 ↓
HTTP status
```

---

# 30. ATTACK — @CONTROLLERADVICE VS @RESTCONTROLLERADVICE

## Q31. Difference?

`@ControllerAdvice` provides global controller advice.

`@RestControllerAdvice` is the REST-oriented shortcut that combines controller advice with response-body semantics. citeturn0search6

### Follow-up

**Q: Does advice run before local exception handlers?**

Spring's resolution rules consider local controller handlers before global advice handlers in the relevant lookup path. citeturn0search6

Do not overstate the exact internal resolver algorithm unless asked.

---

# 31. ATTACK — WHAT SHOULD AN ERROR RESPONSE CONTAIN?

## Q32. Design an API error response.

Possible fields:

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

Exact structure is an API contract decision.

### Production principle

Public response:

```text
safe
stable
client-useful
```

Internal logs/traces:

```text
detailed
diagnostic
```

Never expose:
- secrets
- credentials
- internal stack traces
- unnecessary infrastructure information

---

# 32. ATTACK — MODERN ERROR RESPONSES

## Q33. What is ProblemDetail?

Modern Spring MVC supports the HTTP API error representation defined by RFC 9457 through `ProblemDetail` and related error-response infrastructure. citeturn0search11

### Why this matters

An interviewer may now ask:

```text
@RestControllerAdvice
vs
ProblemDetail
```

These are not mutually exclusive.

You can use:

```text
@ControllerAdvice / @RestControllerAdvice
        ↓
ProblemDetail
        ↓
HTTP error response
```

### Priority

Know conceptually.

You do not need to redesign your current project around it unless you choose to.

---

# 33. ATTACK — EXCEPTION VS LOGGING VS TRACING

## Q34. Are exception handling, logging and tracing the same?

No.

```text
Exception handling
→ what HTTP/API response should the caller receive?

Logging
→ what diagnostic event should be recorded?

Tracing
→ how did this request travel across components/services?
```

In your project:

```text
Exception handling
+
Resilience4j
+
Zipkin
+
application logs
```

solve different problems.

---

# 34. ATTACK — DOWNSTREAM FAILURE

## Q35. User Service calls Rating Service and Rating Service is down. What happens?

Strong answer:

```text
User Service
 ↓
Feign
 ↓
Rating Service unavailable
 ↓
timeout / connection failure
 ↓
resilience policy if configured
 ↓
fallback or propagated failure
 ↓
service decides API response
```

### Follow-up

**Q: Should you retry?**

Not automatically.

Consider:
- transient vs permanent failure
- idempotency
- retry count
- backoff
- downstream overload
- user-visible latency

This connects to the later Resilience4j module.

---

# 35. ATTACK — API GATEWAY VS CONTROLLER

## Q36. Why have API Gateway if the service already has controllers?

They operate at different boundaries.

```text
Gateway
→ system-level edge concerns

Service Controller
→ service-level HTTP/API boundary
```

Gateway may handle:
- routing
- authentication integration
- cross-cutting edge concerns
- rate limiting depending on architecture

Service controller handles the service's own API contract.

Do not move all business logic into the gateway.

---

# 36. ATTACK — 404 VS 405 VS 415 VS 406

These are excellent depth probes.

### 404

Target resource/handler not found.

### 405 Method Not Allowed

Path exists but the HTTP method is not supported for that endpoint.

### 415 Unsupported Media Type

Request representation/media type is unsupported.

Example:

```http
Content-Type: application/xml
```

when only JSON is accepted.

### 406 Not Acceptable

Server cannot produce a representation matching the client's acceptable media types.

These naturally connect to Spring MVC's request mapping and message-conversion infrastructure. Spring's error-response documentation lists media-type and method-related exceptions including `HttpMediaTypeNotSupportedException`, `HttpMediaTypeNotAcceptableException`, and `HttpRequestMethodNotSupportedException`. citeturn0search11

---

# 37. ATTACK — MALFORMED JSON

## Q37. What if the client sends invalid JSON?

Example:

```json
{
  "name": "Hotel"
```

The body cannot be successfully deserialized.

This is different from:

```text
valid JSON
+
invalid business/input constraint
```

The first is a message-conversion/readability problem; the second is validation.

Spring documents `HttpMessageNotReadableException` among its MVC error-response cases. citeturn0search11

---

# 38. ATTACK — VALID JSON BUT WRONG FIELD TYPE

Example:

```json
{
  "age": "abc"
}
```

when:

```java
int age;
```

This can fail during data binding/deserialization before business logic executes.

### Interview distinction

```text
Malformed/unreadable body
        ↓
message conversion/binding

Readable body but violates constraints
        ↓
validation

Valid input but violates domain rule
        ↓
business validation
```

This separation is very useful in interviews.

---

# 39. ATTACK — CONTENT NEGOTIATION

## Q38. What is content negotiation?

The client and server negotiate which representation should be used.

Client:

```http
Accept: application/json
```

Server selects a supported representation.

### Follow-up

**Q: What is `produces`?**

It can constrain what representations a controller mapping produces.

### Follow-up

**Q: What is `consumes`?**

It can constrain what request media types a mapping accepts.

Example:

```java
@PostMapping(
    consumes = "application/json",
    produces = "application/json"
)
```

This is a high-value practical question.

---

# 40. ATTACK — REQUEST MAPPING

## Q39. What happens if two controller methods match the same request?

Spring's mapping infrastructure attempts to select the most specific matching handler.

If mappings are ambiguous, application startup can fail because Spring cannot create an unambiguous mapping.

### Interview-safe answer

> Mapping conditions must resolve to an unambiguous handler; conflicting mappings should be diagnosed from the request path, HTTP method, params, headers, consumes and produces conditions.

Do not claim “first method wins.”

---

# 41. ATTACK — ARGUMENT RESOLUTION

## Q40. How does Spring populate `@PathVariable`, `@RequestParam`, etc.?

Spring MVC has method argument resolution infrastructure that examines controller method parameters and resolves them from the current request.

Conceptually:

```text
Controller method parameter
        ↓
argument resolver
        ↓
request data
        ↓
Java argument
```

This is a depth question.

You do not need to memorize every resolver class before mastering the core lifecycle.

---

# 42. ATTACK — RESPONSE CONVERSION

## Q41. Controller returns a Java DTO. How does client receive JSON?

```text
Controller return value
 ↓
Spring MVC return-value handling
 ↓
HttpMessageConverter
 ↓
Jackson / appropriate representation converter
 ↓
HTTP response body
```

The same message-conversion abstraction is used for writing response bodies. citeturn0search0

---

# 43. ATTACK — RESPONSEENTITY

## Q42. Why use `ResponseEntity`?

It gives explicit control over:

```text
status
headers
body
```

Example:

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(response);
```

### Follow-up

**Q: Is ResponseEntity mandatory for REST APIs?**

No.

A controller can return a body directly and let Spring determine the response handling.

Use `ResponseEntity` when explicit response control improves clarity.

---

# 44. ATTACK — THIN CONTROLLER

## Q43. What happens if controller contains all business logic?

Problems can include:

```text
poor separation
harder testing
duplication
coupling to HTTP
large classes
harder reuse
```

### Follow-up

**Q: Should service layer know HTTP?**

Ideally, domain/business services should not depend heavily on HTTP-specific concerns.

This keeps business logic reusable and testable outside the web layer.

---

# 45. ATTACK — DTO MAPPING

## Q44. Why map DTO → Entity → DTO?

Because each layer has a different responsibility.

```text
Request DTO
   ↓
Service
   ↓
Entity
   ↓
Persistence
```

and:

```text
Entity
   ↓
Service
   ↓
Response DTO
   ↓
HTTP
```

### Follow-up

**Q: ModelMapper vs manual mapping?**

Trade-off:

```text
automatic mapper
→ less repetitive code
→ less explicit control

manual mapping
→ more verbose
→ more explicit
→ easier to reason about unusual mappings
```

The correct choice depends on complexity and team conventions.

---

# 46. ATTACK — VALIDATION VS AUTHORIZATION

## Q45. Is `@Valid` authorization?

No.

```text
Validation
→ Is the data acceptable?

Authorization
→ Is this principal allowed to perform this operation?
```

Example:

```text
@NotBlank name
```

is validation.

```text
ADMIN can delete hotel
```

is authorization.

---

# 47. ATTACK — FULL PROJECT WHITEBOARD QUESTION

## Q46. "Explain GET /users in your project."

This is one of the strongest questions you can receive because it lets you connect multiple modules.

Answer:

```text
Client
 ↓
API Gateway
 ↓
User Service
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
Controller
 ↓
User Service business logic
 ↓
Repository / database
 ↓
Feign → Rating Service
 ↓
hotel IDs
 ↓
Feign → Hotel Service
 ↓
aggregate data
 ↓
response DTO
 ↓
HttpMessageConverter
 ↓
JSON
 ↓
Client
```

Then mention resilience/tracing only when relevant:

```text
Feign call
 ↓
Resilience4j policy
 ↓
fallback / failure
```

```text
request
 ↓
distributed tracing
 ↓
Zipkin
```

This answer proves practical knowledge instead of isolated annotation memorization.

---

# 48. HIGH-VALUE CROSS-QUESTION TREES

## Tree A — `@RequestBody`

```text
@RequestBody
 ↓
How does JSON become Java?
 ↓
HttpMessageConverter
 ↓
Which converter?
 ↓
Jackson
 ↓
How selected?
 ↓
Content-Type + supported media type + target type
 ↓
Malformed JSON?
 ↓
message-conversion/readability failure
 ↓
Valid JSON but invalid constraints?
 ↓
validation failure
 ↓
How return error?
 ↓
@RestControllerAdvice / ProblemDetail / ErrorResponse
```

## Tree B — HTTP status

```text
400
 ↓
Why?
 ↓
malformed / invalid input
 ↓
Is it authentication?
 ↓
401
 ↓
Is it authorization?
 ↓
403
 ↓
resource missing?
 ↓
404
 ↓
state conflict?
 ↓
409
```

## Tree C — Spring MVC

```text
Tomcat
 ↓
DispatcherServlet
 ↓
HandlerMapping
 ↓
HandlerAdapter
 ↓
argument resolution
 ↓
Controller
 ↓
Service
 ↓
Repository / Feign
 ↓
return value
 ↓
message conversion
 ↓
HTTP response
```

## Tree D — downstream failure

```text
Controller
 ↓
Service
 ↓
Feign
 ↓
dependency fails
 ↓
timeout / exception
 ↓
retry?
 ↓
idempotent?
 ↓
transient?
 ↓
circuit breaker?
 ↓
fallback?
 ↓
stable HTTP response
```

## Tree E — DTO

```text
Why DTO?
 ↓
API contract vs persistence model
 ↓
Security?
 ↓
Avoid leaking fields
 ↓
Coupling?
 ↓
API independent of DB model
 ↓
Request vs response DTO?
 ↓
Different client/server responsibilities
```

---

# 49. TOP 20 QUESTIONS TO MASTER FIRST

```text
1. What is REST?
2. GET vs POST?
3. PUT vs PATCH?
4. Safe vs idempotent?
5. 401 vs 403?
6. 404 vs 409?
7. 200 vs 201 vs 204?
8. Content-Type vs Accept?
9. What does stateless mean?
10. What is DispatcherServlet?
11. Explain the Spring MVC request lifecycle.
12. HandlerMapping vs HandlerAdapter?
13. @PathVariable vs @RequestParam?
14. How does @RequestBody work?
15. What is HttpMessageConverter?
16. DTO vs Entity?
17. How does @Valid work?
18. @ControllerAdvice vs @RestControllerAdvice?
19. How would you handle a downstream microservice failure?
20. Explain one complete request from your Hotel Review System.
```

---

# 50. RED-FLAG ANSWERS TO DELETE

Avoid:

> "REST means JSON APIs."

> "POST means create."

> "PUT and PATCH are same."

> "401 means forbidden."

> "Stateless means no database."

> "`@RequestBody` uses Jackson directly."

> "`@Valid` validates everything."

> "Controller should contain business logic."

> "DTO is always better."

> "All exceptions should become 500."

> "Retry every failed request."

> "Global exception handling means try/catch everywhere."

> "DispatcherServlet directly calls the controller."

> "HandlerMapping and HandlerAdapter are the same thing."

These invite immediate follow-ups.

---

# 51. 30-SECOND MASTER ANSWER

> "For a Spring Boot REST API, an HTTP request reaches the servlet container and enters Spring MVC through DispatcherServlet. Spring resolves the appropriate handler, binds path/query/body data to controller method parameters, and uses HttpMessageConverters for request and response body conversion. The controller should remain thin and delegate business logic to the service layer, which can access persistence or call other services through Feign. DTOs separate the API contract from persistence models, validation handles input constraints, and centralized exception handling provides a stable HTTP error contract. In a microservice system, downstream failures additionally require resilience and observability rather than simply exposing raw exceptions."

---

# 52. PROJECT DEFENCE — QUESTIONS THAT PROVE EXPERIENCE

Be ready for:

```text
Why did you use DTOs?
Why is controller thin?
How does your User Service aggregate data?
Why Feign?
What happens when Rating Service is down?
What happens when Hotel Service is slow?
How does validation fail?
What HTTP status do you return?
How do you handle exceptions globally?
How does JSON become Java?
How does Java become JSON?
Why does localhost break in Docker?
Why use config-server hostname?
How do you trace one request?
```

Your strongest answer is not a textbook definition.

It is:

```text
Concept
+
actual implementation
+
failure encountered
+
how you diagnosed it
+
why the final design exists
```

---

# 53. MODULE 2 — INTERVIEW READINESS CHECKLIST

## 🔴 P0

- [ ] REST
- [ ] HTTP request/response
- [ ] GET/POST
- [ ] PUT/PATCH
- [ ] safe/idempotent
- [ ] DELETE semantics
- [ ] status codes
- [ ] Content-Type/Accept
- [ ] statelessness
- [ ] `@RestController`
- [ ] `@PathVariable`
- [ ] `@RequestParam`
- [ ] `@RequestBody`
- [ ] DispatcherServlet
- [ ] MVC request lifecycle
- [ ] DTO vs Entity
- [ ] validation
- [ ] exception handling

## 🟠 P1

- [ ] HandlerMapping
- [ ] HandlerAdapter
- [ ] HttpMessageConverter
- [ ] Jackson
- [ ] content negotiation
- [ ] `@Valid` / `@Validated`
- [ ] `@ControllerAdvice`
- [ ] `@RestControllerAdvice`
- [ ] `ResponseEntity`
- [ ] downstream failure

## 🟡 P2

- [ ] argument resolution
- [ ] method validation
- [ ] `MethodArgumentNotValidException`
- [ ] `HandlerMethodValidationException`
- [ ] ProblemDetail
- [ ] 405 / 415 / 406
- [ ] malformed JSON vs validation failure
- [ ] mapping ambiguity

## Project proof

- [ ] explain `/users`
- [ ] explain Feign failure
- [ ] explain Docker localhost failure
- [ ] explain DTO boundary
- [ ] explain validation path
- [ ] explain global error handling
- [ ] explain request-to-response pipeline

---

# 54. SOURCE / EVIDENCE NOTES

## Primary technical authority

Spring Framework:
- Spring MVC architecture citeturn0search1turn0search10
- HTTP message conversion citeturn0search0turn0search3
- `@RequestBody` and validation citeturn0search12turn0search7
- validation infrastructure citeturn0search5
- controller advice / exception handling citeturn0search6
- modern error responses / ProblemDetail citeturn0search11

## Interview-pattern calibration

A recent 2026 Spring Boot REST interview guide specifically highlights recurring questions around:
- `@RestController`
- `@PathVariable` vs `@RequestParam`
- `ResponseEntity`
- validation
- global exception handling
- DTO vs entity
- statelessness
- status codes. citeturn0search4

This is used as recurrence calibration, not as proof of universal interviewer frequency.

---

# 55. FINAL MEMORY MAP

```text
HTTP
│
├── Methods
│   ├── GET
│   ├── POST
│   ├── PUT
│   ├── PATCH
│   └── DELETE
│
├── Semantics
│   ├── safe
│   └── idempotent
│
├── Representation
│   ├── Content-Type
│   └── Accept
│
└── Status
    ├── 2xx
    ├── 4xx
    └── 5xx

Spring MVC
│
├── Tomcat
├── DispatcherServlet
├── HandlerMapping
├── HandlerAdapter
├── Argument Resolution
├── Controller
├── Validation
├── Service
├── Repository / Feign
└── HttpMessageConverter

API Design
│
├── Resource URI
├── DTO
├── Validation
├── ResponseEntity
└── Error Contract

Microservices
│
├── Feign
├── Downstream failure
├── Resilience
├── Gateway
└── Distributed tracing
```

**Module 2 — Interview Attack KB: COMPLETE.**
