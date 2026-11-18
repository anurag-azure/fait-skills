---
name: filepro-report-and-xlsx
description: Decodes filePro rreport/dreport batch outputs, out.* print formats (*N and *aa tokens), FORM/EXPORT/PDF_* jobs, fileProXLSX XL_* functions (xlsx_docs.pdf), and cron batch triggers.
version: 2.0.0
applies_to: [WF-F1, WF-F2, WF-F4]
---

# Skill: `filepro-report-and-xlsx`

Use this **separate specialist skill** whenever a module involves `out.*` output formats (`evidence-pack/tables/<Table>/output-formats.md`), `rreport`/`dreport` batch output/selection procs, `FORM`/`PRINTER`/`EXPORT`/`PDF_*`, `XL_*` spreadsheet generation, or cron/print-queue triggers.

## 1. Output Processing (`rreport` / `dreport` & `FORM` Dispatch)
* **CLI Flags (`fPManual.pdf` p. 705–708):** `-S <selection_set>`, `-V <select/sort_proc>`, `-FP <proc_without_output_format>`, `-F <output_format>`, `-A` select all, `-P` printer/file redirect.
* **Event Sequence:** `@once` (run once before record selection) → main output processing per record → `@wbrk1..N` (subtotal breaks) → `@wgt` (grand total) → **`@done` (executes at end of run even when 0 records were selected!)**.
* **`out.*` Format Token Grammar:**
  * **`*N`** (digits) = real field `N` from `dictionary.json` (e.g., `*61`..`*85` Invoice line product codes).
  * **`*aa`** (letters) = dummy/declared print variable (`*va`..`*vz`, unlike `!xx` on screens!).
  * Raw `out.*` strings often preserve printer/header binary prefixes and form names (e.g., `InvSTO-Default`, `InvSTP-Default`, `InvSTO-15000`); never fabricate visual formatting unless sample PDFs are provided.

## 2. `fileProXLSX` (`XL_*` Built-In Functions — `xlsx_docs.pdf`)
* When billing/reporting drops use `XL_*` functions (`XL_OPEN`, `XL_SAVE`, `XL_ADDSHEET`, `XL_ADDCELL`, `XL_CHART`, `XL_STYLE`, `XL_FREE`, etc.), recognize that numeric handles are stored in `declare handle(8,.0)`.
* Distinguish `XL_SAVE` AES128 password-protected exports (`security/PII` check) and map each `XL_ADDCELL` write back to source fields and `@wbrkN` totals.

## 3. FAIT Batch Print & Cron Seams
* Track deferred warehouse/print staging writes (`2BPrtd` queue, `OrderBO`, `DlySave`, `PrtInv` `L1885`, `PickLst` `L2108`, `InvForm` `L2163`, `#21 Print Flag`, `#28 Ship to patient?`) separately from synchronous `inputI` updates.
