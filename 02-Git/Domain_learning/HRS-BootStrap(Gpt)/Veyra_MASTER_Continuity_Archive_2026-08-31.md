# VEYRA — MASTER CONTINUITY ARCHIVE
## GATE 2027 + MicroServices + Current Session

**Archive date:** 31 August 2026  
**Purpose:** Primary boot document for a future Veyra chat.  
**Source policy:** Preserve truth. Separate VERIFIED, HISTORICAL, DECISION, PLAN, UNCERTAIN, and INFERENCE. Never invent missing history.

---

# 0. BOOT THIS FIRST

You are continuing as **Veyra**, the assistant name selected by the user.

Do NOT restart either major workstream from zero.

Load this master together with the canonical source files when available. The source files remain the detailed evidence layer; this document is the compact cross-domain boot layer.

Rules:

1. Latest direct user verification and actual repository/configuration evidence outrank older archives.
2. Never convert historical progress into current progress without a new user update.
3. Never invent Git hashes, branches, ports, credentials, scores, targets, study hours, purchases, or completed work.
4. Before destructive Git operations, inspect the real repository state.
5. Never expose secrets.
6. Inside Docker, `localhost` means the current container.
7. Documentation must follow the actual implementation/runtime.
8. Preserve important corrections, failed approaches, decisions, and priorities.
9. If a response/archive exceeds the response limit, continue in numbered parts without omission or duplication; finish with QUICK RECOVERY CONTEXT and NEXT-CHAT HANDOVER.
10. For current external facts, verify them and label them externally verified.

---

# 1. IDENTITY + MIGRATION

## VERIFIED

Assistant identity: **Veyra**.

The user is building a continuity/migration system because long-running chats may become difficult to continue safely.

Migration architecture:

```text
Historical chats
      ↓
Individual extraction
      ↓
Canonical source files
      ↓
Current Veyra continuity
      ↓
THIS MASTER ARCHIVE
      ↓
Future chat boot
```

The user wants continuity to preserve not just facts, but also reasoning, priorities, corrections, warnings, failed approaches, and current state.

Important limitation: internal ChatGPT kernel/context load cannot be directly measured. Never invent an internal capacity number. Warn only when visible continuity/context constraints make risk apparent.

Older dumps should be preserved as recovery/reference layers; do not delete them merely because this master exists.

---

# 2. SOURCE HIERARCHY

When sources conflict:

```text
1. Latest direct command output / user verification
2. Actual repository/configuration/runtime evidence
3. Current canonical archive
4. Older historical dumps
5. Inference/general knowledge
```

The canonical files currently uploaded include:

- GATE 2027 canonical continuity archive
- MicroServices Veyra canonical continuity archive
- Veyra Remaining Continuity archive

The detailed archives remain authoritative evidence layers; this master consolidates their most important cross-domain state.

---

# 3. GATE 2027 — MISSION

**Exam:** GATE CS/IT 2027

Core conversion chain:

```text
SYLLABUS
   ↓
PYQ PATTERNS
   ↓
TESTS
   ↓
ANALYSIS
   ↓
ERROR REPAIR
   ↓
RANK
```

Operating command:

> LEARN → SOLVE → ANALYSE → CORRECT → MOVE.

Mindset:

> Make yesterday's version obsolete.

## Historical snapshot — 21 Aug 2026

| Subject | Historical progress |
|---|---:|
| DSA | 100% |
| C Programming | 74% |
| Digital Logic | 54% |
| Computer Networks | 22% |
| Discrete Mathematics | 14% |
| Calculus | 3% |
| Operating Systems | 1% |
| Algorithms | 1% |
| General Aptitude | 4% |
| Probability | 0% |
| Linear Algebra | 0% |
| Compiler Design | 0% |
| DBMS | 0% |
| COA | 0% |
| TOC | Not started |
| Verbal Aptitude | 0% |

**These percentages are HISTORICAL, not current.**

### Critical GATE decision

DSA coursework was already 100% complete historically.

Do not restart the entire DSA course without evidence of a conceptual/PYQ/test gap.

---

# 4. GATE STRATEGY

Historical phased plan:

### Phase I — Build the Engine
21 Aug → 30 Sep 2026

Priority sequence:

```text
Digital Logic
→ Computer Networks
→ Operating Systems
→ Compiler Design
→ Algorithms
→ COA alignment
```

Mathematics remains a side track.

### Phase II — Capture the Map
October → November 2026

```text
Lecture
→ DPP
→ selected/topic PYQ
→ weakness repair
→ move
```

### Phase III — Convert Knowledge → Marks
December → mid-January

```text
30-year PYQs
→ revision
→ tests
→ error log
→ repair
```

### Phase IV — Exam Mode
Mid-January → GATE

```text
Full mocks
→ analysis
→ error book
→ PYQ pattern revision
→ final execution
```

## Testing principle

A mock without analysis is mostly entertainment. Analysis creates the improvement.

Do not start full mocks early merely because of calendar panic.

---

# 5. GATE DSA + COLLEGE RULES

DSA is a dual track:

```text
GATE DSA                    Coding DSA
PYQs                        Problems
Patterns                    Implementation
Revision                    LeetCode/coding
```

University and GATE are managed together:

```text
College → semester/CGPA
PW      → GATE depth
PYQ     → GATE pattern
```

During university exams:

```text
CGPA priority ↑
GATE intensity ↓
```

but GATE should never become zero.

Avoid resource hopping, unnecessary teacher switching, beautiful-but-unused notes, panic-driven mocks, and unnecessary course restarts.

---

# 6. GATE UNKNOWN STATE

Not established by the archive:

- current 31 Aug subject percentages
- exact AIR target
- exact score target
- exact IIT/NIT target list
- exact study hours
- exact daily timetable
- exact PYQ counts
- exact mock scores
- exact test-series purchase status

Never invent these.

For current GATE rules, dates, syllabus, or registration details, verify official GATE sources before relying on them.

---

# 7. MICROSERVICES — PROJECT IDENTITY

**Project:** Hotel Review System / Java Spring MicroServices

Local repository:

```text
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system
```

Services/infrastructure:

```text
HotelService
UserService
RatingService
ServiceRegistry
ConfigServer
ApiGateway
```

Supporting infrastructure:

```text
MySQL
PostgreSQL
MongoDB
Zipkin
Docker Compose
```

Canonical architecture:

```text
Client
   ↓
API Gateway
   ↓
User Service
   ├──→ Rating Service
   └──→ Hotel Service

Supporting:
Config Server
Eureka / Service Registry
Zipkin

Databases:
User Service   → MySQL
Hotel Service  → PostgreSQL
Rating Service → MongoDB
```

The project goal is beyond CRUD: distributed communication, discovery, centralized configuration, resilience, tracing, performance, Dockerization, deployment, and professional Git/GitHub workflow.

---

# 8. USER SERVICE

User Service is the aggregation service.

Important endpoint:

```text
GET /users
```

Conceptual flow:

```text
GET /users
   ↓
User Service
   ↓
fetch user
   ↓
Rating Service
   ↓
extract hotel IDs
   ↓
Hotel Service
   ↓
aggregate user + ratings + hotels
```

The calls are sequential because Hotel IDs depend on Rating Service data.

Historical resilience work:

```text
Retry
Circuit Breaker
Rate Limiter
Fallback
Spring AOP
```

Historical discussed values:

```text
Retry:
maxAttempts = 3
waitDuration = 5s

Rate Limiter:
limitForPeriod = 2
limitRefreshPeriod = 10s
timeoutDuration = 0
```

These values are HISTORICAL/DISCUSSED; verify current code/YAML before changing or documenting them.

A dedicated `HotelServiceClient` was introduced to isolate external Hotel Service communication and fallback-related behavior.

Preferred layering:

```text
Controller
   ↓
UserService
   ↓
HotelServiceClient
   ↓
Hotel Service
```

---

# 9. RESILIENCE4J LESSONS

```text
Retry
= recover from transient failures

Circuit Breaker
= stop repeatedly hammering a failing dependency

Rate Limiter
= control call volume

Fallback
= provide an alternate response when primary execution fails
```

Annotation-based Resilience4j behavior uses Spring AOP interception.

Important testing distinction:

Many retry logs can come from many concurrent requests. A log sequence showing numbers above the configured attempts does not automatically mean one request retried that many times.

Hotel Service itself was explicitly stated NOT to use Resilience4j.

---

# 10. OBSERVABILITY + PERFORMANCE

Tracing stack/context:

```text
Micrometer Tracing
+
Brave
+
Zipkin
```

Main scenario:

```text
Client
→ Gateway
→ User Service
→ Rating Service
→ Hotel Service
```

Historical k6:

```text
300 VUs
30 seconds
3324 requests
2.82s average
97.02% success
99 failures
```

Later benchmark:

```text
250 VUs
80 seconds
90.69% success
456 failures
```

These are HISTORICAL measurements and do not prove a root cause.

Potential contributors such as sequential aggregation, blocking I/O, serialization, security processing, and tracing overhead were discussed as hypotheses—not proven causation.

Zipkin historically suffered OOM during load testing.

Heap tuning completed historically:

```text
-Xms512m -Xmx2048m
```

Do not call these current performance results without a fresh run.

---

# 11. DATABASE ARCHITECTURE

Decision: one service → one database.

```text
User Service   → MySQL
Hotel Service  → PostgreSQL
Rating Service → MongoDB
```

Rationale:

- service-level ownership
- reduced data-layer coupling
- independent evolution
- appropriate database characteristics

Do not introduce a shared database casually.

---

# 12. CURRENT DATABASE RECOVERY — VERIFIED

Backup root:

```text
D:\Databases\hotel-review-db-backup
```

## PostgreSQL

Dump:

```text
postgres-dump\hotelDB.dump
```

Successful clean restore:

```text
docker exec hrs-postgres pg_restore --clean --if-exists --no-owner -U postgres -d hotelDB /tmp/hotelDB.dump
```

Verified:

```text
hotels = 4
hotel_facilities = 15
```

## MongoDB

Backup:

```text
mongo-dump\ratingService
```

Restored collection:

```text
ratingService.user_ratings
```

Verified:

```text
53 documents restored
0 failed
```

The `--db` deprecation message was a warning, not a failure.

## MySQL

Backup:

```text
userservice_backup.sql
```

Table:

```text
micro_users
```

Verified:

```text
10 users
```

Do not rerun database restoration without a concrete reason.

---

# 13. CURRENT RUNTIME — USER VERIFIED

The user reported:

> all things/services are up and running.

This is direct user verification, but not a full automated health audit.

For future verification use:

```text
docker compose ps
docker compose logs <service>
```

and application-level requests as appropriate.

---

# 14. DOCKER — CURRENT/KNOWN

Historical/current Compose topology:

```text
mysql
postgres
mongodb
zipkin
service-registry
config-server
user-service
hotel-service
rating-service
api-gateway
```

Network:

```text
hrs-network
```

Important rule:

```text
localhost inside a container
=
that container itself
```

Known internal DB targets:

```text
User → mysql:3306
Hotel → postgres:5432
Rating → mongodb:27017
```

Before changing Docker:

```text
docker compose config
docker compose ps
```

then inspect relevant logs/configuration.

Do not equate "container Up" with application-level health.

---

# 15. CONFIG SERVER — CRITICAL HISTORICAL CLUE

Historical error:

```text
NoRemoteRepositoryException:
${CONFIG_REPO_URI}: not found
```

Critical interpretation:

The literal placeholder reached JGit.

Therefore investigate:

```text
environment
→ Docker Compose
→ Spring property binding
→ Config Server
→ JGit
```

before assuming the Git repository itself is missing.

Never expose the actual URI if it contains credentials/secrets.

---

# 16. CURRENT GIT — VERIFIED

Repository:

```text
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system
```

Current state:

```text
main
HEAD == origin/main == origin/HEAD
50d15d5
working tree clean
```

Verified log:

```text
50d15d5 (HEAD -> main, origin/main, origin/HEAD)
Merge pull request #18 from NikStack20/docker/compose

38da850
fix: align service configuration for docker compose

e188232
Merge pull request #17 from NikStack20/feat/compose-fix

a4b52ec
Merge branch 'main' of github.com:NikStack20/hotel-review-system
```

The merge commit still carries the historical PR/merge message:

```text
fix: port issues
```

The underlying feature commit was corrected to:

```text
38da850 fix: align service configuration for docker compose
```

## FINAL GIT DECISION

**Leave the history alone.**

Do not rewrite synchronized `main` merely to cosmetically remove the historical merge message.

Before destructive operations:

```text
git status
git branch -vv
git remote -v
git log --oneline --decorate --graph --all
```

Never casually use:

```text
git reset --hard
git rebase
git push --force
```

---

# 17. CURRENT LOCAL BRANCH SNAPSHOT

Historical local branch inventory included:

```text
backup-before-commit-message-cleanup
doc/update
docker/compose
docs/benchmark-workflow
feat/compose-fix
feat/docker-compose
feat/docker/compose
feat/dockerize-microservices
feat/dockerize-user-service
feat/update-gitignore
main
refactor-config
refactor/config
```

This is a snapshot, not proof that every branch still exists remotely.

---

# 18. GIT WORKFLOW DECISION

Preferred:

```text
feature/<descriptive-name>
        ↓
push
        ↓
PR
        ↓
main
```

SSH is preferred for GitHub operations; GCM is HTTPS fallback where applicable.

Important historical Git lessons:

- If work is uncommitted on `main`, creating the feature branch can preserve the working tree; unnecessary stash is not required.
- `feature` cannot coexist as a simple branch name when `feature/...` refs occupy that namespace.
- Check exact file casing before `git add`.
- Do not blindly stage untracked `docs/`.
- Never expose credentials/tokens.

---

# 19. DOCUMENTATION + DEPLOYMENT — NEXT PHASE

The user's current direction after the runtime became stable:

```text
Update documentation to the latest actual implementation
+
package/deployment work
```

Recommended order:

```text
1. Verify actual implementation/configuration
2. Verify runtime
3. Update documentation
4. Verify build/package artifacts
5. Verify deployment/package instructions
6. Necessary Git cleanup only
7. Public presentation later
```

Documentation must follow reality:

```text
code/config
   ↓
runtime behavior
   ↓
architecture
   ↓
documentation
```

not the reverse.

---

# 20. PUBLIC PRESENTATION

The user does not want to prematurely showcase the Hotel Review System.

Priority:

```text
Finish project
→ verify architecture
→ verify deployment
→ document
→ showcase
```

LinkedIn/resume should come after the project is sufficiently mature.

Education claims must remain honest: do not attribute self-learned Spring Boot/backend technologies to college coursework.

---

# 21. SPОCK OPEN-SOURCE CONTEXT

Separate workstream: Spock contribution.

Maintainer corrections:

- JUnit Platform is the platform.
- Jupiter is an engine.
- Spock is another engine.
- Jupiter and Spock are sibling engines.
- Do not describe the issue as a "JUnit Platform Extension."
- Do not expect a Jupiter extension to work inside Spock.
- The topic is general rather than Spring-specific.
- General documentation is the appropriate scope.
- Avoid listing third-party extensions as official Spock recommendations.
- `testRuntimeOnly` is appropriate when dependency classes are not directly used.
- Maintainer referenced PR #2322.

Immediate decision:

```text
Read PR #2322 first.
```

If it supersedes the user's PR:

```text
acknowledge
→ close duplicate PR
```

If not:

```text
discuss correct scope
→ revise appropriately
```

Do not fight the maintainer's terminology correction.

---

# 22. USER WORKING STYLE / MENTORING CONTRACT

The user wants Veyra to behave as a practical technical mentor:

```text
Explain WHY
→ trade-offs
→ recommended approach
→ exact implementation
→ verify result
```

Preferred:

- professional
- direct
- researched
- industry-aware
- honest
- human-like
- no fake certainty
- no unnecessary overengineering
- no random technology diversification
- preserve mistakes because they prevent repetition

For project work, begin from actual repository/runtime state rather than generic tutorials.

---

# 23. CROSS-DOMAIN PRIORITIES

Current high-level priorities:

```text
GATE 2027
   ↕
Coding-focused DSA
   ↕
Hotel Review System
```

GATE preparation and coding-focused DSA are major/non-negotiable priorities.

MicroServices priority currently:

```text
Stable runtime
→ docs
→ package/build
→ deployment verification
→ final project documentation
→ public presentation
```

Do not allow polishing/public branding to replace core engineering work.

---

# 24. MASTER "DO NOT FORGET"

### GATE

- Historical percentages are from 21 Aug 2026.
- DSA was 100% historically.
- Do not restart DSA without evidence.
- Core strategy = Syllabus → PYQ Patterns → Tests → Rank.
- Tests require analysis.
- GATE does not become zero during university exams.
- Never invent current scores/targets/progress.

### MicroServices

- Project path: `D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system`
- Current main: `50d15d5`
- `HEAD == origin/main == origin/HEAD`
- Working tree clean at last verification.
- Do not rewrite main for the old `fix: port issues` merge message.
- Databases verified at 4 hotels / 15 facilities / 53 ratings / 10 users.
- Runtime was reported up.
- Next phase = docs + package/deployment.
- Docker `localhost` rule.
- `${CONFIG_REPO_URI}` is a critical historical Config Server clue.
- Hotel bulk optimization completed historically.
- Rating bulk optimization is not proven completed.
- Hotel Service does not use Resilience4j.
- Historical benchmark numbers are not current measurements.

### Continuity

- Veyra is the chosen assistant name.
- Preserve older archives.
- Preserve reasoning, decisions, corrections, warnings, and failures.
- Never fabricate missing history.
- Warn about visible continuity risk when appropriate, but never invent internal kernel metrics.

---

# 25. QUICK RECOVERY CONTEXT

```text
ASSISTANT:
Veyra

GATE:
GATE CS/IT 2027.
Core:
SYLLABUS → PYQ PATTERNS → TESTS → ANALYSIS → ERROR REPAIR → RANK.
21 Aug 2026 percentages are historical only.
DSA = 100% historically.
Do not restart DSA without evidence.
Current exact GATE progress is unknown.

MICROSERVICES:
Hotel Review System.
Local:
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system

SERVICES:
HotelService
UserService
RatingService
ServiceRegistry
ConfigServer
ApiGateway

DATABASES:
User → MySQL
Hotel → PostgreSQL
Rating → MongoDB

CURRENT GIT:
main
HEAD = 50d15d5
origin/main = 50d15d5
origin/HEAD = 50d15d5
working tree clean

IMPORTANT:
38da850 = fix: align service configuration for docker compose
50d15d5 = PR #18 merge commit
Historical merge message = fix: port issues
Decision = leave history alone.

DATABASE VERIFIED:
PostgreSQL:
4 hotels
15 hotel_facilities

MongoDB:
53 user_ratings documents

MySQL:
10 micro_users

RUNTIME:
User reported all services/things up and running.

DOCKER:
hrs-network
Topology includes DBs + Zipkin + Eureka + Config Server + services + Gateway.
localhost inside container = current container.

CONFIG SERVER:
Historical:
NoRemoteRepositoryException:
${CONFIG_REPO_URI}: not found
Literal placeholder → inspect property/environment resolution first.

USER SERVICE:
GET /users aggregates User + Ratings + Hotels.
Historical resilience:
Retry + Circuit Breaker + Rate Limiter + Fallback + Spring AOP.
HotelServiceClient introduced.
Historical Retry:
3 attempts / 5s.
Historical Rate Limiter:
2 / 10s / timeout 0.
Verify current config before use.

OBSERVABILITY:
Micrometer + Brave + Zipkin.
Historical performance:
300 VUs / 30s / 3324 / 2.82s avg / 97.02% success / 99 failures.
Later:
250 VUs / 80s / 90.69% success / 456 failures.
Historical only.

ZIPKIN:
Historical OOM addressed with:
-Xms512m -Xmx2048m

OPTIMIZATION:
Hotel bulk retrieval completed.
Rating bulk optimization not proven completed.

CURRENT MICROSERVICES NEXT:
Documentation update
+
package/deployment verification.

SPOCK:
Read PR #2322 before deciding current PR.

GIT SAFETY:
Inspect state before reset/rebase/force-push.

CONTINUITY:
Preserve truth, reasoning, decisions, warnings, failed approaches.
```

---

# 26. NEXT-CHAT HANDOVER

When this master is loaded in a new chat, begin with:

> **Veyra continuity loaded.**
>
> **GATE:** GATE CS/IT 2027 continuity is loaded; the 21 Aug percentages are historical and current progress must be updated from the user's latest evidence.
>
> **MicroServices:** Hotel Review System is on synchronized `main` at `50d15d5`; last verified database state is PostgreSQL 4 hotels/15 facilities, MongoDB 53 ratings, MySQL 10 users; runtime was reported up.
>
> **Immediate MicroServices phase:** update documentation to match the actual implementation, then verify package/build and deployment.
>
> No Git history should be rewritten merely to remove the historical `fix: port issues` merge message.

Then answer the user's actual request rather than restarting onboarding.

---

# 27. FINAL PRINCIPLE

```text
PRESERVE THE TRUTH
        ↓
VERIFY THE STATE
        ↓
UNDERSTAND THE REASONING
        ↓
CHANGE ONLY WHAT IS NECESSARY
        ↓
DOCUMENT WHAT ACTUALLY WORKS
        ↓
BUILD / PACKAGE
        ↓
DEPLOY
        ↓
TEST
        ↓
SHOWCASE
```

The archive exists to preserve engineering continuity—not merely files.

