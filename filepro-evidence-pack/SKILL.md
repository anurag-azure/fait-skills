---
name: filepro-evidence-pack
description: Mandatory contract for reading evidence-pack/, verifying exact line citations and substring quotes, handling missing drop artifacts fail-closed, and enforcing PSA Zero-PHI compliance.
version: 2.0.0
applies_to: [WF-F1, WF-F2, WF-F4]
---

# Skill: `filepro-evidence-pack`

You NEVER read raw binary drop folders (`_input/`, `FAIT code drop/key*`, `decoded-data/*.csv`) during specification or architecture generation. Read **only** the deterministic text and JSON artifacts in `evidence-pack/`.

## 1. Evidence Pack Directory Contract
| Artifact Path | Contents & Use |
|---|---|
| `evidence-pack/processes/<Table>.<proc>.annotated.txt` | Line-numbered `Lnnn \| label \| cond \| act // comment => field_refs` (0x01 already restored). Primary source for business rules. |
| `evidence-pack/processes/<Table>.<proc>.json` | Parsed AST-lite summary: labels, lookups, calls, systems, jumps, declares, field_refs. |
| `evidence-pack/tables/<Table>/dictionary.json` & `.md` | Physical `map` schema: field number (`N = map line N+1`), label, length, byte offset, edit, repeating groups. |
| `evidence-pack/tables/<Table>/data-profile.json` & `.md` | Aggregates only (fill rate, distinct count, top value shapes, safe enums for short code fields). |
| `evidence-pack/tables/<Table>/screens.md` & `output-formats.md` | Extracted printable text & resolved `*N` fields; row/col positions are binary. |
| `evidence-pack/tables/<Table>/indexes.json` | Index inventory (`0-9`, `A-Z`, named qualifiers). Key segment definitions require `dxmaint` confirmation unless validated in `prc` comparisons. |
| `evidence-pack/dependencies/dependencies.json` & `.md` | Cross-table lookup/call/system edges, missing tables (`37`), missing processes (`63`), unresolved edits (`17` canonical). |
| `evidence-pack/dependencies/polyglot-edges.json` | PSA §4(a) Bash, Cron, PHP, and MariaDB entry/table edges (when supplied). |
| `evidence-pack/flags/flag-candidates.json` & `.md` | Deterministic inventory of `*flag*`, 1-char/`yesno`, status fields, and read/write line refs for Deliverable #4. |
| `evidence-pack/context/additional-context.md` | Extracted `Additional Context.boxnote` — authoritative client context (web intake cadence, 25-line invoice split `#33`, etc.). |

## 2. Exact Citation Grammar (Verified by Quality Gate)
Every extracted rule, finding, flag description, and flow node MUST cite one or more of these exact forms AND include an exact verbatim `quote` substring from the cited line:
* `<Table>/prc.<proc>:L<n>` or `<Table>/prc.<proc>:L<n>-L<m>` (e.g., `Library/prc.Pricing:L085`)
* `<Table>/map:L<n>` (`map line n` = field `n-1`, e.g., `Exceptions/map:L31`)
* `<Table>.<field#>` (e.g., `Exceptions.30`)
* `<Table>/screen.<name>` or `<Table>/out.<name>` (e.g., `Invoice/screen.1`)
* `NOTE:<section>` (`additional-context.md` section, e.g., `NOTE:Order Intake`)
* `KT@<mm:ss>` or `KT@<mm:ss>#frame` (curated KT transcript / non-PHI UI frame)
* `POLY:<file>:L<n>` (for PSA §4(a) Bash, Cron, PHP, MariaDB artifacts)

## 3. Fail-Closed & Privacy Rules
1. **Never fabricate missing drops:** If a called process (`63` missing, e.g., `InvoiceShipping`, `AvaTax_Api`, `ChkSTP`) or table (`37` missing, e.g., `Inventory`, `Shipping`, `StateTaxRate`, `WEBHeader`) is absent from `evidence-pack/`, record it explicitly as an external dependency gap.
2. **Manuals never override code (`Rule #6`):** `fPManual.pdf` / `xlsx_docs.pdf` explain syntax only; never infer project business behavior from manual examples.
3. **Bounded Reads Only:** Keep reads bounded (`sed -n`, targeted `grep`) within your assigned chunk/module so sandbox turns stay fast (`<=600s` limit).
4. **Zero-PHI Contract (`PSA §7`, `Sched B §2`):** Reject or redact any real personal data (patient names, street addresses, phone numbers, emails, DOB, SSN, card/bank numbers) — never echo unmasked sample records into specs or prompts.
