# FinanceFlow AI — Roadmap

## Status and delivery policy

This roadmap is a dependency-based plan, not an implementation inventory or a schedule. No dates are assigned without delivery evidence. [Product Vision](product-vision.md) defines purpose and [MVP Scope](mvp-scope.md) defines the first usable release and its acceptance IDs.

M0–M3 deliver the financial MVP. M4 is the next product priority after that core is stable. M5–M7 extend it without making M3 depend on investments, cloud infrastructure, or mobile.

## Milestones

### M0 — Engineering Foundation

**Dependencies:** approved product direction and scope.

**Deliverables:**

- Product vision, MVP contract, roadmap, and an initial architecture decision backlog.
- English technical and GitHub artifacts, Conventional Commits, issue/PR conventions, review requirements, and GitHub Projects organization.
- Follow-up issues for AGENTS.md, reusable Codex Skills, development-agent context/policies, and documentation of AI-assisted development.
- CI and test strategy, security/accessibility expectations, and a prioritized implementation backlog linked to acceptance IDs.

**Completion:** documentation is reviewed, unresolved decisions have owners through follow-up issues, and the first vertical slice has a clear acceptance plan. Issue #1 delivers only the three planning documents; it does not complete every M0 deliverable or create agent configurations, workflows, or ADR files.

**Risks:** process overhead and speculative tooling. Keep conventions minimal; add automation when an implementation can benefit from it. Development-agent setup does not require multi-agent work.

### M1 — Web Foundation & Design System

**Dependencies:** M0's relevant conventions and acceptance priorities.

**Deliverables:**

- React, TypeScript, and Vite application shell with navigation and a restrained reusable visual system.
- Responsive layouts, accessible form/table patterns, chart alternatives, review states, and internationalization-ready text/formatting.
- IndexedDB storage boundary, schema versioning, test isolation, and demo-workspace separation.
- CI checks for types, lint, tests, and build; initial end-to-end coverage when the first complete flow exists.

**Completion:** a minimal synthetic account workflow persists across reload and has a reviewed storage/error contract. Representative empty, loading, invalid, and small-screen states work with a keyboard. Establish MVP-AC-01, 14, and 16 foundations without claiming the full MVP passes.

**Risks:** building an oversized design system or generic persistence framework. Extract only patterns used by actual financial workflows. Establish migration safety now, before changing persisted schemas.

### M2 — Banking Data & Financial Core

**Dependencies:** M1 storage/UI contracts, safe monetary rules, and supported-profile evidence decisions.

**Deliverables:**

- Institutions, accounts, opening-balance references, deterministic money/date rules, and coverage metadata.
- Staged import pipeline with profile detection, preview, diagnostics, accepted-row planning, and atomic persistence.
- Modular adapters for Ibercaja, Openbank, and imagin; coverage of CSV, XLS, XLSX, and HTML-with-XLS-extension through an explicit profile matrix.
- Duplicate prevention and persistent human decisions for uncertain cases.
- Editable categories, manual expense attribution using `self`/`partner`/`joint`/`unassigned` with localizable labels, financial types, and movement search/filtering. Attribution is independent of the paying account, category, and financial type.
- Independent synthetic fixture corpus, adversarial file tests, and profile evidence status.

**Completion:** the automated synthetic portions of MVP-AC-01–05, 08, and 11 are demonstrated for committed profiles; input-safety checks from MVP-AC-15 pass for the supported pipeline. Publish profile/version, fixture coverage, and owner-local verification status separately. M2 implementation readiness does not establish full bank-format validation: the owner-local portion of MVP-AC-02 must pass by M3. Unsupported or unverified variants are labeled honestly. Missing profile evidence remains an explicit release blocker; synthetic success must not be reported as real-bank verification.

**Risks:** false deduplication, format drift, parser exposure, and lack of structural evidence without real statements. Limit profiles deliberately and keep ambiguous cases reviewable. The project owner alone performs real-statement checks locally; Codex must not access those files, and real, anonymized, or statement-derived files must not be uploaded to GitHub.

### M3 — Reconciliation & Dashboard

**Dependencies:** M2 normalized movements, provenance, duplicate identity, and classification rules.

**Deliverables:**

- Reviewable one-to-one transfer proposals, confirmation/rejection, atomic reversal, and decision history.
- Explicit unmatched own-account transfers and separate investment contributions without position tracking.
- Professional dashboard for income, spending, period savings, account balances, and evolution, including confirmed/provisional distinctions and incomplete-data warnings.
- Complete versioned export, validated replacement restore, safe migrations, and resettable synthetic demonstration mode.
- Financial regression coverage, import-to-dashboard E2E tests, accessibility review, baseline security review, and measured import limits.

**Completion:** all MVP-AC-01–16 pass with recorded evidence. In addition to independent automated synthetic tests, the project owner must successfully verify real statements on their own computer for every committed Ibercaja, Openbank, and imagin institution/product/format profile. Until then, the MVP cannot be declared fully validated for those institutions. Record only non-sensitive technical evidence: profile identifier, profile/adapter and application versions, movement count, sanitized incidents, and check outcomes for normalization, counts, reimport, and applicable reconciliation/indicator rules. Codex must not access the files; no real, anonymized, or statement-derived files, screenshots, or raw logs may be uploaded to GitHub. Follow the reporting limits in [MVP Scope](mvp-scope.md). Failed imports/restores preserve valid data, the full synthetic journey is usable, and known profile limitations are published. This is the first genuinely usable release, not merely a visual prototype.

**Risks:** misleading totals from missing history, reconciliation errors, local data loss, and late backup complexity. Recovery design starts in M1; M3 completes and proves it. Test cross-period transfers, pending classifications, and later edits before approving release.

### M4 — Agentic AI Copilot

**Dependencies:** stable M3 domain/use-case contracts, data-quality indicators, reversible actions, and a reviewed privacy/service-boundary ADR.

**Deliverables:**

- Node.js and TypeScript orchestration service with server-held provider credentials and a deliberately small typed tool catalog.
- Read-only financial queries and proposed classifications/actions using validated structured outputs.
- Human approval UI, authorization checks, stale-state detection, deterministic command execution, and safe retries.
- Guardrails for untrusted statement text and model output; consent and minimization before any external data transfer.
- Synthetic evaluation tasks, tracing/observability without sensitive payloads, provider failure handling, and cost/usage controls.

**Proposed local-first connection:** the client remains the authority for its IndexedDB workspace. For an authorized request, it exposes only validated, scoped data through a controlled bridge. The Node.js service orchestrates tools and proposals; it has no direct browser database access. The client validates a proposed command, shows its concrete effects, obtains approval, rechecks the referenced state, and applies the local use case atomically. No arbitrary JavaScript, SQL, unrestricted queries, or blind execution of model output.

The transport, service hosting, tool protocol, and identity mechanism are ADR decisions. The MVP's lack of authentication is not permission to expose an unrestricted public inference endpoint. An externally hosted service needs access control, rate/cost limits, deployment hardening, and an explicit retention policy. A local-only service has a different threat model that must also be reviewed.

**Completion criteria:**

| ID | Required evidence |
| --- | --- |
| AI-AC-01 | Tool inputs and outputs are runtime-validated; unauthorized scopes, malformed calls, and unsupported actions are rejected. |
| AI-AC-02 | Financial answers use deterministic tools and expose the relevant period, coverage, and uncertainty. |
| AI-AC-03 | Every mutation requires approval of a concrete proposal; declined, stale, replayed, or invalid proposals cannot silently modify data. |
| AI-AC-04 | An independently specified synthetic evaluation set covers useful queries, classifications, ambiguity, prompt injection, and provider/tool failures; release thresholds are agreed before implementation is judged. |
| AI-AC-05 | Traces correlate orchestration, tool outcome, validation, and approval without raw financial content; data sharing and consent are verified. |
| AI-AC-06 | Credentials stay outside the browser/repository, and the chosen deployment passes its access, abuse, and cost-control checks. |

**Risks:** prompt injection through imported descriptions, data disclosure, hallucinated financial claims, stale proposals, and uncontrolled cost. Start with one orchestration flow. Add multi-agent coordination only for a demonstrated responsibility split and measured benefit against the simpler baseline.

### M5 — Advanced Personal Finance

**Dependencies:** stable financial semantics; M4 is the planned preceding priority, although deterministic finance features must not require AI to function.

**Deliverables:** separately scoped iterations for budgets, goals, advanced shared expenses, debts, investments, and wealth. Include later configurable column mapping, Revolut adapters and multicurrency, MyInvestor positions, and DWS/other fund valuation where justified.

**Completion:** each selected iteration has its own issue, financial rules, ADRs where needed, synthetic tests, and acceptance evidence. M5 is not a commitment to implement all advanced capabilities in one release.

**Risks:** conflating contribution flows with holdings or returns; exchange-rate semantics; shared ownership and settlement complexity. Define models and data sources before implementation. Do not treat investment products as ordinary cash accounts.

### M6 — Portfolio Release

**Dependencies:** validated M3/M4 outcomes and the explicitly selected M5 release scope; completing every possible M5 feature is not a prerequisite.

**Deliverables:** release-wide E2E regression, expanded security/accessibility review, complete setup and architecture documentation, CI/CD and release process, and a public demonstration containing only synthetic data. Show the copilot's evaluation and privacy boundaries alongside product behavior.

**Completion:** a reviewer can reproduce the documented release, explore the synthetic demo safely, and inspect passing checks and known limitations. Publishing and infrastructure changes require a separately authorized implementation task.

**Risks:** postponing basic quality to the portfolio phase or publishing private data. M6 consolidates checks that started earlier; it is not their first appearance. Keep public demo storage and any copilot service isolated from personal workspaces.

### M7 — Mobile

**Dependencies:** stable domain contracts, validated mobile user journeys, and a mobile persistence/synchronization decision. Planned after M6 unless the roadmap is explicitly revised.

**Deliverables:** React Native and Expo application reusing framework-independent financial rules and compatible use cases. Add platform-specific UI, storage, file access, and accessibility behavior rather than assuming web components or IndexedDB will transfer unchanged.

**Completion:** selected mobile journeys meet their own acceptance criteria, share validated financial behavior with web, and have explicit backup/synchronization boundaries.

**Risks:** premature abstraction, fragmented data, and assuming cloud sync already exists. Extract shared packages only when responsibilities and real consumers justify them.

## Architecture direction and pending ADRs

The approved MVP direction is local-first with a framework-independent domain. Node.js is planned for M4; it may be introduced earlier only through a documented concrete need, such as an integration requiring protected server credentials. Backend-first persistence, microservices, event infrastructure, and account authentication are not MVP defaults.

These are future ADR topics, not files created by Issue #1:

| ADR topic | Resolve by | Decision to record |
| --- | --- | --- |
| Local-first boundaries | M1 | Durability assumptions, storage contract, demo isolation, offline scope, and absence of server sync. |
| Money, dates, and balance references | M2 | Safe representation, limits, date semantics, opening balances, coverage, and later currency extensibility. |
| Financial classification and metrics | M2/M3 | Unknowns, unmatched transfers, contributions, refunds/reversals/fees, confirmed/provisional indicators. |
| Import profiles and parsing | M2 | Evidence, content detection, parser selection, profile versioning, supported matrix, and resource limits. |
| Identity and provenance | M2 | Reimport identity, legitimate repetitions, retained source metadata, and correction behavior. |
| Reconciliation | M3 | Candidate window/evidence, one-to-one rules, approval, reversal, and conflicting edits. |
| Local recovery | Design in M1; prove in M3 | Schema migration, export versioning, integrity checks, atomic replacement, and failure recovery. |
| Frontend security and dependencies | Initial controls in M1; release review in M3 | Script policy, parser isolation if justified, dependency review, and sensitive-data handling. |
| Copilot boundary and orchestration | Before M4 implementation | Service deployment/identity, bridge protocol, typed tool catalog, consent, retention, approval, and evaluation gates. |
| Shared/mobile architecture | M7 | Actual shared modules, platform storage, backup, and any synchronization. |

## Cross-cutting engineering workflow

Use **GitHub Issue → Branch → Implementation → Tests → Code Review → Pull Request → CI → Merge**. Local tests and review prepare a PR; required automated checks run on the PR before merge. Use Conventional Commits, issue-linked acceptance criteria, GitHub Projects, GitHub Actions, and ADRs where decisions warrant them. These are intended practices, not claims that automation is already configured.

Keep code, identifiers, comments, documentation, issues, PRs, and commits in English. Initial Spanish UI text must be separable from financial rules. Maintain a record of AI-assisted development without embedding private data or treating generated output as verified by default.

Testing grows with each vertical slice: deterministic financial tests, parser/adapter tests, IndexedDB integration tests, synthetic E2E journeys, and manual usability/accessibility reviews. Re-run the relevant checks when changed behavior warrants it; avoid coverage targets that reward implementation-mirroring tests.

## Issue #1 review checklist

- [ ] The three English documents are coherent and reviewed against the approved product decisions.
- [ ] Local-first MVP, initial institution targets, format/profile limits, expense attribution, and investment contribution treatment are explicit.
- [ ] Financial rules and MVP-AC identifiers are verifiable, including uncertainty, duplicate safety, reconciliation, and recovery.
- [ ] M0–M7 include dependencies, deliverables, risks, and exit criteria; M4 is the priority after stabilization.
- [ ] Codex-assisted development is separated from product AI, and future technologies are not described as implemented.
- [ ] Privacy limits, independent automated synthetic tests, mandatory owner-local real-format verification with non-sensitive evidence, pending ADRs, and excluded scope are explicit.
- [ ] Only the three authorized documents have been added; no application code, dependencies, agent configurations, commits, pushes, or PRs are part of this task.

The checklist remains subject to human review. Creating these documents does not close the GitHub issue or assert that its remote body has been inspected.
