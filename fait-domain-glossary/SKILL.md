---
name: fait-domain-glossary
description: Optical wholesale distribution and WVA/FAIT business domain glossary, file qualifiers, web-intake split rules, and Slide 3-4 UI flows.
version: 2.0.0
applies_to: [WF-F1, WF-F2, WF-F3, WF-F4, WF-F5]
---

# Skill: `fait-domain-glossary`

Shared business domain terminology for all FAIT agents so specs use consistent optical distribution and WVA system terms rather than generic boilerplate.

## 1. Business & Optical Wholesale Terms
* **FAIT / WVA:** **Wisconsin Vision Associates, Inc. d/b/a Fait Distribution** (code strings frequently show `"WVA"` / `"Wisconsin Vision"`), Burlington, WI wholesale distributor of contact lenses & ophthalmic supplies.
* **Members / ECP / OD:** Eye Care Professionals / Optometrist practices ordering stock or patient lenses.
* **Buying Groups & `PLAccount`:** Practice alliances (e.g., Vision Source, IDOC, CECOP) sharing negotiated pricing via **Price-Like Account (`PLAccount`)** fallback (`Exceptions` retries) unless an exception has `Exclusive to this Member = Y` (`Exceptions.1`).
* **3PO (`exc(44)` `third_party_id`):** Third-party managed vision administrator filter checked before falling back to regular member exceptions (`Library/prc.Pricing:L086`).
* **`CLK` (`Members.34/103/128`):** Affiliation code triggering historical `DateOrderTaken` pricing instead of current invoice date (`Pricing:L218`, `L190`).
* **Trial Lens Bank (`Trial_Lens_Bank_Code`, `TrialLens`):** Diagnostic fitting lens banks maintained at clinics; sales tracked separately via bank codes (`bj`, `Ex1("4")`, `Exceptions.7/20`).
* **Years Supply / Multi-Pack / FreeGood / NoExceptions:** Program flags controlling tier pricing, bypass paths (`Pricing:L037–L038`), and patient incentives.

## 2. Order, Fulfillment & Integration Terms
* **`STP` vs `STO`:** **Ship-to-Patient** (`Invoice.#28="Y"`, `#243="Y"`, form `InvSTP-Default`, carries patient name/address PHI in `#5–#10`) vs **Ship-to-Office** (`InvSTO-Default`, `InvSTO-15000`).
* **Web Order Split (`WEBHpending` → `WEBHeader`/`WEBDetail` → `Invoice`):** 10-minute bash/cron intake pulls web/REST/SFTP/email orders; `FillInv`/`WEBOrdr` caps each Invoice record at **25 lines** (`#61–#85`), linking multi-invoice orders via **`#33 WEB Orders#`**.
* **Deferred Print / Sync Queues:** **`2BPrtd`** (print/warehouse queue), **`OrderBO`** (backorder trigger), **`DlySave` / `Add2Log`** (daily snapshot/audit log), **`Push_2_SQL`** (`Invoice.#44` flag queuing MariaDB sync with `dim SQLID(25):421`).
* **FilePro Key Qualifiers on Disk & in Code:** `tj` (sanitized test qualifier `Invoice/keytj`), `hist` / `hst` (historical invoices), `tot` (totals), `@OPT` (credit-memo `DocType="C"` pricing qualifier in `PricesTable@OPT`), `Facility` (`Inventory@<Facility>` warehouse stock).
* **Slide 4 Operator Flow (`PostPOC.pptx`):** **`Prices` (`18` screens) → Press `P` → `PricesTable` (`9` screens) → `Lookup` `Vendors`**.
