---
name: filepro-csa-assessment
description: Executes the 4-pass, 7-domain Current-State Assessment (CSA) and separately identifiable Current-State Architecture for FilePro healthcare/optical ecosystems with strict evidence separation (Observed Fact, Inference, Unknown, Recommendation), Lucid Chart diagram & client project-doc reconciliation, standardized inventory/risk contracts, and evidence-grounded Mermaid diagrams.
version: 1.2.0
applies_to: [WF-F1, WF-F2, WF-F3]
---

# Skill: `filepro-csa-assessment` (v1.2 — 7-Domain, 4-Pass FilePro CSA & Current-State Architecture)

Use this skill in **CSA Architect**, **Spec Orchestrator**, and **Spec Document Assembler** to produce TWO clearly distinct Markdown deliverables inside `wf-f0-f1-v3-all-modules-spec-swarm.json`:
1. `specs/csa/CURRENT_STATE_ASSESSMENT.md` (7-domain, 4-pass Current-State Assessment)
2. `specs/csa/CURRENT_STATE_ARCHITECTURE.md` (separately identifiable Current-State Architecture with >=5 valid zero-HTML Mermaid diagrams)

## 1. Evidence & Separation Guardrails (Non-Negotiable)

1. **Analyze Only Available Evidence:** Never invent file names, tables, fields, record counts, schedules, endpoints, EDI standards, runtime behavior, retry logic, security controls, or test outcomes.
2. **Unknown Marker:** Whenever behavior cannot be verified from supplied artifacts, write `UNKNOWN - Verify in runtime/config` (and name the exact verification check required).
3. **Explicit Epistemic Tagging:** Classify every material finding/claim as:
   - **`Observed Fact`** (explicitly distinguishing **`Code-Verified`**, **`Lucid Chart Diagram-Depicted`**, and **`Client-Doc-Reported`** from `docs/project/`)
   - **`Inference`** (reasoned interpretation with explicit limitation)
   - **`Unknown`** (not established by available evidence)
   - **`Recommendation`** (proposed improvement, strictly separated from current-state description)
4. **Architecture Diagram & Client Project Documentation Reconciliation (`Lucid Chart Diagrams/` & `docs/project/`):**
   - Treat `Lucid Chart Diagrams/Order Life Cycle v20250804.png` as the primary end-to-end architecture reference when present, reconciled against companion Lucid Chart diagrams and in-drop filePro code (`evidence-pack/`).
   - Reconcile client-supplied operational & estate documentation under `docs/project/` (when present or via baseline inventory):
     - **Signed PSA (`FAIT_PS PSA`):** Confirms **MariaDB** RDBMS, 6 in-scope artifact types (Bash, InputI/PRC, PHP, MariaDB Schema, Cron, Screens), Deliverable #4 flag catalog, and **Zero Personal Data (PII/PHI)** rule.
     - **`fP Table Documentation/` (`wva_data_table_list.xlsx` + `data_tables/*.xlsx`):** ~5-year-old FAIT estate inventory (**519 tables**: 98 P3, 46 P2, 112 P1, 211 P0/archive, 52 `?`/TBD) and field `Valid Data Details`. Always flag drift vs live code (e.g., `Invoice.60` `Facility_ID` vs `Unused`; `Elavon` migration note vs `1stPayGateway` diagram).
     - **`fP ITHD documentation/`:** Operational SOPs confirming random filePro invoice data corruption recovered via `Invoice dly` (`DlySave`), `/etc/no_user_login` overnight lockout, `Push_2_SQL` ~5-min cadence, `lpr -r -pA07 STMNTS`, and same-day-only `/appl/print/PrintEmFiles/ArchivedPrints/`.
     - **`CSR_Documentation/`:** Live 2025–2026 operational rules (Anzio emulator, 6-month password expiry, `Panther=Costco/15000`, `Hulk=30000`, `Shopko=7500`, `HiWaldo=55000`, `Demo=99999` real-stock deduction warning, ECP E360+ Nov 2026 mandate, Alcon Marlo 30-min edit window & `Pcode 0092`, YourStore/EyeSubscribe/Text2Order auth-hold/capture rules, FDA/manufacturer return rules).
     - **`Filepro Learning Sessions/`:** Dave Snyder developer training recordings covering filePro map/zero-size placeholders, screens, selection sets, input/automatic/output processing, reports, and indexes.
   - Always isolate draft/stale diagrams (`New MakeOrders -_ LensOrder flow.png`, Shopko/Fielmann note in `Invoice Posting.png`) and report conflicting client docs explicitly without guessing.
5. **Evidence Citation Format:** `Source path | Artifact identifier | Line range or stable locator | Supporting detail`
6. **Zero Personal Data (Signed PSA §7):** Never copy real customer names, phone numbers, email addresses, DOBs, or account PII from live data or CSR docs into generated deliverables.

## 2. Four Modular Assessment Passes

| Pass | Focus | Inputs | Required Outputs |
|---|---|---|---|
| **Pass 1** | Schema & Lineage | `dictionary.json`, `data-profile.json`, `indexes.json`, `dependencies.json`, `ValidProduct*.png`, `RELATI~1.PNG`, `wva_data_table_list.xlsx`, `data_tables/*.xlsx` | Entity inventory (6 in-drop + 519-table estate context), As-Is ERD, lookup graph, data-quality & PHI/PII flags (`WTP_Charges.24 DOB`, `PatientData`) |
| **Pass 2** | Workflow & Business Logic | `processes/*.json`, `*.annotated.txt`, `screens.md`, `screen-guardrail-report.json`, `rule_catalog.json`, `findings.json`, `Library-Pricing.png`, `inputI.png`, `CSR_Documentation/` (ECP E360+, Marlo, Returns, Training Manual 2026) | Event-driven processing model, business rule summary, screen inventory, coupling & provisional dead-code findings, operational SOP alignment |
| **Pass 3** | Integrations & Operations | `polyglot-edges.json`, `dependencies.json`, `Order Life Cycle v20250804.png` & companions, `fP ITHD documentation/`, `FAIT_PS PSA`, `fP Table Documentation/` | EDI / MariaDB (`wva SQL`/`wva2 SQL`/`hulk_ecom SQL`) / File System / API interface matrix, locking/overnight lockout (`no_user_login`), `Invoice dly` corruption recovery, security & payment-gateway conflicts |
| **Pass 4** | Cross-Cutting Synthesis | Outputs from Passes 1–3 | Executive summary, separate Current-State Architecture with 5 Mermaid diagrams, 28-diagram + client-doc reconciliation matrices, severity-rated Risk Register, phased recommendations |

## 3. Seven Mandatory CSA Domains

1. **5.1 Executive Summary and System Context** (reconciling `Order Life Cycle v20250804.png`, signed PSA §4a, and 519-table FAIT estate scope).
2. **5.2 Data Architecture and Domain Model** (filePro maps, `wva SQL`/`wva2 SQL`/`hulk_ecom SQL` MariaDB seams, `wva2.order_detail.id` / `webprod.payment_card_tokens`, keys, indexes, `Invoice dly` corruption risk, PHI/PII including `WTP_Charges.24` DOB & `PatientData`).
3. **5.3 Application and Business Logic** (input/automatic/output processing, `@`-triggers, rules, shared libraries, partner-specific rules for Panther/Costco, Hulk, Shopko, ECP E360+, Alcon Marlo, Demo `99999` stock risk, **provisional** dead code).
4. **5.4 Integration and Interface Landscape** (strictly separated into **EDI**, **Databases (MariaDB + PHP middleman)**, **File System**, and **External APIs** — flagging `1stPayGateway` vs `Elavon` conflict and absent external repos).
5. **5.5 Runtime, Infrastructure, and Operational Resilience** (Linux/filePro runtime, Anzio terminal, locking `-p`/`-w`/`-nx`, `@pz` vs `-rz`, `/etc/no_user_login` overnight lockout, `Invoice dly` corruption recovery, `QPrint`/`fprint`/`CUPS`, `lpr -r` statement deletion, `AvaTax_Api_Backup`).
6. **5.6 Security, Compliance, and Governance** (`Library/Password`, 6-month password expiry synced with email, LastPass, `Api_Keys` table, `chmod 777`, hardcoded users/accounts, `SndEml` HIPAA warning, `WTP_Charges.24` DOB vs HIPAA policy, `home.wisvis.com` HIPAA breach form, PCI/SSH payment boundary — strictly distinguishing **observed control gaps** vs **not evidenced**).
7. **5.7 Modernization Readiness, Technical Debt, and Risks** (severity-rated Risk Register, coupling hotspots, strangler candidates, quick wins, sequencing — clearly tagged `Recommendation`).

## 4. Standard Output Contract (Required Inside Every Domain)

1. **Inventory Table:** `Component/Interface ID | Name | Domain | Type | Direction and Flow | Schedule/Trigger | Dependencies | Error Handling | Evidence Reference | Evidence Status`
2. **Key Findings and Mechanics** (inputs/outputs, validation/transformation, triggers, dependencies, failure handling, unverified behavior).
3. **Risks and Technical Debt** (`Risk ID`, `Severity`, `Category`, `Affected Components`, `Observed Condition`, `Potential Impact`, `Root Cause: Confirmed/Suspected/Unknown`, `Evidence References`, `Recommended Next Step`, `Verification Required`).
4. **Open Questions and Verification** (what is unknown, why it matters, missing evidence, specific check required, affected deliverables).

## 5. Required Evidence-Backed Mermaid Diagrams (Pure Markdown, Zero HTML Tags, High-Contrast Palette)

1. End-to-End System Context Diagram (Upstream channels from `Order Life Cycle v20250804.png` -> FilePro -> Downstream fulfillment, lab, billing, print, tax, shipping, payments)
2. Logical & Application Architecture Diagram
3. As-Is Entity-Relationship & Lookup Diagram
4. Integration & Data-Flow Diagram (EDI / MariaDB Databases / File System / APIs)
5. Key Processing / Execution Sequence Diagram (`@entsel` / `-rz` -> `prc.inputI` -> `Library/prc.Pricing` -> `SQLID` / `2BPrtd` / `PROSHIP` / `AvaTax`)
