---
name: fait-polyglot-integration
description: Dedicated skill for PSA §4(a) cross-stack seams across Bash Scripts, Cron Schedules, PHP Scripts, MariaDB Schema, C# AvaTax, Ruby Payment Gateway, PROSHIP, and filePro boundaries.
version: 2.1.0
applies_to: [WF-F1, WF-F2, WF-F3]
---

# Skill: `fait-polyglot-integration`

Use this **separate specialist skill** whenever analyzing `evidence-pack/dependencies/polyglot-edges.json`, `system` OS shell commands inside `prc.*`, PHP/MariaDB web order bridges, Cron batch timing, or external API/table handoffs (**Signed PSA Schedule A §4a**).

## 1. Cross-Stack Integration Surface (`Signed PSA §4a` + Reconciled Lucid Charts & Client Docs)
Trace end-to-end execution cleanly across these non-`PRC` integration layers:
1. **Cron Schedules → Bash Scripts:**
   * Periodic jobs fetching e-commerce/REST/SFTP/email orders into staging tables (`WEBHpending`/`WEBDpending` → `WEBHeader`/`WEBDetail`) and pushing status updates back (~5-minute cadence for `Push_2_SQL` per `FP_backorder invoice update.docx`).
   * Overnight batch orchestration (`/bin/OverNight`, `/appl/log/OnDoneYYYYMMDD`, `/appl/log/OnFLags`, `/appl/Flags/ONStopped`, `/etc/no_user_login` maintenance lockout per `FP_OverNight_Incomplete_Nologin.docx`).
   * Export & carrier wrappers: `/appl/bin/WEBExport` (`rreport Prices -fp WEBPricing -i9 -u -r 6615 -rz auto`, log `/appl/log/WELog`; `/appl/bin/WEBExport Vendors`), `/appl/bin/push_tnt` (pushes `TimeInTransit` to Proship), `/appl/bin/QPrint`, `/appl/bin/fprint`, `/appl/bin/getShipment`, `/appl/bin/postShipment`, `/appl/bin/ReceivedFiles`.
2. **Bash Scripts → filePro CLI (`rclerk` / `rreport` / `dclerk`):** Extract exact target table, screen (`-s`), input/auto proc (`-z`, `-y`), selection set/table (`-S`, `-V`), and unattended runtime parameters (`-rz` → `@pz`, `-rx`, `-ry`, `-rw`, `-FP`, `-RO`, `-U`).
3. **filePro `SYSTEM` Calls → OS / External Wrappers:** Capture shell commands launched from `prc.*`, checking `chmod 777` risks (`L2059`), shell interpolation (`L2094`, `L2149`, `L2702`), print spool deletion (`lpr -r -pA07 STMNTS`), and whether `CLOSE`/`FREE` flushed record buffers beforehand.
4. **filePro ↔ PHP Middleman ↔ MariaDB Bridges (Signed PSA §4a confirms **MariaDB**):**
   * Document observed SQL synchronization seams across **3 logical SQL databases** (`wva SQL`, `wva2 SQL`, `hulk_ecom SQL`):
     * `Invoice.33` (`WEB Orders#` → `wva.orders`), `Invoice.44` (`Push_2_SQL`: `1`→`Z`), `dim SQLID(25):421` (`wva2.order_detail.id` per `BkordWebUpdts.xlsx` & `BossOrderAudit.xlsx`), `fp_PaymentCardTokens.2` (`webprod.payment_card_tokens`).
     * PHP middleman staging tables (`php`, `fp_sql_query`, `Export`, `sql_query`, `sql_temp_a`, `util` per `wva_data_table_list.xlsx`).
     * `Members/indexHubSpot.A` and `Members.129 SalesforceID`.
   * When PHP scripts and MariaDB DDL (`CREATE TABLE`, PDO/mysqli queries) arrive in follow-on drops (`POLY:<file>:L<n>`), map exact column-to-filePro-field correspondences without inventing missing DDL beforehand.
5. **External APIs & FilePro Subprocesses:** Track `AvaTax_Api (C#)` & `Library/AvaTax_Api_Backup` (`Api_Keys` table), `PROSHIP`/`PROSHIPLOG`, `Stripe API` (`WTP_Charges` via `WebProd` Bash), `1stPayGateway` (Rails `/www/RAILS/gateway/rt.rb` SSH) vs `Elavon` (`PaymentCardTokens` per `wva_data_table_list.xlsx`), `Innovation LMS` (`WVAOpticsJobs`), `ECP E360+`, `Alcon Marlo`, `Commerce Hub/Rithum`, and `VerifyRX`/`VerifyRX2`.

## 2. Fail-Closed Boundary Rule
While Bash, Cron, PHP, C#, Ruby, and MariaDB DDL artifacts are still pending delivery from FAIT (`polyglot-edges.json` `standalone_scripts_count: 0` on POC drop), strictly separate **Code-Verified** hooks from **Diagram-Depicted** and **Client-Doc-Reported** details, and mark missing scripts/DDL `pending PSA §4(a) polyglot drop`.
