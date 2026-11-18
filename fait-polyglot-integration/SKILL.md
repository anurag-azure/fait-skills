---
name: fait-polyglot-integration
description: Dedicated skill for PSA §4(a) cross-stack seams across Bash Scripts, Cron Schedules, PHP Scripts, MariaDB Schema, and external REST/EDI/filePro boundaries.
version: 2.0.0
applies_to: [WF-F1, WF-F2, WF-F3]
---

# Skill: `fait-polyglot-integration`

Use this **separate specialist skill** whenever analyzing `evidence-pack/dependencies/polyglot-edges.json`, `system` OS shell commands inside `prc.*`, PHP/MariaDB web order bridges, Cron batch timing, or external API/table handoffs (**PSA §4a**).

## 1. Cross-Stack Integration Surface (`PSA §4a` 6 Artifact Types)
Trace end-to-end execution cleanly across these non-`PRC` integration layers:
1. **Cron Schedules → Bash Scripts:** Periodic jobs (e.g., `10`-minute polling scripts) fetching e-commerce/REST/SFTP/email orders into staging tables (`WEBHpending`, `WEBDpending` → `WEBHeader`, `WEBDetail`).
2. **Bash Scripts → filePro CLI (`rclerk` / `rreport` / `Irclerk`):** Extract exact target table, screen (`-s`), input/auto proc (`-z`, `-y`), selection set/table (`-S`, `-V`), and unattended runtime parameters (`-rz` → `@pz`, `-rx`, `-ry`, `-rw`, `-FP`, `-RO`, `-U`).
3. **filePro `SYSTEM` Calls → OS / External Wrappers:** Capture shell commands launched from `prc.*`, checking whether `CLOSE`/`FREE` flushed record buffers beforehand (`Archive`, `DlySave`, external print/email hooks).
4. **filePro ↔ PHP ↔ MariaDB Bridges:**
   * Document known SQL synchronization seams surfaced inside `Invoice/prc.inputI` & `Members`: `Invoice.44` (`Push_2_SQL`, `44="1"`), `dim SQLID(25):421` (per-line MariaDB row FK), and `Members/indexHubSpot.A`.
   * When PHP scripts and MariaDB DDL (`CREATE TABLE`, PDO/mysqli queries, HubSpot/ecommerce endpoints) arrive in follow-on drops (`POLY:<file>:L<n>`), map exact column-to-filePro-field correspondences without inventing missing schema definitions beforehand.
5. **External APIs & FilePro Subprocesses:** Track calls to `Invoice/AvaTax_Api`, `Library/AvaTaxCommitted`, `Invoice/InvoiceShipping`, `Invoice/ChkSTP`, `Library/ValidSKU`, and `Library/Password` cleanly using extended `workflow.schema.json` edge attributes (`kind`, `qualifier`, `mode`, `evidence_ref`).

## 2. Fail-Closed Boundary Rule
While Bash, Cron, PHP, and MariaDB artifacts are still pending delivery from FAIT (`polyglot-edges.json` empty on POC drop), document **only** observed `prc.*` / `additional-context.md` seams (`WEBHeader`/`WEBDetail`, `Push_2_SQL`, `SQLID`, `system` calls) and mark external PHP/MariaDB schemas `pending PSA §4(a) polyglot drop`.
