---
name: filepro-csa-assessment
description: Executes the 4-pass, 7-domain Current-State Assessment (CSA) and separately identifiable Current-State Architecture for FilePro healthcare/optical ecosystems with strict evidence separation (Observed Fact, Inference, Unknown, Recommendation), Lucid Chart diagram reconciliation, standardized inventory/risk contracts, and evidence-grounded Mermaid diagrams.
version: 1.1.0
applies_to: [WF-F1, WF-F2, WF-F3]
---

# Skill: `filepro-csa-assessment` (v1.1 — 7-Domain, 4-Pass FilePro CSA & Current-State Architecture)

Use this skill in **CSA Architect**, **Spec Orchestrator**, and **Spec Document Assembler** to produce TWO clearly distinct Markdown deliverables inside `wf-f0-f1-v3-all-modules-spec-swarm.json`:
1. `specs/csa/CURRENT_STATE_ASSESSMENT.md` (7-domain, 4-pass Current-State Assessment)
2. `specs/csa/CURRENT_STATE_ARCHITECTURE.md` (separately identifiable Current-State Architecture with >=5 valid zero-HTML Mermaid diagrams)

## 1. Evidence & Separation Guardrails (Non-Negotiable)

1. **Analyze Only Available Evidence:** Never invent file names, tables, fields, record counts, schedules, endpoints, EDI standards, runtime behavior, retry logic, security controls, or test outcomes.
2. **Unknown Marker:** Whenever behavior cannot be verified from supplied artifacts, write `UNKNOWN - Verify in runtime/config` (and name the exact verification check required).
3. **Explicit Epistemic Tagging:** Classify every material finding/claim as:
   - **`Observed Fact`** (directly supported by source/evidence-pack or inspected diagram artifact, distinguishing code-verified vs diagram-depicted)
   - **`Inference`** (reasoned interpretation with explicit limitation)
   - **`Unknown`** (not established by available evidence)
   - **`Recommendation`** (proposed improvement, strictly separated from current-state description)
4. **Architecture Diagram Reconciliation (`Lucid Chart Diagrams/`):**
   - Treat `Lucid Chart Diagrams/Order Life Cycle v20250804.png` as the primary end-to-end architecture reference when present.
   - Reconcile companion Lucid Chart diagrams against `Order Life Cycle v20250804.png` and in-drop filePro code (`evidence-pack/`).
   - Always distinguish **Diagram-Depicted** systems/flows (e.g., `EHR`, `3PO Vendor`, `Hulk OMS/BOSS/ECOM`, `Panther ECOM`, `Commerce Hub/Rithum`, `VerifyRX/VerifyRX2`, `Yourstore`, `Faitships`, `wva SQL`/`wva2 SQL`, `Innovation LMS`, `PROSHIP`/`PROSHIPLOG`, `AvaTax_Api (C#)`, `CUPS Print Server`, `Stripe`/`WTP_Charges`, `1stPayGateway`) from **Code-Verified (`Observed Fact`)** hooks in `Invoice/prc.inputI` & `Library/prc.Pricing`, and flag draft/stale/design-note diagrams explicitly (e.g., `New MakeOrders -_ LensOrder flow.png`, Shopko/Fielmann note in `Invoice Posting.png`).
5. **Evidence Citation Format:** `Source path | Artifact identifier | Line range or stable locator | Supporting detail`
6. **Conflicting Evidence:** Document both sources, state what can and cannot be concluded, and name the verification needed. Never silently choose one side.

## 2. Four Modular Assessment Passes

| Pass | Focus | Inputs | Required Outputs |
|---|---|---|---|
| **Pass 1** | Schema & Lineage | `dictionary.json`, `data-profile.json`, `indexes.json`, `dependencies.json`, `ValidProduct*.png`, `RELATI~1.PNG` | Entity inventory, As-Is ERD, lookup graph, data-quality & PHI/PII flags (volumes only from verified profiles) |
| **Pass 2** | Workflow & Business Logic | `processes/*.json`, `*.annotated.txt`, `screens.md`, `screen-guardrail-report.json`, `rule_catalog.json`, `findings.json`, `Library-Pricing.png`, `inputI.png`, `Invoice Posting/Printing.png` | Event-driven processing model, business rule summary, screen inventory, coupling & provisional dead-code findings |
| **Pass 3** | Integrations & Operations | `polyglot-edges.json`, `dependencies.json`, `Lucid Chart Diagrams/Order Life Cycle v20250804.png` & aligned companion diagrams, `/docs` references, `system`/`call`/`form` inventories | EDI / Database / File System / API interface matrix, upstream/downstream boundaries, operational & security gaps |
| **Pass 4** | Cross-Cutting Synthesis | Outputs from Passes 1–3 | Executive summary, separate Current-State Architecture with 5 Mermaid diagrams, Diagram-to-Inventory consistency check, severity-rated Risk Register, phased recommendations |

## 3. Seven Mandatory CSA Domains

1. **5.1 Executive Summary and System Context** (reconciling `Order Life Cycle v20250804.png`'s 3 order-placed entry actors & full upstream/downstream topology against in-drop code).
2. **5.2 Data Architecture and Domain Model** (filePro maps, `wva SQL`/`wva2 SQL`/`hulk_ecom SQL` external DB seams, keys, lookup paths, indexes, referential integrity, data quality, PHI/PII sensitivity, verified volumes only).
3. **5.3 Application and Business Logic** (input/automatic/output processing, `@`-triggers, rules, workflows, shared libraries, side effects, coupling, **provisional** dead code).
4. **5.4 Integration and Interface Landscape** (strictly separated into **EDI**, **Databases**, **File System**, and **External APIs** — distinguishing diagram-depicted vs code-verified vs `UNKNOWN - Verify in runtime/config`).
5. **5.5 Runtime, Infrastructure, and Operational Resilience** (UNIX/Linux `/appl/fp/`, `rclerk`/`rreport`, locking `-p`/`-w`/`-nx`, `@pz` vs `-rz`, `/appl/bin/QPrint`/`fprint`/`CUPS`, `AvaTax_Api_Backup`, cron/DR/recovery).
6. **5.6 Security, Compliance, and Governance** (access controls, `Library/Password` / `Flag(60)`, `chmod 777`, hardcoded users/accounts, `SndEml` HIPAA warning, PCI/payment token boundaries, distinguishing **observed control gaps** vs **not evidenced**).
7. **5.7 Modernization Readiness, Technical Debt, and Risks** (severity-rated Risk Register, coupling hotspots, strangler candidates, quick wins, sequencing — clearly tagged `Recommendation`).

## 4. Standard Output Contract (Required Inside Every Domain)

1. **Inventory Table:** `Component/Interface ID | Name | Domain | Type | Direction and Flow | Schedule/Trigger | Dependencies | Error Handling | Evidence Reference | Evidence Status`
2. **Key Findings and Mechanics** (inputs/outputs, validation/transformation, triggers, dependencies, failure handling, unverified behavior).
3. **Risks and Technical Debt** (`Risk ID`, `Severity`, `Category`, `Affected Components`, `Observed Condition`, `Potential Impact`, `Root Cause: Confirmed/Suspected/Unknown`, `Evidence References`, `Recommended Next Step`, `Verification Required`).
4. **Open Questions and Verification** (what is unknown, why it matters, missing evidence, specific check required, affected deliverables).

## 5. Required Evidence-Backed Mermaid Diagrams (Pure Markdown, Zero HTML Tags)

1. End-to-End System Context Diagram (Upstream channels from `Order Life Cycle v20250804.png` -> FilePro -> Downstream fulfillment, lab, billing, print, tax, shipping, payments)
2. Logical & Application Architecture Diagram
3. As-Is Entity-Relationship & Lookup Diagram
4. Integration & Data-Flow Diagram (EDI / SQL Databases / File System / APIs)
5. Key Processing / Execution Sequence Diagram (`@entsel` / `-rz` -> `prc.inputI` -> `Library/prc.Pricing` -> `SQLID` / `2BPrtd` / `PROSHIP` / `AvaTax`)
