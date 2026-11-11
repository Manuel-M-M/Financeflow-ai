# FinanceFlow AI

A personal finance workspace in development, designed to turn fragmented bank statements into a trustworthy view of income, spending, savings, and account balances.

**Current stage: product planning and repository preparation.** There is no application implementation, runnable application, or demo yet. The repository contains the product vision, MVP acceptance contract, and delivery roadmap. The technologies and product capabilities below are planned unless explicitly stated otherwise.

[Product vision](docs/product-vision.md) · [MVP scope](docs/mvp-scope.md) · [Roadmap](docs/roadmap.md)

## The problem

Managing several bank accounts often means working with inconsistent statement formats, overlapping exports, and transfers that can look like income or spending. A useful dashboard needs reliable normalization and reconciliation before it needs more charts.

FinanceFlow AI aims to make those decisions visible and reviewable: preview imports, resolve uncertain duplicates, confirm internal transfers, and understand which totals are complete or still provisional.

## Planned MVP

The first usable release is defined at milestone M3. It will target one local workspace and EUR banking activity:

- Manage multiple institutions and accounts with opening balances and explicit history coverage.
- Import documented statement profiles for Ibercaja, Openbank, and imagin across CSV, XLS, XLSX, and HTML exported with an XLS extension. Exact supported combinations remain unverified; this is not universal bank-format support.
- Preview normalization and validation results before an atomic import; prevent accidental reimports while preserving legitimate similar transactions.
- Review, confirm, reject, and reverse internal transfer reconciliation.
- Edit categories, manually attribute expenses, and search or filter transactions.
- Show income, expenses, period savings, and balances with unresolved movements and incomplete coverage visible.
- Persist locally, export and validate versioned backups, and provide an isolated demonstration mode using data generated from scratch.

Investments, cloud synchronization, bank API connections, shared-expense settlement, mobile apps, and the product AI copilot are outside the MVP. Investment contributions will be classified separately without claiming to track holdings or returns.

The [MVP scope](docs/mvp-scope.md) defines all 16 acceptance criteria, exclusions, and profile-verification gates. None has been demonstrated by application code yet.

## Architecture direction

| Planned component | Intended responsibility |
| --- | --- |
| React + TypeScript | Responsive, accessible interfaces and explicit review, error, and uncertainty states. |
| Vite | Web development and production build tooling. |
| Independent financial domain | Deterministic money, date, classification, and reconciliation rules, decoupled from React, persistence, parsers, and AI providers. |
| Import adapters and application use cases | Profile-specific normalization, validation, provenance, and atomic workflow boundaries. |
| IndexedDB | Browser persistence with schema migrations, recovery, and demo isolation. |

The MVP direction requires neither a backend nor authentication. Local browser storage is not an encrypted vault or a guaranteed backup. Architecture decision records, storage implementation, and parser selection are future work.

Node.js and TypeScript are planned for the later Agentic AI Copilot service: protected provider credentials, typed tools, validated proposals, and operational controls. Future investment features will need their own financial models. React Native and Expo are planned after the web release, with shared domain rules and platform-specific UI and storage.

## AI-assisted engineering

AI assistance during development and AI features in the product are separate responsibilities.

**Evidence available today:** Codex-assisted planning and repository review, issue-scoped documentation, reviewable commits, and the planning documents integrated through [Pull Request #2](https://github.com/Manuel-M-M/Financeflow-ai/pull/2), closing [Issue #1](https://github.com/Manuel-M-M/Financeflow-ai/issues/1), with human review and approval. Git history preserves the changes and merge; it does not by itself prove the quality of AI output.

The engineering approach uses GitHub Issues for scope, focused branches and commits for traceability, and Pull Requests for human review. Codex can help draft, implement, inspect, and validate work; generated output must be checked against acceptance criteria. Changes remain subject to explicit human approval before commits, pushes, and publication under the current workflow.

**Still planned:** application tests, CI, architecture decision records, reusable agent instructions, and evaluations of the future product copilot. No working product AI, automated engineering pipeline, or multi-agent system is claimed here.

The future copilot must use deterministic financial tools, treat model output and imported text as untrusted, obtain explicit approval for mutations, and minimize external data sharing with user consent. These are design requirements, not implemented safeguards.

## Privacy, accessibility, and quality

The planned product and its implementation will be evaluated against these principles:

- **Privacy:** financial processing stays on the device in the MVP. Repository fixtures and public demos must use data generated from scratch, never real or anonymized statements. Real-format checks are performed privately by the owner under the reporting limits in the MVP scope.
- **Financial correctness:** integer minor units, explicit currency and calendar-date semantics, deterministic calculations, visible uncertainty, and reversible reconciliation.
- **Accessibility:** responsive core journeys targeting WCAG 2.2 AA, with keyboard, focus, form feedback, contrast, and chart alternatives reviewed. No conformance certification is claimed.
- **Security:** imports and backups are untrusted input; parsing limits, inert rendering, dependency review, and safe recovery are required. Credentials must stay out of browser code and Git.
- **Quality:** meaningful domain, parser, persistence, and end-to-end tests, followed by CI checks as implementation begins. No test suite, CI workflow, coverage metric, or performance result exists yet.

### Private file conventions

Keep real financial documents outside this repository. As additional protection against accidental staging, `.gitignore` reserves these root directories:

| Ignored path | Purpose |
| --- | --- |
| `/private-data/` | Accidental local placement of private statements or exports. |
| `/personal-backups/` and `/backups/` | Personal workspace exports and recovery files. |
| `/secrets/` | Local credentials and secret material. |
| `/.local/` | Machine-specific configuration that may contain private values. |

Environment files, common credential files, private keys, and sensitive local configuration patterns are also ignored. These rules are safeguards, not permission to supply private files to Codex or commit them. They do not protect files already tracked or force-added to Git.

Future synthetic fixtures may be versioned under `tests/fixtures/` outside the private paths; CSV, XLS, and XLSX are not globally ignored. `.env.example` remains versionable and must contain placeholders only. These are conventions; the directories and example file have not been created.

## Roadmap

| Milestone | Planned outcome |
| --- | --- |
| M0 — Engineering Foundation | Planning, review conventions, acceptance priorities, and implementation foundations. Planning documents are merged; M0 is not complete. |
| M1 — Web Foundation & Design System | Application shell, accessible UI foundations, and storage boundaries. |
| M2 — Banking Data & Financial Core | Profile-based imports, normalization, duplicate handling, and financial rules. |
| M3 — Reconciliation & Dashboard | Reviewable reconciliation, trustworthy indicators, recovery, and the first validated MVP. |
| M4 — Agentic AI Copilot | Node.js orchestration, validated tools, human-approved actions, and evaluated privacy boundaries. |
| M5 — Advanced Personal Finance | Separately scoped budgets, goals, investments, multicurrency, and wealth features. |
| M6 — Portfolio Release | Reproducible release, synthetic public demo, and consolidated quality evidence. |
| M7 — Mobile | React Native and Expo journeys with appropriate platform boundaries. |

Milestones express dependencies, not delivery dates. M4 follows a stable M3; the [full roadmap](docs/roadmap.md) defines exit criteria and pending decisions. An early public repository would still be an under-development project, distinct from the M6 portfolio release.

## Running the project

There is no runnable application or demo, no dependency manifest, and no setup or start command yet. Application setup instructions will be added with the first implementation slice.

## Before public visibility

This issue prepares the repository while it remains private. A local Git audit does not establish the safety of all GitHub content or settings. The following remote checks remain pending manual review:

- Issues, Pull Requests, review threads, comments, and their uploaded attachments, including closed items.
- Actions workflows, run logs, artifacts, caches, and their access and retention settings.
- Repository settings: collaborators and permissions, branch rules, required reviews, Actions permissions, secrets and variables, integrations, webhooks, and security alerts.
- Remote branches, tags, and available history beyond the locally inspected references.
- Releases and uploaded assets; packages, deployments, and any hosted demo.
- Any Wiki, Discussions, or Projects content and linked files associated with the repository.

Existing commit identities and history are intentionally retained. Historical author and committer names and email addresses will remain discoverable if the repository becomes public; future local commits are configured to use GitHub noreply. No remote-content audit or visibility change is claimed by this documentation update. Publication requires a separate human decision after these checks.

## License

No license has been selected or added. Making the repository public does not automatically grant general permission to reuse, modify, or redistribute its contents. License selection remains a separate owner decision before any broader reuse grant.
