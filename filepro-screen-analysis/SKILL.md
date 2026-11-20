---
name: filepro-screen-analysis
description: Content-aware screen identification guardrail and filePro screen decoder for screen.* and non-screen-prefixed serialized screens, resolving *N, !N, %N, !xx, and !@xx tokens, interactive @wef/@wlf/@wbl/@wuk handlers, POPUP UPDATE screens, and ambiguous-artifact flagging.
version: 3.0.0
applies_to: [WF-F0, WF-F1, WF-F2]
---

# Skill: `filepro-screen-analysis` (v3.0 — Content-Aware Screen Identification Guardrail)

Use this skill in **Data and Screens Analyst**, **Spec Document Assembler**, **CSA Architect**, and **fp-normalizer** auditing.

## 1. Content-Aware Screen Identification Guardrail (Mandatory)

**Never identify screens solely by checking whether filenames start with `screen.`.** Translated-code repositories or legacy drops may serialize screens into `.strings.txt` or alternate filenames (or invoke screens dynamically via `screen "..."`, `switchto "..."`, `popup update ...`).

Apply a generic, multi-signal content-aware classifier across every table directory:

1. **Positive Structural Screen Indicators:**
   - **Valid table-field placeholders:** `*N` (editable), `!N` (display-only), or `%N` (conditional/protected) where `1 <= N <= map.fields.length`, with `>= 2` distinct valid field numbers.
   - **Screen dummy / system tokens:** `!xx` (2-char runtime display/lookup variables such as `!vn`, `!vo`, `!cd`, `!ce`, `!ar`, `!em`) and `!@xx` (`!@qu`, `!@td`, `!@rn`).
   - **Interactive UI markers:** Function-key prompts (`F6`, `F10`, `Press 'P'`), interactive field labels matching `map` labels, or `prc.*` rendering/navigation calls (`screen "<name>"`, `switchto "<name>"`, `popup update <name>`).
   - **Container profile:** Binary/source size consistent with filePro screen templates (~`3,000–4,200` bytes in `strings-manifest.csv` or raw file).
2. **Negative Guards (Preventing False Positives):**
   - Exclude non-screen data/code containers (`map`, `prc.*`, `tok.*`, `index*`, `key*`, `data*`, `lockf*`, `strings-manifest.csv`) whose binary dumps contain incidental `*A`/`!1` byte sequences.
   - Distinguish printer output formats (`out.*` or `hplaser` printer-driver headers with `*xx` print dummies) as **`output-format`** rather than interactive **`screen`**, while still resolving their tokens and documenting them under Reports & Outputs.
3. **Ambiguous & Missing-Screen Flagging:**
   - Record every non-`screen.*` file that triggers positive token/UI signals as `AMBIGUOUS_SCREEN_CANDIDATE` in `screen-guardrail-report.json` for review.
   - Cross-check all `screen`, `switchto`, and `popup update` targets referenced in `prc.*` against detected screen files; flag any referenced screen with no backing file in the drop (e.g. `popup update 1I` referencing `Members/screen.1I` vs `Invoice`) as `REFERENCED_CROSS_TABLE_OR_MISSING` for verification.

## 2. Token Resolution & Editability Classification

Resolve every placeholder against `dictionary.json` in appearance order (zero truncation):
- **`*N`** → Real field `N` (**Editable**)
- **`!N`** → Real field `N` (**Display-only**)
- **`%N`** → Real field `N` (**Conditional / Protected**)
- **`!xx`** → Dummy variable `xx` (**Dummy (runtime)**)
- **`!@xx`** → System variable `@xx` (**System**, e.g. `!@qu` = Current Qualifier)

## 3. Binary Layout Limitation & Naming Hygiene

- Exact 80×24 `(row, col)` cursor coordinates and ANSI colors are stored in undecoded binary headers — **never invent screen geometry**; mark visual layout geometry as `UNKNOWN - Verify via Define Screens export or live KT frames`.
- Use **`Screen Summary`** (never `Context Table Screen Summary`) when summarizing screens of non-primary referenced tables.
