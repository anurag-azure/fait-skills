---
name: filepro-screen-analysis
description: Decodes binary screen.* text extractions, *N / !xx tokens, interactive @wef/@wlf/@wbl/@wuk handlers, POPUP UPDATE screens, KT video screen frames, and Slide 4 multi-table screen flows.
version: 2.0.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `filepro-screen-analysis`

Use this skill as a **separate specialist lens** whenever a module contains `screen.*` files (`evidence-pack/tables/<Table>/screens.md`), screen-field triggers (`@wef`/`@wlf`/`@wbl`/`@wuk`), or KT screenshots. Load ONLY when the module scope includes screen artifacts or UI flows.

## 1. Reading `screen.*` Artifacts Accurately
* `screens.md` extracts printable ASCII runs (`>=4` chars) from binary `screen.*` files in stored order and resolves tokens:
  * **`*N`** = real table field `N` (mapped to `dictionary.json`).
  * **`!xx`** = dummy screen field or declared variable `xx` (distinct from `*xx` used in `out.*` reports!).
* **Binary coordinate limitation:** Exact screen `(row, col)` coordinates, ANSI colors, field cursor tab order, and screen-specific protected-field flags are **not** inside `screens.md`. **Never invent screen geometry.**

## 2. Correlating Screen Layout & Interactive CSR Journeys
Recover operator workflows strictly by fusing three verifiable sources:
1. **`screens.md` token inventory** (e.g., `63` Invoice variants including `screen.I0`, `screen.blk`, `screen.pri`, `18` `Prices` screens, `9` `PricesTable` screens, `34` `Members` screens, `2` `Exceptions` screens).
2. **`prc.*` interactive UI events:**
   * `@wef<fld>` (when entering field), `@wlf<fld>` (when leaving field), `@wbl<fld>` (when leaving browse), `@wuk<fld>` (function/special keys), `@key<X>` (key shortcut).
   * `POPUP UPDATE` / `SHOW` / `MSGBOX` / `ERRORBOX` / `PUSHKEY` statements (`Invoice/prc.inputI:L1428` etc.).
   * Distinguish `@pz=""` interactive behavior from `@pz ne ""` unattended `-rz` automation (`PushKey "[ENTR]"`, `L2622`).
3. **Curated Visual & Architect Context:**
   * Curated non-PHI KT frames cited strictly as `KT@<mm:ss>#frame` (`kt-session/scenes/`).
   * Architect Slide 4 UI navigation baseline: **`Prices` (`18` screens) -> Press `P` -> `PricesTable` (`9` screens) -> `Lookup` `Vendors`**.
   * When referenced screens are driven by missing processes (e.g., `Invoice/prc.input`, `Invoice/ChgPrice`), record the UI behavior as `pending missing process/live screen walkthrough`.
4. **Complete Screen Coverage (Zero Silent Omissions):** Document **every** screen listed in `evidence-pack/tables/<Table>/screens.md` for all in-scope tables (e.g., all `18` `Prices` screens + `9` `PricesTable` screens) with its extracted `*N`/`!xx` field tokens, `@qu` qualifier usage, browse grid tokens (`!zz`, `!ba`), function keys (`F6`, `F10`, Press `P`), and print/date utility menus. Use a Node assembly script if the full screen inventory exceeds `12 KB` rather than omitting screens.
