---
name: filepro-language
description: Authoritative filePro Plus v6.1 syntax, runtime execution semantics, triggers, lookups, arrays, and Custom_Edits rules grounded in fPManual.pdf and FAIT source drops.
version: 2.0.0
applies_to: [WF-F1, WF-F2, WF-F4]
---

# Skill: `filepro-language`

Use this skill whenever reading or interpreting annotated filePro processing (`*.annotated.txt`), runtime CLI triggers, or edit definitions. Never use documentation examples as project business evidence; only cite files inside `evidence-pack/`.

## 1. Physical Files & Line Grammar
* A filePro table directory holds `map` (physical record schema), `key`/`key<qual>` (20-byte record header + fixed-width record), `index.<0-9,A-Z>` / `index<name>.<letter>`, `prc.<name>` (processing source with `0x01` encoded colons), `tok.<name>` (compiled form — use `prc.*`), `screen.<name>`, and `out.<name>`.
* **`map` is schema, NOT navigation control:** `map:recLen:0:fieldCount` followed by `Label:Length:Edit:`. Field `N` is line `N + 1` of `map`.
* **Processing line syntax:** `label : condition : action :` (literal `:` inside code was binary `0x01`, restored to `:` in `*.annotated.txt`). Multiple statements on one action cell are separated by `;`. Comments start with `'` outside double quotes (`// ...` in annotated views).

## 2. Triggers, Modes & Execution Hazards (`fPManual.pdf` p. 705–860)
* **`AUTOMATIC` runs on every record selection/redraw and clears regular (non-global) dummy variables** (`aa`..`zz`) unless declared `,G` / `declare global`.
* **`@entsel`:** Runs when entering Update/Display prompt; `@wefXX` / `@wlfXX` / `@wblXX` / `@wukXX` handle cursor entry, leave, browse leave, and special keys (`F8`/`F9`).
* **`@keyX`:** Locks the current record in multi-user mode, runs `AUTOMATIC` before and after, and **falls through into the next routine if `END` is omitted above it**.
* **Batch / Output triggers:** `@once` runs once before selection; `@wbrkN` / `@wgt` run on subtotal/grand-total breaks; **`@done` executes even when 0 records were selected**.
* **Unattended Batch Mode (`-rz` → `@pz`):** CLI flags `-rw`, `-rx`, `-ry`, `-rz` populate `@pw`, `@px`, `@py`, `@pz` (`-FP` = processing without output format, `-V` = sort/select proc, `-S` = selection set, `-U` = non-locking, `-RO` = read-only). Always inspect `@pz=""` vs `@pz ne ""` (e.g., `Library/prc.Pricing:L087`, `Invoice/prc.inputI:L2566`) because interactive guards and popups often diverge from cron batch runs.
* **`CALL` / `CHAIN` / `SYSTEM` / `FREE`:** `CALL` executes another proc sharing `GLOBAL`/`EXTERN` state and returns on `END`; `CHAIN` transfers control permanently; `SYSTEM` shells out to OS commands (sync write risk unless `CLOSE`/`FREE` ran).

## 3. Lookups, Cursors, Arrays & Variables
* **`lookup alias = Table@ k=... i=X -flags` vs `lookup -`:**
  * Aliased lookup reads another table (`Table@` = unqualified, `Table@qual` = qualified, `("Table@"{Var)` = runtime dynamic qualifier). Never assume composite key fields without `indexes.json`/`dxmaint` confirmation or verifying explicit `k ne ...` key-match checks.
  * `lookup -` moves the **current table cursor** (it is not an external table lookup).
  * Flags: `-nx` exact match, `-ng` greater-than-or-equal (`getnext`), `-nl` less-than-or-equal (`getprev`), `-o` partial match, `-p` record lock, `-w` wait/fail, `-e` fail on error. Always check `if: not alias` immediately after lookup.
* **Arrays & Declarations:**
  * `dim arr(25):61` and `dim BOed2(25):org(331)` overlay array elements sequentially across real map fields starting at field `61` / `331`.
  * `declare extern var` reads caller state; `declare global var(len,edit)` persists across `CALL` and `AUTOMATIC`. Dummy variables (`aa`–`zz`) are 2 chars unless `declare`d.

## 4. Edit Precedence (`Custom_Edits`)
* Resolution order (`fPManual.pdf` p. 641–644): **`<table_dir>/edits` local > `PFGLOB` (`/appl/fp/lib/edits`) global > 38 built-in system edits** (`*`, `.N`, `N,N`, `mdy`/`dmy`/`ymd`/`mdyy/`, `hm`/`hms`, `yesno`, `allup`/`alllow`/`uplow`, `rj`/`lj`/`cj`/`0rj`, `lowup`, `f`, `parneg`, `unparen`, `ssnum`, `sex`, `blob`, `memo`).

## 5. Worked FAIT Anchors
* `Library/prc.Pricing:L021–L027` (`declare extern ProductCode...`; paired `dim pr2((6,*)(3,.0)...):ta` where odd indices = prices and even = qty breaks).
* `Invoice/prc.inputI:L0085–L0097` (`dim stkp(25):61`, `dim SQLID(25):421`, `dim Flag(60)(8,HMS,G)` timer array).
* `Invoice/prc.inputI:L1060` (`lookup inv = ("Inventory@"{Facility)` dynamic qualifier lookup into `Inventory`).
