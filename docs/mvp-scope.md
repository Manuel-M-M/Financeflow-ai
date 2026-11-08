# FinanceFlow AI — MVP Scope

## Status and release boundary

This is the planned acceptance contract for the first usable release, completed at M3. No capability is claimed as implemented by this document. See [Product Vision](product-vision.md) for purpose and [Roadmap](roadmap.md) for delivery gates.

The MVP is a single local workspace using React, TypeScript, Vite, and IndexedDB. It requires neither a backend nor authentication. Supported financial operations use EUR only. Non-EUR movements must be rejected or held outside the committed financial dataset with an explicit diagnostic; they must never be silently converted or aggregated.

## Functional requirements

| ID | Requirement |
| --- | --- |
| MVP-REQ-01 | Create and manage multiple institutions and EUR banking accounts, with opening-balance amount, reference date, and coverage information. Archive accounts with history rather than silently deleting dependent records. |
| MVP-REQ-02 | Import supported CSV, XLS, XLSX, and HTML-with-XLS-extension profiles through modular adapters for Ibercaja, Openbank, and imagin. |
| MVP-REQ-03 | Show detected profile, selected account, normalized rows, diagnostics, and an explicit acceptance plan before saving. |
| MVP-REQ-04 | Prevent duplicate reimports while allowing legitimate identical-looking movements and human resolution of uncertain candidates. |
| MVP-REQ-05 | Propose, review, confirm, reject, and reverse one-to-one internal transfer reconciliation between tracked EUR accounts. |
| MVP-REQ-06 | Create, edit, and assign categories; categorize consumption separately from transaction financial type. |
| MVP-REQ-07 | Manually attribute expenses using `self`, `partner`, `joint`, or `unassigned`, with adaptable, localizable display labels independent of the paying account. |
| MVP-REQ-08 | Distinguish income, expense, internal transfer, investment contribution, and unresolved movement. |
| MVP-REQ-09 | Search and filter movements by account, financial date, text, category, attribution, type, and review state. |
| MVP-REQ-10 | Display income, expenses, period savings, balances, and evolution with scope, coverage, and uncertainty visible. |
| MVP-REQ-11 | Persist locally; export, validate, and restore complete versioned backups; support tested schema migrations. |
| MVP-REQ-12 | Offer an isolated, resettable demonstration mode generated entirely from synthetic data. |

Basic manual corrections and classification are included. A comprehensive manual bookkeeping editor is not a prerequisite for the import-led MVP. Corrections must preserve provenance and must not defeat reimport identity.

## Import support contract

### Profiles rather than universal bank support

The three institution adapters and four format families are committed MVP deliverables. Exact institution/product/format combinations must be enumerated before the relevant adapter is accepted; the roadmap must not infer a twelve-combination promise.

| Institution | Committed target | Current profile evidence |
| --- | --- | --- |
| Ibercaja | Banking movements in EUR through explicitly specified profiles. | Exact columns, variants, and format combinations are not yet verified. |
| Openbank | Banking movements in EUR through explicitly specified profiles. | Exact columns, variants, and format combinations are not yet verified; fund valuation excluded. |
| imagin | Banking movements in EUR through explicitly specified profiles. | Exact columns, variants, and format combinations are not yet verified; card/Bizum variants require explicit scope. |

For each accepted profile, record: stable profile/version identifier, institution and product, detected content type, sheet/table selection, required columns, date and number conventions, sign rules, currency assumptions, source transaction identifiers if available, limits, known unsupported variants, evidence source, and fixture references. Every committed format family needs at least one end-to-end profile test.

Profile states must distinguish **specified**, **synthetically verified**, and **owner-locally verified with real statements**. Issue #1 does not claim existing production compatibility. If no lawful public specification or permissioned structural description is available, the profile remains an explicit evidence gap. Codex must not inspect real statements to close it.

The MVP cannot be declared fully validated for Ibercaja, Openbank, and imagin until the project owner has successfully checked real statements locally on their own computer for every committed institution/product/format profile. The owner performs these checks; Codex has no access to the files. Real statements, anonymized statements, and statement-derived files or fixtures must not be uploaded to GitHub or supplied to Codex.

Record only non-sensitive technical evidence: profile identifier, profile/adapter version, application version, movement count, sanitized incident descriptions, and check outcomes. Check normalization, row counts, reimport behavior, and applicable reconciliation and indicator rules against the source locally; report pass/fail outcomes without recording transaction contents, balances, amounts, dates, account identifiers, filenames, screenshots, or raw logs. The minimal validation record is the only permitted statement-related reporting artifact; it is not a dataset or fixture.

Automated synthetic tests remain a separate mandatory requirement. Owner-local verification does not replace them, and synthetic verification alone does not satisfy the real-format validation gate. Failed or missing owner checks block full validation of the affected profiles and the M3 release gate.

### Import workflow and failure behavior

1. Check size and detect content; extension alone is insufficient.
2. Select a matching adapter/profile or return an unsupported/ambiguous-format diagnostic.
3. Parse into staging data and normalize dates, signed amounts, descriptions, and provenance.
4. Present invalid rows, duplicate candidates, excluded rows, unresolved classifications, and proposed additions.
5. Require the user to resolve blocking decisions or explicitly exclude invalid rows; do not silently discard them.
6. Commit the accepted plan and its batch/provenance metadata atomically. A failure commits none of that plan.

Do not store original statement binaries by default. Persist only the provenance needed for explanation and reimport identity, avoiding unnecessary personal metadata. Retention details require an ADR.

## Financial integrity rules

### Money, dates, and balances

- Represent EUR amounts in integer minor units with explicit currency and validated safe bounds. Reject unsupported precision or overflow instead of silently rounding. A future currency's precision must not be assumed to be two decimals.
- Parse monetary input according to the selected profile. Do not calculate money using binary floating-point fractions.
- Preserve booking date and value date when available; use booking date for MVP period metrics. Ambiguous dates require profile rules or review, never guessing from the runtime locale.
- Treat financial dates as calendar dates; a browser timezone change must not move a transaction into another month.
- Define the opening balance as the balance immediately before movements on its reference date. Count subsequent included movements once; do not reuse a statement closing balance as an opening balance without an explicit reference rule.
- A calculated balance is opening balance plus applicable signed movements. Show its reference date and known coverage. Missing opening data or gaps mean an incomplete balance, not a current bank balance. An imported zero must remain distinguishable from an unknown balance.

### Classification and indicators

- Unknown type defaults to unresolved, never automatically to expense. Import heuristics can suggest types but must expose uncertainty.
- Internal transfers are neither income nor expenses. A confirmed matched pair retains two source movements linked by a reconciliation record.
- An explicitly identified transfer to or from an owned account that is not imported remains an unmatched own-account transfer, excluded from income and expenses but visible in account cash movements and coverage warnings. Without that explicit decision it remains unresolved.
- Investment contributions are tracked as a separate cash movement type, excluded from consumption and income. They affect the source account balance but do not create an investment position or valuation.
- Period savings equals confirmed income minus confirmed expenses within the selected scope and period. It is not cash-balance variation, investment return, or wealth change. Contributions and transfers do not reduce this metric.
- Show confirmed totals and unresolved counts/amounts separately. Provisional views must identify their assumptions and must not silently replace confirmed totals. Unclassified movements still affect a calculable account balance because classification and cash movement are separate concerns.
- Date filters apply to both sides of a transfer independently for account balances; paired status must not cause a movement outside the period to be pulled into that period.
- Refunds, reversals, fees, and opening-balance corrections need explicit rules and synthetic cases before M3. Do not ship unhandled cases as ordinary income or expenses by assumption.

### Duplicates and reconciliation

- Use source identifiers where their scope and stability are established. A fingerprint of date, amount, and description is candidate evidence, not universally unique identity.
- Detect repetition within a file, exact reimports, and overlapping exports per account. Renaming a file must not defeat duplicate detection. Preserve the ability to accept genuinely separate identical-looking rows.
- Preserve import provenance separately from editable classification fields. Record duplicate decisions so repeated imports remain consistent.
- Generate transfer candidates from compatible ownership, opposite signed amounts, currency, date proximity, and available references. The date window and confidence policy require an ADR and test evidence.
- The MVP requires user confirmation before creating a reconciliation link. Multiple plausible matches must be presented as ambiguous. One movement cannot belong to multiple active links.
- Confirming or reversing a link is atomic and auditable. Reversal restores the preceding classification state; it does not delete source movements. Conflicting later edits require review rather than silent overwrite.
- Commission-bearing, split, or multi-leg transfers must remain explicit exceptions unless supported by a documented rule; the MVP does not guess a one-to-one match.

### Categories and attribution

Categories do not determine whether a movement is a transfer. Removing an in-use category requires reassignment or an explicit uncategorized state and must not remove transactions.

Expense attribution uses the stable internal identifiers `self`, `partner`, `joint`, and `unassigned`. Display labels can be adapted or localized without changing these identifiers; `unassigned` means attribution has not been decided, independently of financial review state. The paying bank account does not necessarily determine whom an expense belongs to, so attribution remains a manual decision rather than an inference from account ownership.

Attribution is independent of category and financial transaction type; `joint` does not split an amount or imply a 50/50 allocation. Non-expense rows must not inflate attributed expense totals.

## Persistence, recovery, and privacy

- Application use cases depend on storage contracts; the financial domain does not import IndexedDB, React, parsers, or AI SDKs.
- Local data must not be transmitted by analytics, remote logging, parsers, or background requests. Static asset delivery is distinct from uploading financial records.
- IndexedDB is not a durability guarantee. Explain browser/device loss risks and provide visible backup and restore actions. Do not claim encryption at rest by default.
- A complete backup includes schema/export version, institutions, accounts, balance references, movements, categories, attribution, import identity/provenance, duplicate decisions, reconciliation history, and relevant settings.
- Validate format, version, references, money constraints, identifiers, and supported limits before restoring. Provide a preview and explicit replacement confirmation; workspace merging is outside the MVP.
- Failed restore or migration must leave the previous valid workspace intact or provide a verified recovery path. No silent destructive schema reset.
- Backup verification means integrity and consistency checks plus tested round trips; it is not a claim of cryptographic authenticity. Exported files contain sensitive data and must carry an appropriate warning.
- Demo data must use isolated storage or an equivalent boundary. Resetting the demo must never clear a personal workspace.

## Security and quality requirements

Treat every import and backup as untrusted. Define file, row, cell, archive-expansion, and processing limits before release. Test extension/content mismatch, malformed spreadsheets, unsupported encodings, and hostile HTML. Do not execute macros, formulas, scripts, or fetch external resources. Imported text must render as inert text. Parser selection and dependency maintenance require review.

Local-first does not protect against injected application code or a compromised device. Review script loading, content security policy, dependency exposure, and production configuration before M3. Do not put credentials in client code or record financial payloads in logs and traces.

Target WCAG 2.2 AA for core journeys, verified with automated checks and manual keyboard, focus, label, error, contrast, and chart-alternative review. This target is not a certification claim. Review small and large viewport layouts, loading, empty, error, and unresolved states. Isolate UI messages and locale formatting for future internationalization.

CI starts early with type checking, linting, meaningful financial/parser tests, and production build verification. Introduce end-to-end checks with the first usable vertical slice; do not postpone them or security work to M6. Record representative import performance with declared fixture size and test environment; set limits from evidence rather than invented capacity claims.

## Acceptance criteria

| ID | Verifiable outcome | Evidence |
| --- | --- | --- |
| MVP-AC-01 | Multiple EUR accounts retain institution, balance reference, and history after reload. Non-EUR input is diagnosed without entering EUR totals. | Account and persistence integration tests. |
| MVP-AC-02 | Every committed profile yields independently expected normalized rows; all three institution adapters and all four format families are covered. Unknown variants are rejected clearly. Full validation also requires successful owner-local checks with real statements for every committed profile, without Codex accessing the files. | Published profile matrix, independent automated synthetic results, and non-sensitive owner-local verification records containing profile/version, movement count, incidents, and check outcomes. No real, anonymized, or statement-derived files are uploaded to GitHub. |
| MVP-AC-03 | Invalid dates, malformed amounts, missing required columns, and ambiguous profiles appear before confirmation. Explicit row exclusions are visible; interrupted confirmation persists none of the accepted plan. | Preview and transaction-failure integration tests. |
| MVP-AC-04 | Exact reimport, renamed-file reimport, overlap, and repeated rows create no unintended duplicates; two legitimate identical-looking movements can coexist after review. | Independently specified duplicate scenarios, including decision persistence after reload. |
| MVP-AC-05 | EUR 0.10 plus EUR 0.20 equals EUR 0.30 exactly. Invalid precision and unsafe bounds are rejected. Financial dates remain stable across timezone settings. | Domain tests with boundary cases. |
| MVP-AC-06 | Account A opening at EUR 1,000 and B at EUR 200, followed by a EUR 100 A-to-B transfer, show EUR 900 and EUR 300. Confirming or reversing reconciliation keeps the EUR 1,200 aggregate unchanged and preserves source rows. | Domain and persistence tests. |
| MVP-AC-07 | Two plausible counterparts remain pending; confirmation links one pair only. Reversal restores prior classifications, records the decision, and cannot silently overwrite conflicting edits. | Reconciliation tests and UI walkthrough. |
| MVP-AC-08 | Unknown movement type remains unresolved. Confirming an unmatched own-account transfer excludes it from income/expense while preserving its balance effect and an unmatched warning. | Classification and metric tests. |
| MVP-AC-09 | With EUR 2,000 confirmed income, EUR 500 expense, EUR 300 investment contribution, and EUR 100 unresolved outflow, confirmed savings is EUR 1,500; contribution and unresolved outflow are separately visible. No investment valuation or wealth claim appears. | Metric tests and dashboard review. |
| MVP-AC-10 | Missing opening balance or known coverage gaps prevent an unqualified current-balance label. A known zero is not displayed as unknown. Booking dates drive month boundaries. | Coverage and period-boundary tests. |
| MVP-AC-11 | Editing categories and expense attribution updates filtered views; deleting an in-use category preserves all movements. Attribution uses `self`, `partner`, `joint`, and `unassigned` with localizable labels; changing the paying account, category, or financial type does not determine attribution. `joint` never generates settlement or splits. | Use-case and UI tests, including expenses from the same account with different attribution values and independent label localization. |
| MVP-AC-12 | Search and combined filters produce the expected rows and dashboard scope, with pending items visible and excluded totals explained. | Synthetic end-to-end import-to-dashboard scenario. |
| MVP-AC-13 | Export and restore reproduce all workspace relationships and financial results. Invalid, unsupported, or interrupted restores do not overwrite valid data. Migration tests preserve financial invariants. | Backup round trips, failure injection, and migration tests. |
| MVP-AC-14 | Reload retains data; financial workflows need no backend once application assets are loaded. Demo reset cannot affect a separate personal workspace. Full offline app installation is not implied. | Persistence, network-observation, and demo-isolation checks. |
| MVP-AC-15 | Malformed and oversized inputs fail within declared limits. HTML/scripts/formulas are not executed and external resources are not fetched. Financial payloads do not appear in logs or network requests. | Adversarial fixture tests and security review. |
| MVP-AC-16 | Core flows pass responsive and accessibility review; CI passes types, lint, tests, and build, including a meaningful end-to-end journey. All public fixtures are generated from scratch. | Review checklist, CI results, and fixture provenance. |

## Synthetic validation strategy

Generate fixtures from invented identities, institutions' profile rules where evidenced, dates, amounts, and descriptions. Do not derive them by anonymizing real financial records. Do not access real statements during implementation under the current authorization.

Maintain small readable expected-output descriptions independent of adapter code. Cover decimal conventions, signs, encodings, date formats, workbook sheets, HTML tables, missing columns, overlapping periods, same-looking legitimate purchases, ambiguous transfer matches, delays, fees, reversals, and incomplete coverage. Pair parser tests with end-to-end tests of generated binary formats.

Check invariants in addition to examples: transfer reconciliation does not change cash balances, reimport is idempotent, failed commits create no partial batch, restored workspaces retain results, and category/attribution changes do not alter money amounts. Synthetic fixtures validate behavior against specified profiles; they do not prove undocumented live bank compatibility.

## Explicit exclusions and open decisions

Excluded: backend/authentication, cloud sync, direct bank APIs, payment execution, exchange conversion, Revolut-specific imports, investment positions/valuation, advanced budgets/goals, shared permissions or settlement, debt/wealth modules, native mobile, and the product copilot. A configurable column-mapping assistant is a later iteration.

Open decisions to resolve through later ADRs include exact profiles and evidence, parser libraries and limits, date/precision rules beyond EUR, refund/fee treatment, identity and provenance retention, transfer candidate policy, storage/migration implementation, backup format, and security controls. These decisions cannot weaken the acceptance contract without explicit scope review.
