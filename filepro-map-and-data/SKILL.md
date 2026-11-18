---
name: filepro-map-and-data
description: Analyzes physical filePro table map schemas (dictionary.json), field groups, key data profiles (data-profile.json), edits (edits.json), and sample-data bias without confusing map files with UI navigation flows.
version: 2.0.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `filepro-map-and-data`

Use this skill when building **Data Analysis**, **Entity Data Dictionaries**, and **Schema Mapping** deliverables (`PostPOC Slide 6`).

## 1. Critical Correction: What a `map` File Actually Is
* A filePro `Table/map` file defines **physical record structure**, NEVER control flow or screen navigation (that was a defect in legacy `fptech` templates).
* Header format: `map:<record_length>:0:<field_count>`. Line `n + 1` defines Field `n` as `Label:Length:Edit:`.
* Read `evidence-pack/tables/<Table>/dictionary.json`, `data-profile.json`, and `edits/edits.json` instead of counting offsets manually.
* **Complete `1..N` Field Coverage (Never Truncate):** Document **every** field `1..N` from `dictionary.json` (e.g., all `112` `Prices` fields, all `19` `PricesTable` fields, all `505` `Invoice` fields) plus a complete **Edit Type Legend** and **Key File Storage Profile** (`records_total`, `active_records`, `record_stride`, `header_flag_counts`, and `top_shapes` code patterns). When a complete table exceeds `12 KB`, generate `specs/<module>/data_dictionary.md` deterministically via a Node script reading `evidence-pack/tables/*/*.json` rather than dropping fields.

## 2. Structural & Repeating Group Patterns
* **Repeating line-item groups:** Tables like `Invoice` encode fixed arrays directly in fields — e.g., `25` consecutive slots for Product Code (`61–85`, `dim stkp(25):61`), Qty (`151–175`, `dim qty(25):151`), Price (`181–205`), Backorder Flag (`331–355`, `dim BOed(25):331`), Don't Post (`391–415`, `dim post(25):391`), `SQLID` (`421–445`, `dim SQLID(25):421`). Normalize repeating groups into child entities (`InvoiceLine` / `PriceTier`) in target ERDs.
* **Placeholder / Spare slots:** Empty labels with length `0` (`placeholder: true`) preserve byte offsets and record length — never renumber fields when documenting them.
* **Record header byte `b[0]`:** In active `key*` files, `header_flag_counts["1"]` indicates active records, while `flag=0` (e.g., `~49%` of `Exceptions`) indicates deleted/free slots.

## 3. Data Profiling, Edits & Sample-Size Guardrails
* **Check sample adequacy before concluding defaults:**
  * `Invoice` currently only ships test qualifier `keytj` (`17` records, all Member `99`, `342/505` fields blank). Flag `low-sample-bias` cleanly.
  * `Members` currently has only `2` active records for `209` fields — state `insufficient data profile` rather than assuming unused fields are dead.
* **Surface anomaly shapes faithfully:** Report malformed values revealed in `data-profile.json` as open questions — e.g., `Exceptions.28 Grace_Period` (`'8/'`), `Qty. 2` (`'.'`/`'61.'`), `23` `S1)` records in `Exceptions`, `Prices.Start_Date` (`01/01/:000`), missing `Cost` under `Method=C` (`10.9%`), `flag=2` slots.
* **Edit Resolution:** Distinguish `custom` (resolved in `Custom_Edits/{local,global}_edits`), `builtin:*` (`38` standard edits), and `unresolved` (such as `mthd`, `ptype`, `prcgp`, `qtydisc`, `tax` missing server `PFGLOB` `/appl/fp/lib/edits`).
