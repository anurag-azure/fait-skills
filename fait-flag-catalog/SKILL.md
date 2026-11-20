---
name: fait-flag-catalog
description: Dedicated skill for PSA Deliverable #4 — timeboxed contextual analysis of filePro flag and status-indicator fields across code, cross-checked against client fP Table Documentation Valid Data Details and separating true control flags from timer/numeric false positives.
version: 2.1.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `fait-flag-catalog`

Use this **separate specialist skill** exclusively to produce **PSA Schedule A Deliverable #4**: *Filepro data “flag” field usage descriptions (based on contextual analysis of field usage throughout the code base), on a commercially reasonable efforts basis in a timeboxed manner*.

## 1. Candidate Classification Scope (`evidence-pack/flags/flag-candidates.json`)
Inspect all candidates deterministically surfaced by `fp-normalize.js` and cross-check against client-provided `docs/project/fP Table Documentation/data_tables/*.xlsx` (`Valid Data Details` & `fp_Inventory_Product_Type.xlsx`) when present:
1. **Explicit `*flag*` dictionary fields** (e.g., `Invoice.#21 Print Flag`, `Invoice.#22 Posting Flag`, `Members.#33 Drop Ship Flag`, `Invoice.#331–355 B1) Backorder Flag`, plus 0-ref candidates `Invoice.#45/#53/#55`, `Members.#69/#111`, `Prices.#101`).
2. **Behavioral control flags & status switches:** `yesno` edits, `length === 1` code switches, `status`/`post`/`suppr`/`exempt`/`stp` indicators (`Invoice.#28 Ship to patient?`, `#29 Zero Shipping Charge Status`, `#31 (I)nvoice/(C)redit`, `#34 WEB_2_Patient`, `#35 Suppress Prices on Invoice`, `#391–415 B3) Don't Post`, `Members.#46 BG_Type`, `#52 YS_PaymentByCheck`, `#57 Account Active`), and `dim` field-overlay aliases (`BOed(25):331`, `post(25):391`).
3. **Process-level flags:** Local/global workflow control variables (`multflag`, `WTP_Update_FLAG`, `manualBO`, `useException`, `priceLikeCheck`, `NoExceptions`, `FreeGood`).
4. **False-Positive Timer/Counter Exclusions:** **NEVER classify non-boolean timers/handles as status flags without verifying their edit & usage!** Classic proven counterexample: **`dim Flag(60)(8,HMS,G)`** (`Invoice/prc.inputI:L0097, L1054, L2015–L2031`) stores `HMS` timestamps comparing `@tm gt Flag(PWFunction)+".167"` (~10-minute `Library/Password` authorization expiry cache).

## 2. Client Table Documentation Cross-Check (`docs/project/fP Table Documentation/`)
* Per `20261117_Drop_Readme.txt`, FAIT compiled `fP Table Documentation/data_tables/*.xlsx` (~5 years ago) containing `Valid Data Details` for flag fields (e.g., `Invoice.#22`: `A`=Automatic, `D`=Deleted, `M`=Manual; `Invoice.#28`: blank=Non-STP, `*`=<semi-annual, `S`=semi-annual, `A`=annual; `Invoice.#52`: `!`=Printed on PrePayReport, `*`=CC charged; `Members.#57`: `A/9-1/S/I/C/Z`; `BOed`: `*/!/@/A-Y`; `QCTrail.3`: `1/2/A/B/F/G/J/K`; `ProgramSpecials.35`: `0/1/A`).
* **Precedence Rule:** Live `prc.*` source code is authoritative over ~5-year-old spreadsheets. Tag spreadsheet-only flag definitions as `Client-Doc-Reported (~5 yrs old; verify in live code)` and flag any drift (e.g., `Invoice.60` marked `Unused` in `fp_Invoice.xlsx` vs actively used as `Facility_ID` in `Invoice/prc.inputI:L1357`!).

## 3. Required Description Lifecycle & Timebox Guardrails
For every field in the timebox:
* **If referenced in `prc.*` (`>0` refs):** Document (a) allowed states & edits (`" "` / `"1"` / `"2"` / `"Y"` / `"Z"`), (b) where it is **set / mutated** (exact `Table/prc:Lnnn` + label), (c) where it is **read / gated** (e.g., `AllDone`, `PrtInv`, `GenUpdt`), and (d) downstream side effects (`2BPrtd`, `Push_2_SQL`, `OrderBO`, print forms).
* **If `0` code refs in current drop:** Mark status explicitly as **`pending missing processes`** and cite map metadata + `data-profile` observed fill/values + any `fP Table Documentation` notes — never guess what missing batch programs do with `#45 Special_Acct_Rpt_Flag` or `#53 Push2InvoiceSummaryFlag`.
* **No PII / Personal Data (PSA §7):** Use only aggregate `data-profile` value distributions (`fill_rate`, allowed code tokens `<=30` values). Never include customer names, emails, DOBs, or account PII.
