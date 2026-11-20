---
name: filepro-spec-authoring
description: Authors client-review-ready Markdown (.md) Functional Specifications (with BRD integrated at the beginning and consolidated Business Rules), Technical Specifications (with merged Polyglot Boundaries), concise Overviews, Data Dictionaries, Flag Catalogs, and Evaluation placeholders aligned to current-state FilePro evidence.
version: 3.1.0
applies_to: [WF-F1, WF-F2, WF-F4, WF-F5]
---

# Skill: `filepro-spec-authoring` (v3.1 — Revalidated BRD + Consolidated Markdown Spec + High-Contrast Mermaid Contract)

Use this skill in **Spec Orchestrator**, **Spec Document Assembler**, **CSA Architect**, and **Spec Repair**.

## 1. Revalidated BRD Contract (Integrated at Beginning of `functional_spec.md`)

The Business Requirements Document (BRD) is **not** a standalone duplicate file in the final specification set; it is authored as **Part I (beginning)** of `specs/<module>/functional_spec.md` with strict separation between business requirements and technical implementation:

1. **Intended Inputs:** Verified `evidence-pack/` metadata, `specs/<module>/rule_catalog.json`, `specs/<module>/findings.json`, and validated `/docs` context (never raw `key*` PHI).
2. **Evidence-Grounded & Provenance-Tagged Requirements:** Express capabilities, business outcomes, stakeholder needs, and plain-English functional requirements without leaking low-level filePro runtime syntax or target-state technology choices. Explicitly label all requirements reconstructed from source code as **`Implementation-Derived Requirement (Unapproved — Pending Stakeholder Review)`** — never present code-derived requirements as stakeholder-approved business requirements. Preserve any stakeholder-supplied requirement identifiers and approval status separately.
3. **Standardized Domain Terminology (Mandatory):**
   - Use **`Product Management`** (never `Product Master & Lifecycle Management`).
   - Use **`Exception Overrides`** (never `Customer/Vendor/Product Exception Overrides`).
   - Use **`Screen Summary`** (never `Context Table Screen Summary`).
   - Do **not** assert **`Context Tables`** as an implementation construct (use `Primary Module Tables` and `Referenced Drop Tables (In-Drop)` / `Missing Referenced Tables`).
4. **No Open Business & SME Questions in the BRD Portion:** Strip `Open Business & SME Questions` out of the BRD opening section; consolidate all unresolved questions into ONE clearly separated section at the **end** of `functional_spec.md`.
5. **Evidence Classification & Unknowns:** Tag material claims as `Observed Fact`, `Inference`, `Unknown`, or `Recommendation`. Use `UNKNOWN - Verify in runtime/config` whenever evidence is insufficient; never invent behavior or emit literal `undefined` / `null` / empty placeholders.

## 2. Consolidated Markdown-Only Deliverable Suite (`specs/<module>/`)

Generate **Markdown (`.md`) files only** at this stage (do **not** generate `.html` files). Embed valid **Mermaid** diagrams (`no HTML tags`, `no double ]]`) directly inside Markdown.

### 2.1 `functional_spec.md` (Consolidated BRD + Overview Sections + Functional Behavior + Business Rules)
1. **Part I — Business Requirements Document (BRD):** Executive Summary, Business Capabilities (using `Product Management`, `Exception Overrides`), Business Objectives & Scope Boundaries *(no Open Questions here)*.
2. **Part II — Program Overview & Module Inventory (relocated from Overview):**
   - `<Module> Module — Program Overview`
   - `Scope & Boundaries`
   - `Module Inventory`
   - `Related Specifications`
3. **Part III — Functional Behavior & User Interactions:**
   - Personas & Roles (module-specific; never copy Pricing personas into Invoicing)
   - What Information Is Tracked (grounded in `data_dictionary.md`)
   - How Users Interact With It (grounded in content-aware screen identification; all detected screens)
   - Edge Cases & Failure Modes
   - System Connections & Functional Integration Contracts
   - How `<Module>` Works (Decision Process, embedded Mermaid flowchart, synthetic non-PHI worked examples)
   - Reports & Outputs (`out.*`, print queues, electronic outputs)
4. **Part IV — Consolidated Business Rules:**
   - Complete categorized business rules merged from `rule_catalog.json` (discard standalone `RULE_QUICK_REFERENCE.md` after preserving all unique rule content here).
   - Every rule MUST have a non-empty plain-English description (zero literal `undefined`, `null`, or bare `-`) and an exact evidence citation (`Source path | Artifact identifier | Line range or stable locator | Supporting detail`).
5. **Part V — Evaluation / Validation:** In templates, keep a clearly labeled placeholder. In generated specifications, populate with actual deterministic validation status, scope, known limitations, semantic-review status, and a link to `../evaluation_report.md` (never imply human/SME approval).
6. **Part VI — Unresolved Business & SME Questions:** Single consolidated section at the end of `functional_spec.md` (including pending Glossary decision with Manish for Data Dictionary & Flag Catalog).

### 2.2 `technical_spec.md` (Technical Implementation + Merged Polyglot Boundaries)
1. Executive Summary & Complexity Metrics (verified counts only; never `Screens: 0` when screens exist).
2. Scope Inventory & Excluded / Missing Artifacts (In-Drop tables/processes vs Missing tables/processes; **no `Context Tables`**).
3. Data Dictionary Summary & Key File Storage Profile (linked to retained `data_dictionary.md`).
4. Complete Screen Specifications & Content-Aware Screen Identification Guardrail (`Screen Summary` for non-primary tables; full `%N`/`*N`/`!N`/`!xx`/`!@xx` token tables).
5. Processing Table Logic, Subroutines, `dim` Arrays, Lookups, Calls, `system` & `form` Directives.
6. Index Definitions & Access Patterns (explicitly tagging binary headers as `UNKNOWN - Verify via dxmaint screenshot`).
7. **Polyglot Boundaries & Cross-File Integration Seams (Merged from `polyglot_boundaries.md`):** OS `system` calls, `SQLID`/`Push_2_SQL` MariaDB seam, PHP/AvaTax boundaries, missing Bash/Cron/PHP/SQL scripts (`PSA §4a`), and embedded Mermaid dependency graph.
8. Core Processing Workflows (embedded Mermaid flow diagram).
9. Operational Constraints, Behavioral Flags & Static Analysis Findings (linked to `flag_catalog.md`, `flag_catalog.json`, `findings.json`).
10. **Evaluation / Validation (Placeholder):** Clearly labeled placeholder linking to `../evaluation_report.md`.
11. Traceability Matrix & Evidence Index link (`../traceability_matrix.md`).

### 2.3 Retained vs Removed Artifacts
- **Retained in Current Locations (Pending Manish Confirmation on Glossary Relocation):** `data_dictionary.md`, `flag_catalog.md`, `flag_catalog.json`.
- **Retained Machine-Readable & Supporting Assets:** `rule_catalog.json`, `findings.json`, `findings.md`, `flows/<mod>_flow.mmd`, `flows/<mod>_dependency_graph.mmd`, `tests/<mod>.feature`, `test_seeds.json`, concise `overview.md` (navigation pointer only).
- **Removed Superseded Artifacts (Do NOT publish in final set):** `RULE_QUICK_REFERENCE.md`, `README_TEST_GENERATION.md`, `README_TESTS.md`, `TEST_GENERATION_SUMMARY.md`, `VALIDATION_REPORT.md`, and `.html` files.

### 2.4 High-Contrast Dark/Light Mermaid & Markdown Hygiene Contract
- **Mandatory Contrast Pairings:** Never emit a Mermaid `classDef` or `style` with `fill:` unless an explicit high-contrast `color:` is paired on the same declaration (`color:#0f172a` on light fills, `color:#f8fafc` on dark fills):
  - `classDef observed fill:#dbeafe,stroke:#1d4ed8,stroke-width:2px,color:#0f172a;`
  - `classDef decision fill:#fef3c7,stroke:#b45309,stroke-width:2px,color:#0f172a;`
  - `classDef boundary fill:#e2e8f0,stroke:#334155,stroke-width:2px,stroke-dasharray:4 3,color:#0f172a;`
  - `classDef missing fill:#fee2e2,stroke:#b91c1c,stroke-width:2px,stroke-dasharray:5 5,color:#7f1d1d;`
- **Theme-Proof `%%{init:...}%%` Header:** Every Mermaid diagram MUST include `%%{init: {'theme': 'base', 'themeVariables': {...}}}%%` with explicit `primaryTextColor: '#0f172a'`, `edgeLabelBackground: '#f1f5f9'`, `lineColor: '#3b82f6'`, and `clusterBkg: '#f8fafc'` so diagrams remain legible in both VS Code Dark Mode and Light Mode / PDF export. For `sequenceDiagram`, wrap message exchanges in `rect rgb(248, 250, 252) ... end` with `signalColor: '#1e293b'` and `signalTextColor: '#0f172a'`.
- **Zero Raw HTML in Markdown Tables & Mermaid:** Never use `<br>`, `<br/>`, `<b>`, or `<div>` inside Mermaid labels (use `\n`) or Markdown tables (use `; ` separators).
- **Rich Module Traceability:** Ensure `specs/<module>/traceability_matrix.md` indexes 100% of canonical `R-<CODE>-###` rules and links polyglot references to `technical_spec.md#71-polyglot-integration-boundaries-merged-into-technical-specification` (never the removed `polyglot_boundaries.md`).
