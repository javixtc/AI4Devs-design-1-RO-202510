LTI ATS --- MVP Design Document
=============================

* * * * *

1\. System summary, value proposition & main features
-----------------------------------------------------

**Short description**\
LTI ATS is a cloud-native Applicant Tracking System focused on accelerating hiring through configurable automation, AI-assisted matching with explainability, and real-time collaboration between recruiters and hiring managers. The MVP targets SMBs and mid-market HR teams that want to reduce time-to-hire and improve candidate experience.

**Unique value proposition**

-   **Automated, transparent hiring**: trigger-based workflows and AI matching with explainability reduce manual screening and bias.

-   **Collaboration-first UX**: real-time commenting, notifications and shared candidate timelines shorten review loops.

-   **End-to-end observability & GDPR-by-design**: audit logs, consent flows, and deletion endpoints to meet compliance.

**Competitive advantages**

-   Explainable AI matching (why a candidate was scored)

-   Fully configurable automation workflows per job/department

-   Integrated interview scheduling with two-way calendar sync

-   Lean SaaS pricing and rapid time-to-value for HR teams

**Main features (MVP)**

-   Job creation, edit, multi-channel publication (internal + job boards)

-   Candidate portal + application intake (resume upload, apply form)

-   Candidate database with search & AI match suggestions

-   Configurable workflows (stages, rules, auto-move)

-   Interview scheduling with calendar integration (Google/Microsoft)

-   Comments, mentions, role-based views & permissions

-   Audit logs, consent capture, data deletion endpoint

* * * * *

2\. Lean Canvas (concise)
-------------------------

-   **Problem**

    -   Manual CV screening is slow and inconsistent.

    -   Collaboration between recruiters and hiring managers is fragmented.

    -   Scheduling interviews across calendars is time-consuming.

-   **Customer Segments**

    -   SMB HR teams (10--200 employees)

    -   Mid-market companies (200--2000 employees)

    -   Staffing agencies

-   **Unique Value Proposition**

    -   AI-assisted, explainable matching + configurable automations for faster, fairer hiring.

-   **Solution**

    -   SaaS ATS with AI matching, workflow automation, calendar sync, and collaborative UI.

-   **Key Metrics**

    -   Time-to-hire, time-to-first-interview, candidate conversion rate, MAU, churn.

-   **Unfair Advantage**

    -   Explainability layer for AI matching + configurable workflow templates.

-   **Channels**

    -   Direct sales, marketplace integrations (Slack, GSuite), job boards partnerships.

-   **Cost Structure**

    -   Cloud infra (DB, search, object storage), ML compute for offline matching, SRE & dev teams, 3rd-party API costs.

-   **Revenue Streams**

    -   Monthly subscription (tiers by seats/features), onboarding professional services, integration fees.

* * * * *

3\. Use-cases (detailed)
------------------------

### Use Case 1 --- Publish Job

**Name:** Publish Job

**Actors:** Recruiter, Admin, System (Automation service)

**Preconditions:**

-   Recruiter or Admin is authenticated and authorized to create/publish roles.

-   Organization and recruiting pipelines configured.

**Main Flow (happy path):**

1.  Recruiter creates a Job Draft (title, department, description, location, requisitionId, stages).

2.  Recruiter configures distribution channels (internal, job boards, social).

3.  Recruiter sets automation workflow (auto-screening rules, auto-email templates).

4.  Recruiter clicks **Publish**.

5.  System validates job data and stores Job record.

6.  Publication service pushes job to configured channels (internal page, external job boards via connectors).

7.  System returns confirmation and job public URL.

8.  Notification sent to subscribed hiring managers.

**Alternative Flows:**

-   A1: Data invalid => validation errors displayed; publish aborted.

-   A2: External job board rejects => job marked `PUBLISHED_PARTIAL` and system logs error; recruiter notified.

-   A3: Auto-approval configured => job automatically moves to live without manual confirmation.

**Postconditions:**

-   Job status = `PUBLISHED`

-   Job appears in candidate portal and assigned channels

-   Audit log entry for publication

**UML Use-Case Diagram (PlantUML)**

`@startuml
left to right direction
actor Recruiter
actor Admin
actor System

rectangle "LTI ATS" {
  Recruiter --> (Create Job Draft)
  Recruiter --> (Configure Distribution)
  Recruiter --> (Configure Workflow)
  Recruiter --> (Publish Job)
  Admin --> (Publish Job)
  (Publish Job) --> System : validate & push
  System --> (Publish Job) : publish result
}
@enduml`

* * * * *

### Use Case 2 --- Manage Candidates

**Name:** Manage Candidates

**Actors:** Recruiter, Hiring Manager, System (AI Matching, Automation)

**Preconditions:**

-   Job published and candidate applications are incoming or imported.

**Main Flow:**

1.  System indexes new Applications and extracts resume text (OCR/NLP for PDF).

2.  AI Matching engine computes matchScore for each candidate vs job.

3.  Recruiter reviews candidate list sorted by matchScore or custom filters.

4.  Recruiter tags and shortlists candidate(s); adds comments/mentions Hiring Manager.

5.  Hiring Manager reviews candidate, adds feedback and either requests interview or declines.

6.  Automation rule triggers email to candidate (e.g., move to phone screen).

**Alternative Flows:**

-   A1: Candidate missing required info => system flags and requests completion via candidate portal.

-   A2: Recruiter manually overrides match & marks candidate priority.

**Postconditions:**

-   Application status updated (e.g., `SCREENED`, `SHORTLISTED`, `REJECTED`)

-   Audit log for all state changes and comments

**UML Use-Case Diagram (PlantUML)**

`@startuml
left to right direction
actor Recruiter
actor HiringManager
actor System

rectangle "Candidate Management" {
  Recruiter --> (View Candidate List)
  System --> (View Candidate List) : enrich scores
  Recruiter --> (Tag/Shortlist Candidate)
  Recruiter --> (Comment on Candidate)
  HiringManager --> (Review Candidate)
  System --> (Send Candidate Email) : automation
}
@enduml`

* * * * *

### Use Case 3 --- Schedule Interview

**Name:** Schedule Interview

**Actors:** Recruiter, Hiring Manager, Candidate, Calendar Provider (Google/Microsoft), System

**Preconditions:**

-   Candidate is shortlisted and has provided availability (or candidate portal supports scheduling).

-   Recruiter/Hiring Manager connected calendar via OAuth.

**Main Flow:**

1.  Recruiter opens scheduling UI for a candidate and selects interviewers.

2.  System queries interviewers' free/busy via Calendar Provider API.

3.  System proposes mutually available slots.

4.  Candidate selects preferred slot (or recruiter selects and invites candidate).

5.  System creates event(s) in interviewer & candidate calendars and sends invites.

6.  Candidate acknowledges or requests reschedule; system handles conflicts by triggering alternative suggestions.

7.  After interview, interviewer posts feedback in the candidate timeline.

**Alternative Flows:**

-   A1: Calendar API failure => fallback to manual scheduling with a generated email template.

-   A2: Candidate declines => workflow offers next slots or marks as `AWAITING_RESCHEDULE`.

**Postconditions:**

-   Interview event created and linked to `Interview` entity

-   Notifications and calendar events are sent

-   Interview feedback stored and linked to Application

**UML Use-Case Diagram (PlantUML)**

`@startuml
left to right direction
actor Recruiter
actor HiringManager
actor Candidate
actor CalendarProvider

rectangle "Interview Scheduling" {
  Recruiter --> (Open Scheduling UI)
  (Open Scheduling UI) --> CalendarProvider : query availability
  Candidate --> (Select Slot)
  System --> CalendarProvider : create events
  HiringManager --> (Provide Feedback)
}
@enduml`

* * * * *

4\. Data model (3NF) --- Entities, attributes, relationships
----------------------------------------------------------

**Naming conventions:** Entities `PascalCase`, attributes `camelCase`, enums `UPPER_SNAKE_CASE`.

Below are core entities for MVP. Each entity: description, attributes, PK, FK, indexes, cardinalities.

> **Primary storage:** Relational DB (Postgres) for transactional data.\
> **Search index:** Elasticsearch / OpenSearch for full-text resume/job search and AI match indexing.\
> **Blob/Object storage:** S3-compatible for attachments.

* * * * *

### Entity: Company

-   **Description:** Organization using LTI ATS.

-   **Attributes:**

    -   `companyId` UUID PK

    -   `name` String

    -   `domain` String

    -   `timezone` String

    -   `createdAt` Timestamp

    -   `deletedAt` Timestamp (nullable)

-   **Indexes:** `company.domain`, `company.name`

-   **Cardinality:** Company 1 --- N Users, Company 1 --- N Jobs

* * * * *

### Entity: User

-   **Description:** Platform user (Recruiter, Hiring Manager, Admin, Guest Interviewer).

-   **Attributes:**

    -   `userId` UUID PK

    -   `companyId` UUID FK -> Company(companyId)

    -   `email` String (unique per company)

    -   `firstName` String

    -   `lastName` String

    -   `role` Enum `USER_ROLE` (RECRUITER, HIRING_MANAGER, ADMIN, GUEST_INTERVIEWER)

    -   `isActive` Boolean

    -   `createdAt` Timestamp

-   **Indexes:** `user.companyId`, `user.email`

-   **Cardinality:** User N --- 1 Company

* * * * *

### Entity: Job

-   **Description:** Job requisition.

-   **Attributes:**

    -   `jobId` UUID PK

    -   `companyId` UUID FK

    -   `createdBy` UUID FK -> User(userId)

    -   `title` String

    -   `department` String

    -   `location` String

    -   `description` Text

    -   `skills` JSON (array of normalized skill strings)

    -   `status` Enum `JOB_STATUS` (DRAFT, PUBLISHED, CLOSED)

    -   `postedAt` Timestamp

    -   `createdAt` Timestamp

-   **Indexes:** `job.companyId`, `job.title`, GIN index on `skills`

-   **Cardinality:** Job 1 --- N Applications

* * * * *

### Entity: CandidateProfile

-   **Description:** Canonical candidate data (may pre-exist via sourcing).

-   **Attributes:**

    -   `candidateId` UUID PK

    -   `fullName` String

    -   `email` String (nullable)

    -   `phone` String (nullable)

    -   `resumeText` Text (extracted)

    -   `profileJson` JSON (structured parsed info: experience, education)

    -   `createdAt` Timestamp

-   **Indexes:** full-text index on `fullName`, `resumeText`

-   **Cardinality:** CandidateProfile 1 --- N Applications

* * * * *

### Entity: Application

-   **Description:** Candidate application for a Job.

-   **Attributes:**

    -   `applicationId` UUID PK

    -   `jobId` UUID FK -> Job(jobId)

    -   `candidateId` UUID FK -> CandidateProfile(candidateId)

    -   `source` Enum (INTERNAL, JOB_BOARD, REFERRAL, MANUAL_IMPORT)

    -   `status` Enum `APPLICATION_STATUS` (APPLIED, SCREENED, SHORTLISTED, INTERVIEW_SCHEDULED, OFFERED, HIRED, REJECTED)

    -   `appliedAt` Timestamp

    -   `matchScore` Integer (0--100) stored for quick sort (recomputed in background)

    -   `metadata` JSON (original CV file metadata)

-   **Indexes:** `application.jobId`, `application.candidateId`, index on `status`, index on `matchScore`

-   **Cardinality:** Application N --- 1 Job ; Application N --- 1 Candidate

* * * * *

### Entity: Attachment

-   **Description:** Files stored (resumes, cover letters).

-   **Attributes:**

    -   `attachmentId` UUID PK

    -   `applicationId` UUID FK -> Application(applicationId) nullable (attachments may be in candidate profile)

    -   `candidateId` UUID FK -> CandidateProfile(candidateId) nullable

    -   `s3Key` String

    -   `fileName` String

    -   `mimeType` String

    -   `size` Integer

    -   `uploadedAt` Timestamp

    -   `checksum` String

-   **Indexes:** `attachment.candidateId`, `attachment.applicationId`

-   **Cardinality:** Candidate 1 --- N Attachment ; Application 1 --- N Attachment

* * * * *

### Entity: Interview

-   **Description:** Interview event linked to application.

-   **Attributes:**

    -   `interviewId` UUID PK

    -   `applicationId` UUID FK -> Application(applicationId)

    -   `scheduledBy` UUID FK -> User(userId)

    -   `mode` Enum `INTERVIEW_MODE` (ON_SITE, VIDEO, PHONE)

    -   `startTime` Timestamp (ISO-8601 w/timezone)

    -   `endTime` Timestamp

    -   `calendarEventId` String (third-party provider id)

    -   `status` Enum (SCHEDULED, COMPLETED, CANCELLED, RESCHEDULED)

    -   `createdAt` Timestamp

-   **Indexes:** `interview.applicationId`, `interview.startTime`

-   **Cardinality:** Application 1 --- N Interview

* * * * *

### Entity: Comment

-   **Description:** Collaboration comments on application.

-   **Attributes:**

    -   `commentId` UUID PK

    -   `applicationId` UUID FK

    -   `authorId` UUID FK -> User(userId)

    -   `content` Text

    -   `createdAt` Timestamp

    -   `isPrivate` Boolean

-   **Indexes:** `comment.applicationId`, `comment.authorId`

-   **Cardinality:** Application 1 --- N Comment

* * * * *

### Entity: WorkflowAutomation

-   **Description:** Configurable automation rules per company/job.

-   **Attributes:**

    -   `workflowId` UUID PK

    -   `companyId` UUID FK

    -   `name` String

    -   `trigger` JSON (example: `{ "onEvent":"APPLICATION_CREATED", "conditions":[...] }`)

    -   `actions` JSON (example: sendEmail, moveStage)

    -   `isActive` Boolean

    -   `createdAt` Timestamp

-   **Indexes:** `workflow.companyId`

-   **Cardinality:** Company 1 --- N WorkflowAutomation

* * * * *

### Entity: AuditLog

-   **Description:** Immutable audit trail of actions.

-   **Attributes:**

    -   `auditId` UUID PK

    -   `companyId` UUID FK

    -   `actorId` UUID FK -> User(userId) nullable (system actions null)

    -   `entityType` String

    -   `entityId` UUID

    -   `action` String

    -   `payload` JSON

    -   `timestamp` Timestamp

-   **Indexes:** `audit.companyId`, `audit.entityId`

* * * * *

### Enums (examples)

-   `APPLICATION_STATUS` = {APPLIED, SCREENED, SHORTLISTED, INTERVIEW_SCHEDULED, OFFERED, HIRED, REJECTED}

-   `USER_ROLE` = {RECRUITER, HIRING_MANAGER, ADMIN, GUEST_INTERVIEWER}

-   `INTERVIEW_MODE` = {ON_SITE, VIDEO, PHONE}

-   `JOB_STATUS` = {DRAFT, PUBLISHED, CLOSED}

-   `SOURCE_TYPE` = {INTERNAL, JOB_BOARD, REFERRAL, MANUAL_IMPORT}

* * * * *

### Relationship summary (cardinalities)

-   Company 1 --- N User

-   Company 1 --- N Job

-   Job 1 --- N Application

-   CandidateProfile 1 --- N Application

-   Application 1 --- N Interview

-   Application 1 --- N Comment

-   CandidateProfile 1 --- N Attachment

-   Company 1 --- N WorkflowAutomation

* * * * *

5\. High-level architecture (pattern & justification)
-----------------------------------------------------

**Chosen pattern:** **Hexagonal architecture + modular microservices** for MVP.

-   **Justification:** Hexagonal for clear separation of business logic from infra (DB, search, calendar providers). Modular microservices (bounded by domain: Candidate Management, Job Management, Scheduling, Automation, Auth/Users, Search/Index) provide independent scaling and clearer ownership for future growth. Start with a small number of containers (single deployment per service) and evolve to independent services as scale requires.

**Primary components/services:**

-   **API Gateway / BFF** --- single entry point for frontend, JWT token validation, rate-limiting.

-   **Auth Service** --- OAuth2/OIDC integration (SSO), user management, role claims.

-   **Job Service** --- job CRUD, publication, job-board connectors.

-   **Candidate Management Service** --- candidate profiles, applications, attachments, comments (C4 Level-3 focus).

-   **Scheduling Service** --- calendar integration and interview orchestration.

-   **Automation / Workflow Service** --- executes WorkflowAutomation rules, triggers emails, state transitions.

-   **Search & Indexing** --- full-text search (Elasticsearch / OpenSearch) and match index.

-   **AI Match Engine (offline/async)** --- ML pipeline to compute match scores + explanation artifacts; stores results in match index and application.matchScore.

-   **Notification Service** --- email, in-app, webhooks.

-   **Storage** --- Postgres (Relational), S3-compatible object store for attachments, Elasticsearch for search.

-   **Event Bus** --- Kafka or cloud pub/sub for async events (ApplicationCreated, JobPublished, MatchComputed, InterviewScheduled).

-   **Observability** --- Metrics/Tracing/Logs stack.

**Communication patterns:**

-   Synchronous: REST/JSON for external-facing APIs (API Gateway -> services).

-   Internal RPC/HTTP between services (gRPC optional for high-throughput components).

-   Async: event bus (Kafka or managed pub/sub) for decoupling heavy flows (resume parsing, AI match, notifications).

* * * * *

### High-level architecture diagram (PlantUML)

`@startuml
left to right direction

actor "Recruiter / HM / Candidate" as User

package "Frontend" {
  [Web App] as Web
  [Mobile App] as Mobile
}

package "Edge" {
  [API Gateway / BFF] as API
}

package "Core Services" {
  [Auth Service] as Auth
  [Job Service] as JobS
  [Candidate Management Service] as CMS
  [Scheduling Service] as Sched
  [Workflow Automation Service] as Flow
  [Notification Service] as Notify
  [AI Match Engine] as AI
}

package "Infra" {
  [Postgres DB] as DB
  [Search Index (Elasticsearch)] as ES
  [Object Storage (S3)] as S3
  [Event Bus (Kafka)] as Kafka
  [Calendar Provider (Google/Microsoft)] as Cal
}

User --> Web
Web --> API
Mobile --> API

API --> Auth
API --> JobS
API --> CMS
API --> Sched

JobS --> DB
CMS --> DB
Sched --> Cal
Flow --> Kafka
Notify --> Kafka
AI --> ES
CMS --> ES
CMS --> S3

@enduml`

* * * * *

6\. C4 Diagrams --- Candidate Management Service (Levels 1--3)
-----------------------------------------------------------

### C4 Level 1 --- System Context (PlantUML)

`@startuml
left to right direction
actor "Recruiter / Hiring Manager" as HM
actor "Candidate" as C
actor "Admin" as Admin

rectangle "LTI ATS System" {
  HM --> (Use system)
  C --> (Apply & track)
  Admin --> (Manage org)
}

note right of (Use system)
Includes: Job publishing, candidate intake,
scheduling, matching, automation.
end note
@enduml`

* * * * *

### C4 Level 2 --- Containers (PlantUML)

`@startuml
left to right direction
actor "Recruiter/HiringManager" as HM
actor "Candidate" as C

rectangle "LTI ATS" {
  rectangle "API Gateway" as GW
  rectangle "Candidate Management Service" as CMS
  rectangle "Job Service" as JS
  rectangle "Scheduling Service" as SS
  rectangle "Workflow Automation Service" as WAS
  rectangle "AI Match Engine (offline)" as AI
  rectangle "Search Index" as ES
  rectangle "Postgres" as PG
  rectangle "Object Storage" as S3
}

HM --> GW
C --> GW

GW --> CMS
GW --> JS
GW --> SS

CMS --> PG
CMS --> S3
CMS --> ES
AI --> ES
WAS --> CMS
ES --> CMS : search/match queries
PG --> JS
PG --> SS
@enduml`

* * * * *

### C4 Level 3 --- Components inside Candidate Management Service (PlantUML)

**Components:** REST Controller / API, Candidate Domain Service, Application Domain Service, Attachment Store Adapter, Resume Parser Adapter, Search Index Adapter, Audit Adapter, Event Publisher.

`@startuml
left to right direction
package "CandidateManagementService" {
  [Candidate API Controller] as Controller
  [Candidate Domain Service] as Domain
  [Application Domain Service] as AppService
  [Attachment Adapter (S3)] as S3Adapter
  [Resume Parser Adapter] as Parser
  [Search Index Adapter] as SearchAdapter
  [Audit Adapter] as Audit
  [Event Publisher (Kafka)] as EventPub
  [Postgres Repository] as Repo
}

Controller --> Domain : create/read/update candidate
Controller --> AppService : create/read/update application
Domain --> Repo : persist CandidateProfile
AppService --> Repo : persist Application
AppService --> S3Adapter : upload attachment
S3Adapter --> Parser : trigger resume parse
Parser --> SearchAdapter : send parsed resume text
AppService --> EventPub : publish ApplicationCreated
AppService --> Audit : record action
SearchAdapter --> Repo : optional sync matchScore
@enduml`

**Responsibilities (component list)**

-   **Candidate API Controller:** validate requests, enforce RBAC, expose REST endpoints.

-   **Candidate Domain Service:** business rules for candidate merges, deduplication, profile enrichment.

-   **Application Domain Service:** application lifecycle, status transitions, matchScore writes.

-   **Attachment Adapter (S3):** upload/download attachments, pre-signed URLs, encryption at rest.

-   **Resume Parser Adapter:** orchestrates 3rd-party NLP (or internal parser) to extract resumeText and structured fields.

-   **Search Index Adapter:** indexes candidate/resume into Elasticsearch and queries for search/matching.

-   **Audit Adapter:** append-only audit log writes.

-   **Event Publisher:** emits domain events to event bus for async processing.

* * * * *

7\. Sample REST API --- Candidate Management (OpenAPI-like examples)
------------------------------------------------------------------

**Base path:** `POST /api/v1/companies/{companyId}/candidates`

### 1) Create Candidate (apply)

**Request**

`POST /api/v1/companies/{companyId}/applications
Authorization: Bearer <access_token>
Content-Type: multipart/form-data`

Form fields:

-   `jobId` (UUID) --- required

-   `firstName` (String) --- required

-   `lastName` (String) --- required

-   `email` (String) --- optional

-   `phone` (String) --- optional

-   `resume` (file) --- optional

-   `coverLetter` (file) --- optional

**Response 201**

`{
  "applicationId": "uuid",
  "candidateId": "uuid",
  "status": "APPLIED",
  "appliedAt": "2025-11-18T12:34:56Z"
}`

### 2) Get Candidate Profile

**Request**

`GET /api/v1/companies/{companyId}/candidates/{candidateId}
Authorization: Bearer <access_token>`

**Response 200**

`{
  "candidateId": "uuid",
  "fullName": "Jane Doe",
  "email": "jane@example.com",
  "phone": "+44..",
  "resumeText": "...",
  "profileJson": {...},
  "createdAt": "2025-11-10T10:00:00Z"
}`

### 3) List Applications for Job (paged, filterable)

**Request**

`GET /api/v1/companies/{companyId}/jobs/{jobId}/applications?status=APPLIED&page=1&size=25&sort=matchScore:desc`

**Response 200**

`{
  "page": 1,
  "size": 25,
  "total": 234,
  "items": [
    {
      "applicationId":"uuid",
      "candidateId":"uuid",
      "matchScore": 87,
      "status":"APPLIED",
      "appliedAt":"2025-11-17T13:22:00Z"
    }
  ]
}`

### 4) Update Application Status (example)

**Request**

`PATCH /api/v1/companies/{companyId}/applications/{applicationId}
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "status": "SHORTLISTED",
  "comment": "Strong match on required skills."
}`

**Response 200**

`{ "applicationId":"uuid", "status":"SHORTLISTED", "updatedAt":"2025-11-18T13:00:00Z" }`

**Authentication & Claims (JWT)**

-   Token must include `companyId` claim and `roles` array with `USER_ROLE` values.

-   Use role claims for authorization checks: `roles: ["RECRUITER", "HIRING_MANAGER"]`.

* * * * *

8\. AI Matching & Explainability (design notes)
-----------------------------------------------

-   **Offline ML pipeline** consumes indexed candidate resumes + job descriptors and computes a ranked `matchScore` and an `explainability` JSON with feature contributions (e.g., skillsMatch: +40, experienceYears: +20, education: +5).

-   **Storage:** `matchScore` stored in Application table; `explainability` stored in Search Index or a separate ML artifacts store (JSON reference).

-   **Explainability:** surface top 3 contributing factors to the recruiter UI.

* * * * *

9\. Search & Indexing strategy
------------------------------

-   **Search backend:** Elasticsearch / OpenSearch.

-   **Indexes:**

    -   `candidate_index`: fields `candidateId`, `fullName`, `resumeText` (full-text), `skills` (keyword), `experienceYears` (numeric).

    -   `job_index`: `jobId`, `title`, `description`, `skills`.

-   **Indexed fields for match:** job title, skills, keywords, candidate name, resume text, normalized skills, experience years.

-   **Sync approach:** Event-driven indexing --- on `ApplicationCreated`, resume parsed and candidate indexed asynchronously.

* * * * *

10\. Non-functional: Scalability, Security, Observability
---------------------------------------------------------

### Scalability strategy

-   **Initial target:** 10k MAU. Design for growth to 100k MAU.

-   **Services:** containerized (Kubernetes). Autoscale pods by CPU/RPS.

-   **Database:** single Postgres primary with read replicas. Use partitioning (e.g., by companyId or createdAt) if growth dictates. Use connection pooling (PgBouncer).

-   **Search:** Elasticsearch cluster scaled by nodes; index sharding tuned to document count.

-   **Background workers:** horizontally scalable worker pool (Kubernetes deployments) subscribe to event bus for heavy tasks (resume parsing, AI match).

-   **Blob storage:** S3 with lifecycle policies; CDN for public assets.

-   **Caching:** Redis for ephemeral caches, rate-limits, session data, common lookups.

### Security & privacy

-   **Auth:** OAuth2 / OIDC (support enterprise SSO). Access tokens include `companyId` and `roles`.

-   **RBAC:** central enforcement in API layer + service-level checks.

-   **Data encryption:** TLS in transit; AES-256 encryption-at-rest for attachments and DB encryption at rest.

-   **Consent & GDPR:** explicit consent capture during application; endpoints: `DELETE /api/v1/companies/{companyId}/candidates/{candidateId}?erase=true` implementing right-to-be-forgotten (subject to legal constraints --- retain audit of deletion action without PII).

-   **Attachment handling:** pre-signed URLs for upload, server-side virus scanning pipeline.

-   **CSRF/XSS:** BFF for cookie logic, use JWT in authorization header for SPAs, sanitize all user-generated content and use CSP headers.

### Observability

-   **Metrics:** request latency, error rates, queue lengths, match computation times, pipeline throughput. (Prometheus-compatible metrics)

-   **Logging:** structured logs (JSON) aggregated to centralized log store (e.g., ELK / Loki).

-   **Tracing:** distributed tracing (OpenTelemetry) for request flows across services.

-   **Alerting:** SLO-based alerts (e.g., 99th-percentile API latency, error-rate thresholds).

* * * * *

11\. CI/CD & Testing
--------------------

**CI/CD pipeline stages**

1.  **Build** --- compile, container image build

2.  **Unit & Integration tests** --- run in pipeline with test DB

3.  **Lint & Static Analysis** --- code style, security scans (SAST)

4.  **Contract Tests** --- API consumer/provider checks

5.  **E2E Staging Deploy** --- deploy to staging, run smoke & acceptance tests

6.  **Security Scans** --- dependency scanning, container image scans

7.  **Deploy to Prod** --- gated by approvals; progressive rollout

**Testing recommendations**

-   Unit tests for domain logic (high coverage for status transitions).

-   Integration tests for DB interactions and search indexing.

-   Contract tests for API expectations.

-   E2E tests covering: job publish → application → scheduling flow.

-   Load tests for critical endpoints: application submission and search queries.

**Key test cases**

-   Create and publish job; validate publication to job-board (mocked).

-   Candidate apply with resume file; resume parsed & indexed.

-   MatchScore computed and returned in application listing.

-   Schedule interview across calendars; conflict resolution path.

-   GDPR deletion path removes PII but retains audit markers.

* * * * *

12\. Operational considerations & backups
-----------------------------------------

-   **Backups:** nightly DB backups, point-in-time recovery retained 30 days.

-   **DR:** multi-AZ deployment, Elasticsearch snapshot to S3.

-   **Maintenance windows:** for index re-index and schema migrations, use blue-green or rolling migrations.

* * * * *

13\. Assumptions
----------------

1.  **Third-party integrations** (job boards, calendar APIs) have standard REST interfaces and support OAuth-based auth.

2.  **Resume parsing** can be either a 3rd-party service (i.e., AWS Textract + custom NLP) or an internal pipeline; MVP uses 3rd-party to accelerate time-to-market.

3.  **Initial tenant model**: single-tenant logical separation per company (shared application with `companyId` multitenancy).

4.  **GDPR rules**: deletion removes PII but preserves minimal audit markers; legal review required for edge cases.

5.  **AI model compute** will be async (not blocking the apply request) and run on periodic batch or event-triggered jobs.

6.  **Storage compliance**: S3-compatible storage supports server-side encryption.

* * * * *

14\. Engineering next steps (3--5 actionable items)
--------------------------------------------------

1.  **MVP Tech Spike (2 weeks):** implement Candidate Management + Job Service skeleton, wire Postgres + S3 + Elasticsearch. Deliver APIs for create job, create application, and basic search.

2.  **Integrations PoC (2 weeks):** calendar OAuth flow (Google), job-board connector stub, and resume parser integration (3rd-party).

3.  **AI Match Pipeline (3 weeks):** deliver an offline pipeline prototype that consumes job & resume data and outputs `matchScore` + `explainability` JSON.

4.  **Security & Privacy Implementation (1 sprint):** finalize token claims, RBAC checks, consent capture flow, and deletion endpoint.

5.  **Observability & CI/CD:** set up Prometheus metrics, tracing with OpenTelemetry, logging stack, and a basic pipeline in CI for build/test/deploy.

* * * * *

15\. Appendices --- Additional diagrams & artifacts
-------------------------------------------------

(Already included: PlantUML diagrams above for Use-Cases, High-level architecture, C4 L2-L3.)
