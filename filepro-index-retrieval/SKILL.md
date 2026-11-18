---
name: filepro-index-retrieval
description: Analyzes filePro automatic/demand/named indexes (index.0-9, index.A-Z, indexView.*), lookup flags (-nx/-ng/-nl/-o/-p/-w), lookup - cursor jumps, and composite keys with strict dxmaint uncertainty rules.
version: 2.0.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `filepro-index-retrieval`

Use this **separate specialist skill** whenever analyzing `lookup` statements, `getnext` / `getprev` scan loops, target database indexing recommendations, or `indexes.json` inventories.

## 1. Index Classification (`evidence-pack/tables/<Table>/indexes.json`)
* **Automatic Indexes:** `index.A`..`index.Z` (updated live on record write).
* **Demand Indexes:** `index.0`..`index.9` (rebuilt in batch or on demand — warn of stale-read risk if queried live without rebuild).
* **Qualifier / Named Indexes:** `Members/indexView.*` (`A-Z`), `Members/indexHubSpot.A`, `Invoice/indextj.A`..`P`, `Prices/index.0-9, A-F`.

## 2. Lookup & Retrieval Semantics (`fPManual.pdf` p. 517–518)
* **`lookup alias = Table@ k=... i=X <flags>`:**
  * `-nx` = exact match only.
  * `-ng` = greater-than or equal match (used with `getnext alias` to walk ranges).
  * `-nl` = less-than or equal match (used with `getprev alias`).
  * `-o` = partial/prefix key match (high false-match risk unless key length is guarded in `prc`).
  * `-p` = protected/locked read; `-w` = wait/fail if locked; `-e` = fail on lookup error.
* **`lookup - k=... i=X`:** Moves the cursor inside the **current** table rather than opening an external alias.
* **Composite Key Verification Rule:** `fp-normalize.js` intentionally does **not** decode binary index headers (`indexes.json`). You MUST infer composite index structure **only** when `prc` either states it explicitly in code comparison (e.g., `exc(2) & exc(3) & exc(4) & exc(5) & exc(6) ne ky` at `Library/prc.Pricing:L084` confirming `Exceptions/index.A` = fields `2,3,4,5,6`) OR cites a verified `dxmaint` screenshot (`07-Gaps` B5). For all other indexes (e.g., `Members` 51 indexes, `Prices/index.A`), report key composition as **`unverified pending dxmaint screenshot`**.
