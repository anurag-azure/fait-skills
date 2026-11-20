---
name: fait-domain-glossary
description: Optical wholesale distribution and WVA/FAIT business domain glossary, account/partner mappings, file qualifiers, flag/status codes, web-intake split rules, and Slide 3-4 UI flows.
version: 2.1.0
applies_to: [WF-F1, WF-F2, WF-F3, WF-F4, WF-F5]
---

# Skill: `fait-domain-glossary`

Shared business domain terminology for all FAIT agents so specs use consistent optical distribution and WVA/FAIT system terms grounded in source code, Lucid Chart diagrams, and client project documentation (`docs/project/`).

## 1. Business, Account & Partner Terms
* **FAIT / WVA:** **Wisconsin Vision Associates, Inc. d/b/a Fait Distribution** (code strings frequently show `"WVA"` / `"Wisconsin Vision"`), Burlington, WI wholesale distributor of contact lenses & ophthalmic supplies (operating across `State St.` & `Pine St.` facilities).
* **Members / ECP / OD:** Eye Care Professionals / Optometrist practices ordering stock or patient lenses (`Members` table).
* **Key Price-Like / Enterprise Partner Mappings (`CSR_Documentation/` & `Invoice/prc.inputI`):**
  * **`15000` = Panther = Costco** (`contacts.costco.com`, `InvSTO-15000`, Panther FastPass).
  * **`30000` = Hulk** (`Hulk OMS` / `Hulk BOSS` / `Hulk ECOM` / `Hulk EDI`, `InvSTO-30000`).
  * **`7500` = Shopko** (transitioned to Fielmann Group per `Invoice Posting.png`).
  * **`55000` = HiWaldo** (STP descriptions drop `"Patient"` at `L0632`).
  * **`99999` = FAIT Demo Practice Account** (blocked from floor printing at `L2033`; CSR training warns un-deleted practice invoices deduct real production stock!).
  * **`98765Z` = 3PO Test Account** (skips inventory decrement/restock at `L1344`/`L1365`).
  * **`Optiport` (`F0` Web Kickout bucket):** Includes Price-Like accounts `17000` (SVS Vision), `9900` (Grene Vision), `1743` (Bard Optical), `8900` (Dr. Tavel).
  * **Private Label Prefixes (`Training Manual 2026`):** `V` = Vision Source (`VSP`), `P` = Pearle, `E` = PERC, `C` = LensCrafters.
* **Buying Groups & `PLAccount`:** Practice alliances sharing negotiated pricing via **Price-Like Account (`PLAccount`)** fallback (`Exceptions` retries) unless an exception has `Exclusive to this Member = Y` (`Exceptions.1`). Buying group billing/rewards are controlled by `Members.46 BG_Type` (`1-4`: check, ACH, $6k/quarter, 1.25% YourRewards), `Members.47-51`, `Vendors.18` (`D`=Distribution, `B`=Buying Group), `BGProductLines`, `DiscException`, and `BGSummary`.
* **Alcon Marlo (`MRLO` / `exc(44)` `third_party_id`):** Alcon direct-to-patient/ECP portal (`Invoice.59="MRLO"`). Marlo pricing applies ONLY to Alcon orders placed through Marlo (Loyalty Lock honored); 30-min Pending edit window in Marlo, after which Fait CSRs cannot modify or partial-ship (`L1618` blocks update; must cancel & reorder). Supports **Bridge Trials** and **Marlo VROs** (`Pcode 0092` = Alcon UPS shipping).
* **`CLK` / EyeCare Partners (`ECP` — `Members.34/103/128`):** Clarkson Eyecare / ECP affiliation triggering historical `DateOrderTaken` pricing (`Pricing:L218`, `L190`). Per Nov 2026 `Quick Reference ECP.docx`, all ECP orders must come via **E360+** EMR (no phone/web orders; STP trial-only orders auto-canceled unless revenue items or expedited shipping; CSR cannot alter qty/prices/line order).
* **Trial Lens Bank (`Trial_Lens_Bank_Code`, `TrialLens`):** Diagnostic fitting lens banks maintained at clinics; sales tracked separately via bank codes (`bj`, `Ex1("4")`, `Exceptions.7/20`). `TBL` = Trial Bank Locked kickout.
* **Product Types (`fp_Inventory_Product_Type.xlsx`):** `0`=Fitting Sets, `1-3`=Sphere/Toric/Specialty Vial, `4-6`=Sphere/Toric/Specialty Trials, `7-9`=Sphere/Toric/Specialty Blister, `A`=Protective Equip, `B`=Banks, `C`=Supplies, `F`=Frames, `G`=Gift Cards, `I`=General Information, `L`=Stock Lenses, `M`=Misc, `O`=Solutions, `P`=Pharmaceuticals, `S`=Sunglasses, `W`=Marketing Solutions, `Z`=WVA Instrument.

## 2. Order, Fulfillment, Billing & Integration Terms
* **`STP` vs `STO`:** **Ship-to-Patient** (`Invoice.#28` non-blank: `*`=<semi-annual, `S`=semi-annual, `A`=annual supply; carries patient name/address PHI in `#5–#10`) vs **Ship-to-Office** (`Invoice.#28` blank; `InvSTO-Default`, `InvSTO-15000`, `InvSTO-30000`).
* **YourStore (`WTP`) / EyeSubscribe / Text2Order:** Patient e-commerce (`Invoice.#34="*"`). Places pending card authorization hold at checkout, captures via **Stripe API** (`WTP_Charges`) during warehouse Check, releases hold (1–7 days) on cancel. CSR cannot modify dollar amount (`WTP_Update_Flag`, `L1629-L1638`).
* **Billing & Payments:** Most wholesale accounts receive monthly **Statements** (`Statement`, `/space/StatementHistory/`); optional `Pay_By_Invoice` (`Members.168`, `OpenInvoices`); Lotus accounts use `Prepay_Account` (`Members.104`, `Invoice.52 PrePayComplete`). Note payment-vault transition conflict between `1stPayGateway` (Lucid Chart) and `Elavon` (`wva_data_table_list.xlsx` / `PaymentCardTokens`).
* **Web Order Split & Kickouts (`WEBHpending` → `WEBHeader`/`WEBDetail` → `Invoice`):** Automated intake caps each Invoice record at **25 lines** (`#61–#85`), linking multi-invoice orders via **`#33 WEB Orders#`** (`wva.orders` / `wva2.order_detail.id`). Kickout queues (`W-F`): `A0` (Un-Processed), `A1` (NegQty-ExpShip), `F0` (Optiport), `R0` (70+ trials).
* **Warehouse & Audit Flags:** **`QCTrail.3 Job`**: `1`=Print Pickticket, `2`=Print Invoice, `A`=End Pick, `B`=Start Pick, `F`=Start Check, `G`=End Check, `J`=Pack, `K`=Ship. **`BOed` (`Invoice.331-355`)**: `*`=on-order, `!`=manual F3 fill, `@`=filled from PostMenu, `<`=BO All, `>`=Dropship, `A-Y`=original invoice line. **`FFS`** = Fill From Stock (when R-screen QOH ≥ 5).
* **Deferred Print, Recovery & Sync Queues:** **`2BPrtd`** (print queue), **`OrderBO`** (backorder trigger), **`DlySave` / `Invoice dly`** (daily snapshot used by ITHD to restore randomly corrupted Invoice records), **`Push_2_SQL`** (`Invoice.#44`: `1`=queued, `Z`=pushed, ~5-min MariaDB sync with `dim SQLID(25):421`), **`Anzio`** (Windows terminal emulator for BlueScreen).
* **FilePro Key Qualifiers:** `tj` (test qualifier), `hist`/`hst` (historical), `tot` (totals), `@OPT` (credit-memo `DocType="C"` pricing qualifier in `PricesTable@OPT`), `Facility` (`Inventory@<Facility>`), `dly` (`Invoice dly` daily recovery copy).
* **Slide 4 Operator Flow (`PostPOC.pptx`):** **`Prices` (`18` screens) → Press `P` → `PricesTable` (`9` screens) → `Lookup` `Vendors`**.
