---
name: fait-flag-catalog
description: Dedicated skill for PSA Deliverable #4 — timeboxed contextual analysis of filePro flag and status-indicator fields across code, separating true control flags from timer/numeric false positives.
version: 2.0.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `fait-flag-catalog`

Use this **separate specialist skill** exclusively to produce **PSA Schedule A Deliverable #4**: *Filepro data “flag” field usage descriptions (based on contextual analysis of field usage throughout the code base), on a scoped-effort basis in a timeboxed manner (5 business days per drop)*.

## 1. Candidate Classification Scope (`evidence-pack/flags/flag-candidates.json`)
Inspect all candidates deterministically surfaced by `fp-normalize.js`:
1. **Explicit `*flag*` dictionary fields** (e.g., `Invoice.#21 Print Flag`, `Invoice.#22 Posting Flag`, `Members.#33 Drop Ship Flag`, `Invoice.#331–355 B1) Backorder Flag`, plus 0-ref candidates `Invoice.#45/#53/#55`, `Members.#69/#111`, `Prices.#101`).
2. **Behavioral control flags & status switches:** `yesno` edits, `length === 1` code switches, `status`/`post`/`suppr`/`exempt`/`stp` indicators (`Invoice.#28 Ship to patient?`, `#29 Zero Shipping Charge Status`, `#31 (I)nvoice/(C)redit`, `#34 WEB_2_Patient`, `#35 Suppress Prices on Invoice`, `#391–415 B3) Don't Post`), and `dim` field-overlay aliases (`BOed(25):331`, `post(25):391`).
3. **Process-level flags:** Local/global workflow control variables (`multflag`, `WTP_Update_FLAG`, `manualBO`, `useException`, `priceLikeCheck`, `NoExceptions`, `FreeGood`).
4. **False-Positive Timer/Counter Exclusions:** **NEVER classify non-boolean timers/handles as status flags without verifying their edit & usage!** Classic proven counterexample: **`dim Flag(60)(8,HMS,G)`** (`Invoice/prc.inputI:L0097, L1054, L2015–L2031`) stores `HMS` timestamps comparing `@tm gt Flag(PWFunction)+".167"` (~10-minute `Library/Password` authorization expiry cache).

## 2. Required Description Lifecycle & Timebox Guardrails
For every field in the timebox:
* **If referenced in `prc.*` (`>0` refs):** Document (a) allowed states & edits (`" "` / `"1"` / `"2"` / `"Y"` / `"Z"`), (b) where it is **set / mutated** (exact `Table/prc:Lnnn` + label), (c) where it is **read / gated** (e.g., `AllDone`, `PrtInv`, `GenUpdt`), and (d) downstream side effects (`2BPrtd`, `Push_2_SQL`, `OrderBO`, print forms).
* **If `0` code refs in current drop:** Mark status explicitly as **`pending missing processes`** and cite map metadata + `data-profile` observed fill/values — never guess what missing batch programs do with `#45 Special_Acct_Rpt_Flag` or `#53 Push2InvoiceSummaryFlag`.
* **No PII:** Use only aggregate `data-profile` value distributions (`fill_rate`, allowed code tokens `<=30` values).
