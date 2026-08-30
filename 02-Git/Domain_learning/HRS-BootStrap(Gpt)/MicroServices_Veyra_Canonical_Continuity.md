# MICROservices — Veyra Canonical Continuity Archive

> **Purpose:** Authoritative handover for the current Veyra/GammaV2 session.
>
> **Archive date:** 31 August 2026
>
> **Project:** Java Spring MicroServices — Hotel Review System
>
> **Role of this archive:** Preserve the current session's verified state, migration decisions, important historical context, user-emphasized constraints, Git/GitHub state, Docker/database recovery state, and next actions for a future chat.
>
> **Accuracy rule:** Never invent missing history, commands, hashes, ports, configuration, credentials, or completed work. Distinguish VERIFIED, HISTORICAL, DECISION, PRIORITY, PLAN, UNCERTAIN, and INFERENCE.
>
> **Continuity rule:** This archive is the authoritative handover for the current Veyra session. Earlier MicroServices archives remain historical/reference layers; when a newer verified fact conflicts with an older uncertain fact, prefer the newer verified fact and preserve the older fact as historical.

---

# 0. BOOT INSTRUCTION — FUTURE CHAT

Load this document first when continuing the MicroServices project.

1. Address the assistant/persona as **Veyra**.
2. Do not restart the project from zero.
3. Treat the latest VERIFIED state in this document as the current baseline.
4. Preserve historical facts separately from current facts.
5. Never turn a plan, recommendation, or possibility into completed work.
6. Before destructive Git operations, inspect the actual repository state.
7. Never expose or recreate secrets, passwords, tokens, PATs, client secrets, private keys, or credentials.
8. For Docker, remember: `localhost` inside a container means that container itself.
9. Prefer actual repository/configuration evidence over assumptions.
10. If an answer/archive exceeds the response limit, continue automatically in numbered parts without omission or duplication; the final part must contain QUICK RECOVERY CONTEXT and NEXT-CHAT HANDOVER.
11. Preserve user-emphasized, repeated, corrected, warned, and priority information.
12. For current external information, research and clearly label externally verified facts.

---

# 1. CURRENT MIGRATION STATUS

## VERIFIED

The user is currently performing a continuity migration because of concern that a long-running chat/kernel may become overloaded or unavailable.

Migration strategy:

```text
Historical important chats
        ↓
Individual extraction
        ↓
Canonical source files
        ↓
Current Veyra session last
        ↓
Final master continuity layer
        ↓
Future chat
```

The user has already completed the GATE-side archival phase.

## GATE STATUS

`GATE_2027_Veyra_Canonical_Continuity.md` has been prepared and uploaded into the project's Source segment.

GATE is currently considered **frozen for migration purposes** unless genuinely missing information is later discovered.

The remaining migration focus is MicroServices/current Veyra continuity.

---

# 2. ASSISTANT IDENTITY / CONTINUITY

## VERIFIED

The assistant name chosen in this session is:

**Veyra**

The user explicitly chose **Veyra** over **Plexa**.

The user treats this current session as the continuity layer responsible for preserving the project/workflow into the next chat.

## USER EXPECTATION

Continuity should preserve not only raw facts but also:

- important memories
- decisions
- reasoning
- priorities
- warnings
- corrections
- workflows
- project state
- failed approaches
- what must not be repeated

The user wants future sessions to feel like a continuation, not a fresh project onboarding.

---

# 3. PROJECT IDENTITY

## VERIFIED

Project:

**Hotel Review System / Java Spring MicroServices**

Local workspace:

```text
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system
```

Core services/infrastructure:

```text
HotelService
UserService
RatingService
ServiceRegistry
ConfigServer
ApiGateway
```

Supporting infrastructure also includes:

```text
MySQL
PostgreSQL
MongoDB
Zipkin
Docker Compose
```

Canonical architecture:

```text
                    CLIENT
                       ↓
                API GATEWAY
                       ↓
                 USER SERVICE
                  ↙          ↘
          RATING SERVICE   HOTEL SERVICE

Supporting:
Config Server
Eureka / Service Registry
Zipkin

Databases:
User Service   → MySQL
Hotel Service  → PostgreSQL
Rating Service → MongoDB
```

---

# 4. ENGINEERING DIRECTION

The project is intended to demonstrate more than CRUD.

Important engineering areas:

- Spring Boot
- Spring Cloud
- REST APIs
- OpenFeign
- Eureka/service discovery
- Config Server
- API Gateway
- Resilience4j
- OAuth2/OIDC/JWT/Okta context
- distributed tracing
- Zipkin
- Docker/Docker Compose
- database-per-service
- performance/load testing
- Git/GitHub workflow
- professional documentation/deployment

## Important positioning rule

Do not claim a technology is implemented in a particular service unless repository evidence supports it.

In particular:

- Hotel Service was explicitly stated not to use Resilience4j.
- Rating bulk optimization was historically planned but should not be called completed without verification.
- Security details should be verified from actual configuration before claiming exact scopes/issuer/client configuration.

---

# 5. USER SERVICE — HISTORICAL ENGINEERING CONTEXT

User Service is the aggregation service.

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

Historical resilience work included:

- Retry
- Circuit Breaker
- Rate Limiter
- fallback
- Spring AOP for annotation interception

Historical discussed configuration:

```text
Retry:
maxAttempts = 3
waitDuration = 5s

Rate Limiter:
limitForPeriod = 2
limitRefreshPeriod = 10s
timeoutDuration = 0
```

These are **historical discussed values**. Verify current YAML/code before treating them as current.

A dedicated `HotelServiceClient` was introduced to isolate external Hotel Service calls/fallback logic.

Important architectural reasoning:

```text
Controller
    ↓
UserService
    ↓
HotelServiceClient
    ↓
Hotel Service
```

rather than mixing HTTP calls and resilience logic directly into the controller.

---

# 6. PERFORMANCE / OBSERVABILITY HISTORY

## VERIFIED HISTORICAL

Zipkin was used for distributed tracing and scalability observation.

Important endpoint:

```text
GET /users
```

The request path was traced through:

```text
Gateway
→ User Service
→ Rating Service
→ Hotel Service
```

Historical performance work included JMeter and k6.

Recorded historical k6 result:

```text
300 VUs
30 seconds
3324 requests
2.82s average
97.02% success
99 failures
```

Later historical benchmark:

```text
250 VUs
80 seconds
90.69% success
456 failed requests
```

These measurements do not by themselves prove a root cause.

Zipkin had a historical OOM issue during load testing.

Historical heap tuning:

```text
-Xms512m -Xmx2048m
```

was completed.

Do not present historical benchmark results as current measurements unless re-run.

---

# 7. GIT/GITHUB — CURRENT VERIFIED STATE

## Repository

Local repository:

```text
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system
```

## Current branch

At the latest verified point:

```text
main
```

## Current Git status

Verified:

```text
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

## Current log

Verified:

```text
50d15d5 (HEAD -> main, origin/main, origin/HEAD) Merge pull request #18 from NikStack20/docker/compose
38da850 fix: align service configuration for docker compose
e188232 Merge pull request #17 from NikStack20/feat/compose-fix
a4b52ec (origin/feat/compose-fix, feat/compose-fix) Merge branch 'main' of github.com:NikStack20/hotel-review-system
```

## Current HEAD metadata

Verified:

```text
commit 50d15d5b440eb203775a0c44c3fe31e5335d183d
Merge: e188232 38da850
Author: Nikhil Chauhan
AuthorDate: Sun Aug 30 00:00:58 2026 +0530
CommitDate: Sun Aug 30 01:54:20 2026 +0530

Merge pull request #18 from NikStack20/docker/compose

fix: port issues
```

### Important Git-history correction

The user was bothered because the visible GitHub merge commit still displayed:

```text
fix: port issues
```

The underlying feature commit was changed during the cleanup:

```text
38da850 fix: align service configuration for docker compose
```

but the already-created merge commit remained:

```text
50d15d5 Merge pull request #18 from NikStack20/docker/compose
```

with the displayed PR title/message:

```text
fix: port issues
```

The user ultimately verified:

```text
HEAD == origin/main == origin/HEAD
working tree clean
```

Therefore the repository is currently synchronized and should **not** be rewritten merely to cosmetically change the historical merge message.

---

# 8. IMPORTANT GIT INCIDENT — COMMIT MESSAGE CLEANUP

Historical situation:

The user accidentally used:

```text
fix: port issues
```

for the feature/PR flow and later noticed the GitHub merge page.

They also accidentally merged PR #18 while trying to clean the commit history/message.

A rebase workflow was used.

Important observed intermediate state:

```text
git rebase -i --rebase-merges e188232
```

produced:

```text
[detached HEAD 38da850] fix: align service configuration for docker compose
```

and:

```text
Successfully rebased and updated refs/heads/main.
```

Afterward, the local and remote branches diverged temporarily.

The final state was restored/synchronized as:

```text
50d15d5 (HEAD -> main, origin/main, origin/HEAD)
```

### Final decision

**Leave the history alone now.**

The visible merge commit is an historical record of the PR title/message. The actual code/configuration commit was corrected to:

```text
38da850 fix: align service configuration for docker compose
```

Do not force-push or rebase again just to remove the old PR merge message unless there is a concrete reason and the full remote state is inspected first.

---

# 9. CURRENT GIT BRANCH INVENTORY

Latest verified local branch listing included:

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

Current checked-out branch:

```text
main
```

This is a snapshot from the migration session, not a guarantee that every branch still exists remotely.

---

# 10. GIT SAFETY RULES

These are high-priority continuity constraints.

### Never blindly run:

```text
git reset --hard
git push --force
git rebase
```

before checking:

```text
git status
git branch -vv
git remote -v
git log --oneline --decorate --graph --all
```

### Feature workflow preference

Preferred model:

```text
feature/<descriptive-name>
        ↓
      push
        ↓
       PR
        ↓
      main
```

### Current special case

The current main branch is already synchronized with origin.

Do not create artificial cleanup work simply because the merge commit message is aesthetically unpleasant.

---

# 11. DOCKER COMPOSE — CURRENT VERIFIED WORK

The project reached actual containerized deployment/debugging.

Historical Compose topology:

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

Known network:

```text
hrs-network
```

with bridge networking.

Important container DNS rule:

```text
localhost inside a container
=
that same container
```

Inter-container service names should be used.

Known internal database targets:

```text
User Service   → mysql:3306
Hotel Service  → postgres:5432
Rating Service → mongodb:27017
```

---

# 12. DATABASE RESTORATION — CURRENT VERIFIED STATE

This is one of the most important current facts.

Backup directory:

```text
D:\Databases\hotel-review-db-backup
```

Contained:

```text
postgres-dump\hotelDB.dump
mongo-dump\ratingService\
userservice_backup.sql
```

## PostgreSQL dump

Verified file:

```text
postgres-dump\hotelDB.dump
```

Size:

```text
3,689 bytes
```

`pg_restore --list` verified:

```text
Archive created at 2026-08-22 02:03:27 UTC
dbname: hotelDB
TOC Entries: 10
Compression: gzip
Dump Version: 1.16-0
Format: CUSTOM
Dumped from database version: 18.4
Dumped by pg_dump version: 18.6
```

Tables:

```text
public.hotel_facilities
public.hotels
```

The initial restore produced existing-table/FK errors because the target database already contained the tables.

After stopping Hotel Service:

```text
docker compose stop hotel-service
```

the restore was successfully rerun with:

```text
docker exec hrs-postgres pg_restore --clean --if-exists --no-owner -U postgres -d hotelDB /tmp/hotelDB.dump
```

Verified afterward:

```text
\dt
```

returned:

```text
hotel_facilities
hotels
```

Verified row counts:

```text
hotels = 4
hotel_facilities = 15
```

This is a **CURRENT VERIFIED DATABASE STATE**.

---

# 13. MONGODB RESTORATION — CURRENT VERIFIED STATE

Backup:

```text
D:\Databases\hotel-review-db-backup\mongo-dump\ratingService
```

Files:

```text
prelude.json
user_ratings.bson
user_ratings.metadata.json
```

Copied into:

```text
hrs-mongodb:/tmp/ratingService
```

Restore command:

```text
docker exec hrs-mongodb mongorestore --db ratingService /tmp/ratingService
```

MongoDB emitted a deprecation warning:

```text
The --db and --collection flags are deprecated for this use-case;
please use --nsInclude instead
```

This was a **warning, not a restore failure**.

`prelude.json` was skipped because mongorestore did not know how to process it.

The actual collection restored successfully:

```text
ratingService.user_ratings
```

Verified result:

```text
53 documents restored successfully
0 documents failed to restore
```

Current data state:

```text
user_ratings = 53 documents
```

---

# 14. MYSQL / USER SERVICE RESTORATION — CURRENT VERIFIED STATE

Backup:

```text
D:\Databases\hotel-review-db-backup\userservice_backup.sql
```

Database:

```text
userservice
```

Table:

```text
micro_users
```

The backup contains:

```text
10 users
```

including the project's development/test dataset.

The user subsequently restored the database and verified:

```text
user count = 10
```

The User Service was then started.

---

# 15. CURRENT RUNTIME STATE

## VERIFIED

At the latest point in this migration session:

> **All services/things were reported by the user as up and running.**

This means the database restoration and Docker/runtime recovery had reached an operational state according to the user's direct verification.

Do not reinterpret this as a complete production-health audit.

For future work, if needed, verify actual health through:

```text
docker compose ps
docker compose logs <service>
```

and application-level requests.

---

# 16. HOTEL SERVICE RESTORE CONTEXT

The Hotel Service was stopped before PostgreSQL restoration to avoid application interaction during cleanup/restore:

```text
docker compose stop hotel-service
```

After successful restoration, the user reported that everything was up and running.

The current PostgreSQL dataset is:

```text
4 hotels
15 hotel_facilities
```

Do not rerun the restore unless there is a concrete data-recovery reason.

---

# 17. CONFIG SERVER — HISTORICAL CRITICAL DEBUGGING

Historical blocker:

```text
JGit NoRemoteRepositoryException:
${CONFIG_REPO_URI}: not found
```

The most important clue is that the literal placeholder:

```text
${CONFIG_REPO_URI}
```

reached JGit.

Therefore the first debugging layer is:

```text
environment
    ↓
Docker Compose
    ↓
Spring property binding
    ↓
Config Server
    ↓
JGit
```

not immediately:

```text
Git repository does not exist
```

Current final resolution is not reconstructed here unless verified from current repository/configuration.

---

# 18. DOCUMENTATION / DEPLOYMENT — CURRENT DIRECTION

The user has stated that once the current system status is locked, the next major engineering phase is:

```text
update documentation to the latest version
+
package/deployment work
```

This should be treated as the **NEXT PLANNED PHASE**, not as completed.

Likely order:

```text
1. Verify final runtime/configuration state
2. Update docs to match actual implementation
3. Verify deployment/package artifacts
4. Verify README/deployment instructions
5. Final Git cleanup only where necessary
6. Public presentation later
```

Do not document an architecture feature that the repository does not actually implement.

---

# 19. DOCUMENTATION PRINCIPLE

Documentation must follow implementation.

Preferred sequence:

```text
actual code/config
      ↓
actual runtime behavior
      ↓
actual architecture
      ↓
documentation
```

not:

```text
planned architecture
      ↓
claim it as implemented
```

The project should eventually have:

- master architecture documentation
- service READMEs
- Docker/deployment instructions
- configuration explanation
- observability/tracing documentation
- performance documentation
- accurate setup instructions

---

# 20. PUBLIC PRESENTATION / LINKEDIN

The user explicitly does **not** want to prematurely showcase the Hotel Review System before it is sufficiently complete.

Current priority:

```text
Finish project
→ verify architecture
→ verify deployment
→ document
→ then showcase
```

Do not reverse this.

---

# 21. OPEN SOURCE / SPOCK CONTEXT

A separate Spock contribution thread exists.

Historical situation:

```text
fork
→ PR
→ maintainer review
```

Maintainer corrected several conceptual/documentation points:

1. There is no concept called "JUnit Platform Extension".
2. There is the JUnit Platform.
3. The Platform supports Engines.
4. Jupiter is one engine.
5. Spock is another engine.
6. Jupiter and Spock are sibling engines.
7. A Jupiter extension should not be expected to work inside Spock.
8. The issue is general, not specifically Spring-related.
9. It belongs in general documentation rather than Spring-specific documentation.
10. Specific third-party extensions should not be listed as official Spock recommendations.
11. `testRuntimeOnly` is preferable to `testImplementation` when no classes from the dependency are directly used.
12. Maintainer referenced PR #2322.

Immediate historical action:

```text
Read PR #2322 first.
```

If #2322 supersedes the user's PR:

```text
acknowledge
→ close own PR
```

If not:

```text
discuss correct scope
→ revise appropriately
```

Do not fight the maintainer's terminology correction.

---

# 22. USER-EMPHASIZED CONTINUITY REQUIREMENTS

## PRIORITY

Continuity across chat migrations is important.

The user wants the next session to retain:

- project state
- reasoning
- important memories
- decisions
- constraints
- failures
- recovery steps
- Git/GitHub history
- Docker/database state
- current priorities

## STYLE

The user prefers:

- direct answers
- professional tone
- industry-aware reasoning
- honest assessment
- no sugar-coating
- no fake certainty
- no unnecessary diversification
- explain WHY
- practical implementation guidance
- human-like professional writing

## ARCHIVAL

Preserve:

```text
FACT
DECISION
PREFERENCE
PRIORITY
WARNING
PLAN
UNCERTAIN
INFERENCE
```

Do not silently merge categories.

---

# 23. USER'S MIGRATION STRATEGY

The user is building a backup/continuity system because a long-running chat may eventually become too large or lose continuity.

Desired behavior:

```text
Current chat
   ↓
detect important context accumulation
   ↓
prepare archival extraction before continuity degrades
   ↓
new chat receives canonical archive
   ↓
continue seamlessly
```

The user specifically wants early warning if the conversation/kernel appears to be becoming too overloaded to safely preserve the working flow.

Important limitation:

The assistant cannot reliably inspect internal ChatGPT kernel load/remaining context capacity directly. Therefore future warnings should be based only on visible context/continuity constraints, not invented internal metrics.

---

# 24. CURRENT MIGRATION CHECKLIST

```text
GATE archival
    ✅ completed

MicroServices historical archives
    ✅ major historical dumps recovered

Current Veyra session archive
    ✅ THIS DOCUMENT

Source segment canonical files
    ✅ GATE archive uploaded
    🔄 MicroServices canonical archive should be uploaded after finalization

Final master continuity archive
    ⏳ later

New-chat boot
    ⏳ after final master is prepared
```

---

# 25. WHAT MUST NOT BE LOST

### Project

```text
Hotel Review System
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system
```

### Services

```text
HotelService
UserService
RatingService
ServiceRegistry
ConfigServer
ApiGateway
```

### Current Git

```text
main
HEAD = 50d15d5
origin/main = 50d15d5
working tree clean
```

### Current database restoration

```text
PostgreSQL:
hotels = 4
hotel_facilities = 15

MongoDB:
user_ratings = 53 documents

MySQL:
micro_users = 10 users
```

### Current runtime

```text
All services reported up and running.
```

### Current next phase

```text
Documentation update
+
package/deployment work
```

### Historical critical Docker lesson

```text
localhost inside container ≠ another container
```

### Historical Config Server clue

```text
${CONFIG_REPO_URI}
```

reaching JGit means investigate property/environment resolution first.

### Git decision

```text
Do not rewrite current main merely to cosmetically remove
the historical "fix: port issues" merge message.
```

---

# 26. QUICK RECOVERY CONTEXT

```text
PROJECT:
Hotel Review System / Java Spring MicroServices

LOCAL:
D:\java-projects\JAVA-SPRING\MicroServices\hotel-review-system

ASSISTANT:
Veyra

CURRENT MIGRATION:
Current Veyra session is being converted into a canonical continuity archive.

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
working tree clean

IMPORTANT COMMITS:
38da850 = fix: align service configuration for docker compose
50d15d5 = Merge pull request #18 from NikStack20/docker/compose
                    displayed merge/PR message:
                    fix: port issues

GIT DECISION:
Leave history alone now.
No unnecessary force-push/rebase.

DATABASE CURRENT VERIFIED:
PostgreSQL hotels = 4
PostgreSQL hotel_facilities = 15
MongoDB user_ratings = 53
MySQL micro_users = 10

RUNTIME:
User reports all services/things are up and running.

DOCKER:
Compose topology includes:
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

NETWORK:
hrs-network

DOCKER RULE:
localhost inside container means that container.

CONFIG SERVER HISTORICAL ERROR:
NoRemoteRepositoryException:
${CONFIG_REPO_URI}: not found

INTERPRETATION:
Literal placeholder reached JGit.
Check environment/property resolution first.

USER SERVICE:
Aggregation service.
GET /users → User + Ratings + Hotels.

RESILIENCE HISTORY:
Retry
Circuit Breaker
Rate Limiter
Fallback
Spring AOP

HISTORICAL RETRY:
maxAttempts=3
waitDuration=5s

HISTORICAL RATE LIMITER:
limitForPeriod=2
limitRefreshPeriod=10s
timeoutDuration=0

OBSERVABILITY:
Micrometer/Brave/Zipkin context.
GET /users is major tracing/performance scenario.

HISTORICAL PERFORMANCE:
300 VUs / 30s / 3324 requests / 2.82s avg / 97.02% success / 99 failures
Later:
250 VUs / 80s / 90.69% success / 456 failures

ZIPKIN:
Historical OOM addressed with:
-Xms512m -Xmx2048m

HOTEL OPTIMIZATION:
Bulk hotel retrieval completed historically.

RATING BULK:
Historical plan, completion not proven.

CURRENT NEXT PROJECT PHASE:
Update documentation to latest actual implementation
+
package/deployment work

PUBLIC PRESENTATION:
Do not prematurely showcase the project.

SPOCK:
PR #2322 should be inspected before deciding on current PR.
Maintainer corrections must be respected.

MIGRATION:
GATE canonical archive is already done.
Current Veyra archive is now the MicroServices continuity layer.
Final master archive comes later.
```

---

# 27. NEXT-CHAT HANDOVER

When a future chat loads this archive:

### First acknowledge:

> "Veyra continuity loaded. The Hotel Review System is currently on synchronized `main` at `50d15d5`, the restored databases are verified at 4 hotels / 15 facilities / 53 ratings / 10 users, and the runtime was reported up. The immediate engineering phase is documentation update plus package/deployment verification."

Then continue from the user's requested task.

### Do not:

- restart the project explanation
- redo database restoration
- rewrite Git history without reason
- assume old uncertain configurations are current
- claim planned optimizations are completed
- expose secrets
- treat historical benchmark numbers as current
- prematurely move to LinkedIn/resume polish

### Before changing Git:

Inspect:

```text
git status
git branch -vv
git remote -v
git log --oneline --decorate --graph --all
```

### Before changing Docker:

Inspect:

```text
docker compose config
docker compose ps
```

and relevant logs/configuration.

### Before updating documentation:

Verify the actual current implementation first.

---

# 28. FINAL PRINCIPLE

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
DEPLOY
        ↓
SHOWCASE
```

The goal is not merely to keep files alive.

The goal is to preserve the **engineering continuity** so the next Veyra session can continue from the real state without losing the reasoning that produced it.
