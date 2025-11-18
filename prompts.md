Your mission is to design and document a complete software system following the phases of research & analysis, use-case definition, data modeling, and high-level design.

The system: **LTI**.

LTI is a startup aiming to build the Applicant Tracking System (ATS) of the future. An ATS manages the entire hiring lifecycle — job creation, publication, candidate intake, evaluation, interview scheduling, and final hiring.

An illustrative diagram of a typical ATS lifecycle has been provided (use it as conceptual inspiration; do not describe it).

LTI wants to differentiate through:

- Intelligent automation of hiring workflows  
- Real-time collaboration between recruiters and hiring managers  
- AI-assisted features across the hiring pipeline  
- Time-efficiency improvements for HR teams  
- A modern, delightful experience for candidates and recruiters  

Your mission is to design the MVP for LTI ATS, delivering the following artifacts:

- Short description of the software, value proposition, competitive advantages, main features + Lean Canvas.  
- Three primary use-case descriptions, each with a use-case diagram.  
- Data model with entities, attributes (name + type), relationships (cardinalities).  
- High-level system design, explained + accompanying diagram.  
- C4 diagrams (Levels 1–3) for one chosen component.  

This prompt is intended for **GPT-5** (high-capacity model).

---

## 1️⃣ ROLE & CONTEXT

Adopt the following role:

You are a **Senior Software Architect and Product Lead** with **15+ years of experience** building SaaS platforms for HR Tech (ATS, HRIS). You are an expert in **Domain-Driven Design (DDD)**, **hexagonal architecture**, **microservices**, **event-driven systems**, **C4 modeling**, **UML**, **GDPR compliance**, and **AI-assisted product capabilities**.

You operate in a **startup environment** preparing to raise funding, delivering an **MVP to early customers**, and handing off **formal documentation** to engineering teams.

---

## 2️⃣ TASK

Produce the complete **design documentation** for the **LTI ATS MVP**, including:

- System description & value proposition  
- Lean Canvas  
- 3 use-case specifications + UML diagrams  
- A normalized data model (entities, attributes, relationships)  
- A high-level architecture design + diagram  
- C4 diagrams (context, containers, components) with Level-3 depth for one component  

Output must be in **Markdown**, with diagrams in **PlantUML or Mermaid** code blocks.

---

## 3️⃣ SPECIFICATIONS

Follow these precise **functional and technical requirements**.

---

### 📌 SYSTEM FUNCTIONAL SCOPE

The ATS must support the entire hiring lifecycle:

- Job creation → Job publication  
- Candidate intake  
- Candidate review  
- Interview scheduling  
- Evaluation  
- Hiring / offer  

Differentiating features to design and explain:

- Configurable automation workflows (trigger-based: auto-screening, automatic emails, movement across stages)  
- AI matching engine (CV ↔ Job matching + explainability)  
- Real-time collaboration (comments, tagging, notifications, shared candidate timelines)  
- Interview scheduling integration (Google/Microsoft calendar syncing)  
- Candidate portal (apply, track status, upload files)  
- RBAC (Recruiter, Hiring Manager, Admin, Guest Interviewer)  
- Audit logs, data retention, consent and GDPR compliance  

---

### 📌 DELIVERABLE DETAILS

#### Lean Canvas

Must include nine blocks:

- Problem  
- Customer Segments  
- Unique Value Proposition  
- Solution  
- Key Metrics  
- Unfair Advantage  
- Channels  
- Cost Structure  
- Revenue Streams  

Use concise bullet points.

---

### Use Cases (3 minimum)

1. **Publish Job** – from job draft to multi-channel publication  
2. **Manage Candidates** – screening, shortlisting, tagging, AI suggestions  
3. **Schedule Interview** – calendar invites, conflict resolution, feedback capture  

Each must include:

- Name  
- Actors  
- Preconditions  
- Main Flow (step-by-step)  
- Alternative Flows  
- Postconditions  
- UML Use-Case Diagram (PlantUML or Mermaid)  

---

### Data Model Requirements

Provide:

- Complete entity list  
- For each entity:
  - Description  
  - Attributes with types (UUID, String, Text, Integer, Boolean, Timestamp, Enum, JSON)  
  - Primary key  
  - Foreign keys  
  - Index suggestions  
  - Cardinalities (1:1, 1:N, N:M)  
- Normalize to **3NF**, unless you justify denormalization.  

Include enums, e.g.:

- `ApplicationStatus`  
- `UserRole`  
- `InterviewMode`  

---

### High-Level Architecture

Include:

- Chosen architecture pattern (hexagonal or microservices) and justification  
- Services/components + responsibilities  
- Communication patterns (REST, GraphQL, gRPC, event bus)  
- Storage choices (relational DB, search index, object storage)  
- Third-party integrations (email, calendar, job boards, SSO)  

Deliver a diagram (PlantUML or Mermaid).

---

### C4 Model

Provide:

- **Level 1 — Context**  
- **Level 2 — Containers**  
- **Level 3 — Components** for **one chosen container** (e.g., Candidate Management Service)  

Include responsibilities and major interactions.

---

### NON-FUNCTIONAL REQUIREMENTS

Scalability:  
- Design for **10k → 100k MAU**.  
- Provide scaling strategies for DB, search, and background workers.

GDPR & privacy:  
- Consent flows  
- Right-to-be-forgotten  
- Encryption-at-rest and in-transit  

Observability:  
- Metrics, logs, traces  
- Recommended tools  

Security:  
- RBAC  
- OAuth2/OIDC  
- Safe attachment storage  
- CSRF/XSS protections  

---

## OUTPUT FORMAT

- Entire output in **English**  
- Use **Markdown headings**  
- Diagrams **only** in **PlantUML or Mermaid**  
- Entity & attribute names must be in English  
- Include explicit **Assumptions** section  

---

## 4️⃣ TECHNICAL REQUIREMENTS

- Language: **English**  
- Diagrams: **PlantUML or Mermaid**  
- Naming conventions:
  - Entities → **PascalCase**  
  - Attributes → **camelCase**  
  - Enums → **UPPER_SNAKE_CASE**  
- File path hints:
  - `docs/architecture/`
  - `diagrams/`
  - `models/schema.sql`  
- Data formats: **JSON** for APIs; timestamps in **ISO-8601**  
- API design: provide at least one **sample REST API** for the Candidate Management module  
- Security: **OAuth2/OIDC** with role claims  
- Indexing: propose a **search indexing strategy** and indexable fields  
  (job title, skills, keywords, candidate name, resume text)  
- CI/CD: build → test → lint → security scan → deploy  
- Testing: define test types + key test cases for core flows  

---

## 🧩 METAPROMPT REQUIREMENTS

Your output must:

- Be highly detailed, prescriptive, and unambiguous  
- Enforce technical consistency (data models, diagrams, APIs)  
- Use professional tone and clear formatting  
- Be directly usable by engineers  
- Be reproducible in any environment  

---

## 📌 EXPECTED OUTPUT

GPT-5 must generate:

- System description + value proposition + Lean Canvas  
- Three full use-cases + UML diagrams  
- Full normalized data model (entities, attributes, relationships, PK/FK, indexes)  
- High-level architecture + diagram  
- C4 diagrams (Levels 1–3)  
- API samples, security, scaling, observability, assumptions, and engineering next steps  

---

## 📝 NOTES

- Target model: **GPT-5**  
- Image: use as conceptual reference only  
- Output style: **production-grade documentation** with Markdown + PlantUML/Mermaid  
- Must include:
  - **Assumptions** section  
  - **3–5 next engineering steps**  
