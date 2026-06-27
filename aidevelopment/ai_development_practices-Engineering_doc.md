# Professional Engineering Specification & Process Guide

## 1. Introduction & Core Paradigm

This engineering document outlines the required processes, architectural boundaries, and process guardrails necessary to transition a "vibe-coded" prototype into a highly stable, production-grade application.

The structural integrity of our deployment pipeline relies on a dual-layered operational model:

* **The Gate Paradigm:** Proactive process constraints designed to block defective or unauthorized code and configurations from reaching the production environment.
* **The Net Paradigm:** Reactive architectural patterns, global handlers, and telemetry platforms designed to intercept, isolate, and log anomalies that escape perimeter gates.

---

## 2. Spec-Driven Development (SDD)

To prevent model drifting and logic degradation, all feature engineering must follow strict Spec-Driven Development frameworks (e.g., *OpenSpec* or *GitHub Spec Kit*). This transforms software requirements into enforceable state contracts.

### Operational Sequence

```
[Proposal Generation] ──> [Concrete Specification] ──> [System Design] ──> [Task Isolation] ──> [Implementation/Verification]

```

1. **Proposal Generation:** Detail raw technical intents, APIs, and schema modifications.
2. **Concrete Specification:** Build rigid functional requirements acting as model contracts.
3. **System Design:** Formalize boundaries, service contracts, and structural patterns.
4. **Task Isolation:** Split the monolithic scope into small, digestible work packages.
5. **Implementation/Verification:** Cycle systematically through tasks while maintaining explicit audit trails in a `changes/` directory to preserve system lineage for bug backtracking.

---

## 3. Project Context Engineering & Documentation Architecture

Context pollution is a major failure mode when engineering with LLM agents. Systems must be documented linearly, prioritizing data layers that the models cannot natively resolve.

### Markdown Context Topology

* **Root Level (`.claudemd` / `agent.md`):** Contains high-level project summaries, foundational technological choices, and global constraints.
* **Sub-directory Context Scoping:** Specialized context files nested inside isolated layers (e.g., `/api`, `/ui`). Directories exceeding **20,000 tokens** must be scoped with dedicated sub-agent markdown pointers (such as *Intent Layers* mapping file relationships).
* **Stale Context Elimination:** Code comments and system markdown guidelines must be refactored continuously. Stale documentation causes silent model assumptions, forcing agents to implement legacy design structures.

### Non-Inferable Declarations

Markdown files must prioritize the documentation of:

1. **Anti-patterns:** Explicitly discovered solutions or frameworks that *must not* be utilized under any circumstance.
2. **Non-inferables:** Architectural designs, downstream business logic quirks, or ecosystem conventions that are impossible to extrapolate purely from analyzing raw codebase structures.

---

## 4. Version Control Systems & Pull Request Protocols

Branch protections act as primary infrastructure gates. Uncontrolled modifications directly to target environments are completely prohibited.

### Strict Branch Pipeline

```
[Feature Branch / Worktree] ──> [Development Branch (PR Trigger)] ──> [Main/Production Branch]

```

### Commit and Pull Request Rules

* **Atomic Commits:** Every single commit must be scoped to exactly *one* logical and granular modification.
* **Explaining Intent:** Commit messages must explicitly detail *why* the modification was executed, providing a semantic trace for adversarial review iterations.
* **Branch Protections:** Direct modifications to `main` are permanently blocked. Feature branches must target `develop`. Merging into `main` can only occur through programmatic promotions from `develop` once automated validation suites pass.
* **State Recovery:** Every commit state must remain clean, ensuring the immediate capability to rollback and recover application systems to known historical baselines during catastrophic outages.

---

## 5. Test-Driven Development (TDD) Framework

Testing acts as an automated validation layer that forces coding agents away from lazy execution vectors.

### The Red-Green-Refactor Cycle

1. **Red:** Write an intentionally failing unit or integration test case to prove the system lacks the feature or harbors the bug.
2. **Green:** Implement the minimum possible code surface necessary to flip the test to a passing state.
3. **Refactor:** Polish, clean, and optimize the newly introduced codebase infrastructure while maintaining green assertions.

### Testing Architecture Matrix

| Test Type | Scope & Focus | Velocity |
| --- | --- | --- |
| **Unit Tests** | High isolation, individual functions, mathematical/deterministic validation. | Near-Instantaneous |
| **Integration Tests** | Core multi-service interactions, system handshakes, and database layers. | Moderate |
| **End-to-End (E2E)** | Critical transaction/value paths via *Playwright* (e.g., Stripe subscription checkout). | Slow / Heavy Resource |

* **Regression Safeguards:** Every single reported production bug must be immediately translated into an automated regression test prior to deploying hotfixes.
* **Evasion Defenses:** Guard rails must explicitly block agent testing anti-patterns, preventing models from writing non-assertive, self-passing test files.

---

## 6. Security, Authentication, & Authorization Mechanics

A crisp boundary must separate user identity verification from capability access rights.

* **Authentication (AuthN):** Validates the cryptographic identity of the requester (*"Who are you?"*).
* **Authorization (AuthZ):** Dictates resource entity consumption bounds (*"What are you allowed to do?"*).

### Defense-in-Depth Implementation

* **Server-Side Checkpoints:** Multi-tenant constraints must be explicitly executed within API routing levels and controller logic layers. Models cannot be trusted to naturally infer tenant isolation maps.
* **Database Perimeter Enforcement:** Enable database row-level security (**RLS**) globally by default. All data-access mutations must clear RLS policies to block data leaks if the API level suffers logic bypasses.
* **Secret Protection Mechanics:** Raw keys, application salts, or connection strings must never exist within code surfaces. Implement local **pre-commit hook scanners** to intercept human or agent hard-coding habits before commits reach remote hosting targets.

---

## 7. Fault Isolation & Error Boundary Architecture

Applications must be structured to maximize blast radius containment. Single service failures must never cascade into general application outages.

### Error Strategy

* **Expected Failures:** Known API bottlenecks or edge network timeouts must be caught early, triggering explicit retry policies, back-off mechanisms, or clean, readable error updates for end users.
* **Unexpected Failures:** Edge-case bugs must fall back safely into global exception boundaries. System stack traces must be scrubbed completely from public surfaces to prevent sensitive data exposures.
* **External Integration Isolation:** Outbound vendor API queries (e.g., LLM inferences, text-to-speech engines) must be wrapped inside strict timeouts and circuit breakers to avoid compounding cascading failures and service blocks.

---

## 8. Database Engineering, Migrations, and Performance Optimization

Production databases require structured schema controls and proactive access tuning to survive enterprise load profiles.

### Schema Version Tracking

* Every structural database mutation (table structures, indexing configurations, column data type adjustments) must be run through a managed tracking framework. Raw database structural hacking is strictly banned.

### High-Volume Query Optimization

* **Alphabetic Optimization (Indexing):** Any data field subjected to frequent lookup operations or sorting patterns must have dedicated structural indexing. This prevents expensive sequential table scans.
* **N+1 Query Resolution:** Proactively audit LLM database handlers to eliminate iterative, multi-trip requests. Implement clean joins, lookups, and batch queries to optimize processing roundtrips.

---

## 9. Continuous Integration & Safe Deployment Pipelines (CI/CD)

Manual deployment pushes from local workspaces are prohibited. Environmental promotion must remain programmatic.

### Pipeline Pipeline Actions (GitHub Actions Workflow)

```
[Local Push/PR] ──> [Code Linting] ──> [Type Verification] ──> [Test Suites Run] ──> [Production Build Execution]

```

### Deployment Strategy

1. **Isolation of Staging:** Build all system iterations inside ephemeral preview instances (e.g., *Vercel Preview Deployments*).
2. **Manual Gatekeeping:** Run dedicated human verification loops across generated preview URLs prior to promoting changes into the live `main` workspace branch.
3. **The Database Expansion Pattern (Expand-Migrate-Contract):**
* **Expand:** Safely inject new structural tables or columns into production database schemas. Ensure existing live production code remains completely unaffected.
* **Migrate:** Backfill data fields across the updated schema structures and systematically deploy updated codebase services that read from the new system layout.
* **Contract:** Once telemetry tools confirm structural stability, run cleanup workflows to delete legacy schema assets and deprecate unused columns without introducing structural downtime.



---

## 10. Observability, Metrics, and System Telemetry

An app is blind without live operation insights. Telemetry frameworks must collect explicit data states directly across remote clients.

* **Centralized Logging:** Track operations via unique request IDs linked across downstream systems, allowing engineering teams to follow full transaction lifecycles.
* **Metric Dashboards:** Keep real-time visual metrics tracking error rates, latency spikes, and system edge failures (e.g., *AppSignal* or *Sentry* dashboard analytics).
* **Automated Alerting Triggers:** Configure immediate warning thresholds on all unhandled boundary errors. Engineers must find, trace, and patch faults proactively before users submit trouble reports.

   [ ]  Enforce OpenSpec/Spec Kit contract patterns before generating code.
   [ ]  Scrape legacy code comments and build modular subdirectory .claudemd contexts.
   [ ]  Lock down master branch protections: Feature -> Develop -> Main.
   [ ]  Build automated regression checks for all discovered software bugs.
   [ ]  Enable global Supabase Row-Level Security (RLS) toggles by default.
   [ ]  Install local pre-commit hook tools to block secret exposures.
   [ ]  Wrap outbound network integrations in strict timeouts and error boundaries.
   [ ]  Run schema changes through the Expand-Migrate-Contract design model.
   [ ]  Deploy via automated CI/CD pipelines backed by ephemeral staging URLs.
   [ ]  Link error boundaries to Sentry alert systems to catch unhandled errors early.

