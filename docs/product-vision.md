# FinanceFlow AI — Product Vision

## Status and purpose

This document records the product direction reviewed through PR #2, which closed Issue #1. It describes intended behavior, not capabilities already implemented. The repository currently has no application implementation. The MVP contract is defined in [MVP Scope](mvp-scope.md); sequencing, dependencies, and decision gates are defined in [Roadmap](roadmap.md).

## Problem statement

Personal financial activity is distributed across accounts and institutions. Statements use inconsistent formats, overlapping exports can introduce duplicates, and transfers between owned accounts can inflate income and spending. A useful financial overview requires trustworthy normalization, reconciliation, and an explanation of incomplete or uncertain data.

FinanceFlow AI will turn those statements into a coherent financial workspace while keeping the user in control of imported data and financial decisions. Manual entry alone does not solve the primary problem.

## Two equally important outcomes

1. **Functional outcome:** deliver a real application for managing bank accounts, expenses, income, and savings, later extending to investments and wealth.
2. **Professional outcome:** demonstrate engineering judgment through React, TypeScript, Node.js when justified, architecture, testing, CI/CD, GitHub collaboration, and evaluated agent orchestration.

Portfolio value must come from working behavior, financial correctness, maintainability, and evidence. Technology count and superficial demonstrations are not success measures.

## Initial user and jobs to be done

The initial user manages several personal accounts and wants to:

- Consolidate statements without uploading them to an application server.
- Reimport overlapping statements without losing legitimate transactions or creating duplicates.
- Distinguish consumption from internal transfers and investment contributions.
- Review uncertain classifications and transfer matches before relying on totals.
- Attribute expenses manually using the internal identifiers `self`, `partner`, `joint`, or `unassigned`, with adaptable, localizable display labels.
- Understand income, spending, period savings, balances, and their evolution.
- Recover the workspace from a verified backup.

Household attribution is metadata within one local workspace, independent of category and financial transaction type. The paying bank account does not necessarily determine whom an expense belongs to. Attribution does not imply shared access, ownership percentages, debt allocation, or settlement between people.

## Core journey

1. Create institutions and accounts with their currency and opening-balance reference.
2. Select an account and a supported statement profile.
3. Inspect detected content, normalized rows, duplicate candidates, and validation errors.
4. Confirm an explicit import plan; save the accepted batch atomically.
5. Review transaction classifications and proposed internal transfer matches.
6. Confirm or reject matches, assign categories and expense attribution, and inspect unresolved items.
7. Read financial indicators alongside their coverage and uncertainty.
8. Export a versioned backup and restore it into a validated workspace.

## Product principles

- **Correctness before apparent completeness.** Unknown transactions remain unresolved; missing history must not be presented as a current bank balance.
- **Explainable decisions.** Users can understand import diagnostics, duplicate handling, classifications, and reconciliation evidence. Reconciliation can be reversed.
- **Local-first by default.** The MVP stores financial data in IndexedDB and does not require a backend or authentication. Browser storage is not a guaranteed backup or an encrypted vault.
- **Deterministic financial rules.** Financial calculations and invariants do not depend on model output.
- **Privacy by design.** Development and public demonstrations use data generated from scratch. Real statements and anonymized real statements are excluded from repository fixtures.
- **Excellent usable design.** Responsive layouts, accessible controls, clear charts, useful empty states, and actionable error messages are product requirements.
- **Small, meaningful modules.** Isolate financial rules, import adapters, use cases, storage, and presentation. Avoid microservices and speculative package boundaries.
- **Honest delivery.** Supported profiles, limitations, planned capabilities, and implemented behavior must remain distinguishable.

## Financial concepts and boundaries

| Concept | Responsibility |
| --- | --- |
| Financial Institution | Identifies the provider without embedding its name in financial rules. |
| Financial Account / Product | Represents a tracked banking account or a future financial product; product-specific capabilities remain explicit. |
| Transaction | Represents an account movement with safe money values, financial dates, provenance, classification, and review state. |
| Import Adapter | Converts a documented statement profile into the common transaction model and diagnostics. |
| Financial Asset / Investment Position | Future investment concept; not a disguised bank account and not implemented in the MVP. |

Institution, product, file format, and adapter are different concepts. An institution can offer multiple products and statement profiles. Identifying an institution does not establish support for all its products.

### Initial and future institution coverage

| Institution or product | Planned coverage |
| --- | --- |
| Ibercaja | MVP adapters for explicitly documented EUR banking statement profiles. |
| Openbank | MVP adapters for explicitly documented EUR banking statement profiles; DWS valuation is excluded. |
| imagin | MVP adapters for explicitly documented EUR banking statement profiles; no assumed support for every card or Bizum export. |
| Revolut | Later dedicated imports and multicurrency behavior. |
| MyInvestor | Later contributions, investment positions, funds, equities, and wealth features. |
| DWS and other funds | Later valuation and investment performance. |

The MVP format families are CSV, XLS, XLSX, and HTML exported with an XLS extension. This is not a promise that every institution supports every family. The support matrix and verification gates in [MVP Scope](mvp-scope.md) define that commitment.

## MVP boundary

The first usable release ends at M3: multiple EUR accounts, supported statement imports, normalization, duplicate prevention, reversible reconciliation, editable categories, manual expense attribution, transaction search, trustworthy financial indicators, local persistence, verified backup recovery, and a synthetic demonstration mode.

Advanced budgets, financial goals, shared-expense settlement, investments, complete wealth reporting, cloud synchronization, bank API connections, and mobile applications are outside this release. Investment contributions are classified separately from consumption without pretending that investment holdings are tracked.

The initial UI may be Spanish. UI text, dates, and number presentation must be organized for later internationalization; identifiers, technical documentation, and GitHub artifacts use English.

## Success signals and evidence

| Outcome | Evidence |
| --- | --- |
| Reliable consolidation | Automated synthetic profile tests pass independently of the owner's required local checks with real statements for every committed profile, as defined in the MVP contract. Only non-sensitive technical evidence is recorded. |
| Safe reimport | Repeated and overlapping imports satisfy identifiable duplicate acceptance criteria. |
| Trustworthy overview | Transfers, investment contributions, unresolved items, and incomplete balances follow explicit indicator rules. |
| User control | Ambiguous matches can be reviewed and reconciliation can be undone without destroying source movements. |
| Recoverability | Export and restore preserve the full workspace and invalid backups do not overwrite it. |
| Engineering quality | Reviewable issues and PRs, passing automated checks, meaningful tests, and documented architecture decisions. |
| Usable presentation | The complete import-to-dashboard journey passes responsive and accessibility review. |

These are release evidence requirements, not claims about current usage or measured adoption. Later personal-use feedback can inform priorities without storing personal financial data in the repository.

## AI: two separate responsibilities

### AI-assisted development

Codex may help plan, implement, test, and review changes. Human review, repository policy, and CI remain necessary. AGENTS.md, reusable Skills, and development-agent policies belong to a later issue within M0; Issue #1 does not create them.

### Agentic AI Copilot in the product

M4 is an explicit priority immediately after the financial MVP is stable. It introduces Node.js and TypeScript for provider credentials, tool orchestration, validation, and operational controls when those capabilities are implemented.

The initial copilot can query authorized information and propose classifications or actions. Its engineering evidence must include typed tools, structured outputs, human approval, guardrails, evaluations, tracing, and privacy-aware integration. Multiple agents require an identified coordination need and comparative evaluation; they are not a prerequisite.

The copilot does not replace financial calculations. Model output is untrusted. No financial mutation may execute without application validation and explicit human approval of the proposed change. External processing requires a clear account of what data leaves the device and the user's consent.

## Principal risks

- Statement profiles can change, and synthetic fixtures alone cannot prove current compatibility with actual institution exports.
- False duplicate decisions can remove legitimate activity; false transfer matches can distort metrics.
- Incomplete history can make attractive dashboards misleading unless coverage is visible.
- Browser eviction, device loss, and failed migrations can cause data loss without verified backups.
- Rendering imported text or parsing complex files creates a security boundary; local storage does not remove this risk.
- The combined finance and AI ambitions can expand scope. M3 correctness is the dependency for M4, and later features must not displace it.

Risk controls, acceptance criteria, and unresolved decisions are detailed in the companion documents.
