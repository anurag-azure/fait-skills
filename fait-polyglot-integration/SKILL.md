---
name: fait-polyglot-integration
description: Dedicated skill for PSA §4(a) cross-stack seams across Bash Scripts, Cron Schedules, PHP Scripts, MariaDB 4-seam Sync, C# AvaTax/Gfk, Python UPS Sort, Ruby Payment Gateway, PROSHIP, USPS API v3, AutoFTP EDI, Faxage, and filePro boundaries (incl. 20261118_Drop).
version: 2.2.0
applies_to: [WF-F1, WF-F2, WF-F3]
---

# Skill: `fait-polyglot-integration`

Use this **separate specialist skill** whenever analyzing `evidence-pack/dependencies/polyglot-edges.json`, `system` OS shell commands inside `prc.*`, PHP/MariaDB web order bridges, Cron batch timing, or external API/table handoffs (**Signed PSA Schedule A §4a**).

## 1. Cross-Stack Integration Surface (`Signed PSA §4a` + Reconciled Lucid Charts & Client Docs)
Trace end-to-end execution cleanly across these non-`PRC` integration layers:
1. **Cron Schedules → Bash / Python / Expect Scripts (`20261118_Drop`):**
   * Periodic jobs fetching orders into staging tables (`WEBHpending`/`WEBDpending` → `WEBHeader`/`WEBDetail`) and pushing updates back (~5-min `Push_2_SQL`, 10-min `db_request` & `FAXAGE_Processing`, 5-min `AutoFTP` SFTP/FTP/LFTP/SCP for X12 `850/852/867/997` + Expect `AutoGFK` + C# `/appl/dotnet/GfkReport`, 5-min `WVAOpticsJobs` CSV M–F 7a–6p / Sat 7a–3p, `backup3:/appl/bin/ups_sort_pull.py` -> `Shipping@alt` + `QCTrail "N"`).
   * Overnight & monthly batch orchestration: `/bin/OverNight` (2–2.5 hr, `OverNight_CHECK` 1:30a & 3:30a, `/etc/no_user_login`, 46-min 18-thread `RecoverIndexes All` across `736` tables, Sunday `/bin/MiscJobs ArchiveInvoice`), M–F `MakeOrdersPGC` (`PO_Group_Config`, 5a/10:30a/2p), `AutoMonthlyTaxCommit.sh` (by 10th, 8 `xargs` workers on `Statement@YYYYM`), `/appl/bin/nest` cron stderr wrapper, and `flux.php` change tracker.
   * Export & carrier wrappers: `/appl/bin/WEBExport`, `/appl/bin/push_tnt`, `/appl/bin/QPrint`, `/appl/bin/fprint`, `/appl/bin/getShipment`, `/appl/bin/postShipment`, `/appl/bin/RecievedFiles` (`InventoryThresholdCheck` -> `ImportData/3PO_Inventory_Threshold`), `/appl/bin/SHIMS/` (non-prod outbound shims on `fptest`).
2. **Bash Scripts → filePro CLI (`rclerk` / `rreport` / `dclerk`):** Extract exact target table, screen (`-s`), input/auto proc (`-z`, `-y`), selection set/table (`-S`, `-V`), and unattended runtime parameters (`-rz` → `@pz`, `-rx`, `-ry`, `-rw`, `-FP`, `-RO`, `-U`).
3. **filePro `SYSTEM` Calls → OS / External Wrappers:** Capture shell commands launched from `prc.*`, checking `chmod 777` risks (`L2059`, `/appl/log/shim_log`), shell interpolation (`L2094`, `L2149`, `L2702`), print spool deletion (`lpr -r -pA07 STMNTS`), routine `root` jobs (`AutoFTP`, `SR`, `Archive FP Data`), and whether `CLOSE`/`FREE` flushed buffers beforehand.
4. **filePro ↔ 4 Active MariaDB Sync Seams (Replacing Abandoned ODBC — `20261118_Drop`):**
   * Native filePro ODBC was formally **abandoned** (`152s` per 10k `getnext` rows; `Write` exits processing) and replaced by 4 active seams against **`dbprod1`/`faitdb-prod`** (`wva`, `wva2`, `fait_edi`, **`fp_info`** nightly metadata catalog) and **`webprod.wisvis.com`**:
     1. `/appl/bin/WEBExport` nightly `dreport` pipe-delimited `LOAD DATA LOCAL INFILE` (`load-*.sql`);
     2. Real-time named-pipe `user data=/appl/bin/SQL/ToSQL.sql` (`INSERT ... ON DUPLICATE KEY UPDATE`);
     3. Batch `/appl/bin/SQL/Insert-UpdateI.php` (`UpdateWEBStatus`/`InvoiceStatus`);
     4. PHP Database Middleman (`db_request` generating temp PRCs every 10 min; `fp_sql_query` keyed by `sql_key`).
   * Map exact column-to-filePro-field correspondences (`Invoice.33 -> wva.orders`, `SQLID -> wva2.order_detail.id`, `fait_edi.edi_file_store`, `webprod.payment_card_tokens`) without inventing missing DDL.
5. **External APIs & FilePro Subprocesses:** Track `AvaTax_Api (C#)` + `Library/AvaTax_Api_Backup` + `AvaTax_SpecialExemptions`, `PROSHIP`/`PROSHIPLOG`, **`USPS API v3` (OAuth2, Jan 2025)** via `Library/prc.VerifyAddressUSPS` -> `validate_incoming_address.php` -> `/appl/php/scripts/utility/carrier_util.php` (FedEx/UPS/USPS), **`Stripe API` (`WTP_Charges`)** & **LIVE `Elavon` (`TokenTransactions`)** vs. legacy `1stPayGateway` (Rails `/www/RAILS/gateway/rt.rb` SSH), **`Faxage`** (`Print2FAX -> SendFAX`), `Innovation LMS` (`WVAOpticsJobs` 5-min CSV from DOS `P:\wvalab`), `fopadmin.wisvis.com` Asterisk `astmanproxy`, `ECP E360+`, `Alcon Marlo`, `Commerce Hub/Rithum`, and `VerifyRX`/`VerifyRX2`.

## 2. Fail-Closed Boundary Rule
While Bash, Cron, PHP, C#, Ruby, and MariaDB DDL artifacts are still pending delivery from FAIT (`polyglot-edges.json` `standalone_scripts_count: 0` on POC drop), strictly separate **Code-Verified** hooks from **Diagram-Depicted** and **Client-Doc-Reported** details, and mark missing scripts/DDL `pending PSA §4(a) polyglot drop`.
