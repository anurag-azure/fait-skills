---
name: filepro-rule-extraction
description: Per-chunk rule extraction, deduplicating consolidation, anomaly/finding detection, and BRE testable-rule rules aligned to PostPOC Slide 13 and evidence-pack schemas.
version: 2.0.0
applies_to: [WF-F1, WF-F4]
---

# Skill: `filepro-rule-extraction`

Use this skill in **Rule Extractor Agent**, **Rule Gap Fixer Agent**, **Rule Consolidator**, and **Findings Analyst**. Every extracted rule feeds directly into the **Stage 1 Code2Spec** and **Slide 13 BRE closed-loop** validation pipeline.

## 1. Per-Chunk Extraction Checklist
Inspect EVERY line of your assigned `*.annotated.txt` chunk in order and extract a candidate rule for every:
1. **`if:` Condition Branch & Negative Path:** Never merge distinct branches or paraphrase away fallback paths (`then:` vs implicit fall-through).
2. **Mode Divergence (`@pz == ""` vs `@pz ne ""`):** Whenever `-rz` unattended batch processing takes a different branch (or skips an expiration check / popup), capture both behaviors explicitly.
3. **Hard-Coded Business Literals:** Specific account numbers (`7500`, `12000`, `55000`), user IDs (`dave`, `aaron`, `root`), ship-to suffixes (`44Z`), date literals, or threshold constants (`25` lines max per `#33` web order).
4. **Lookup Hierarchy, Exact Key Padding & Fallbacks:** Every cascade step (e.g., `Exceptions` 3-key probe → `PLAccount` retry → third-party clear → `PricesTable` redirect → standard group → `"  1"` list), plus **exact character-width key construction & space-padding** (e.g., `ProductCode(7)+AccountNum(6)=13`, `Spaces(7)+AccountNum(6)+Vendor(4)+Spaces(7)=24`), `@qu` qualifier lookup sequence (`PricesTable@OPT` vs unqualified) and missing-table fallback scenarios, **6-step exception validation failure modes** (`useException='N'` vs skip-to-next), `PLAccount` circular-reference / loop prevention (`priceLikeCheck`), `NoExceptions`/`FreeGood` early exit vs group list fallback, and exact `WARNING !!` / `ERRORBOX` / `MSGBOX` literals.
5. **Calculation, Array Semantics & Conditional Overrides:** Fixed (`F`), Cost-Plus (`C`), Percent-of-Cost (`%`), **empty `pr2` array behavior per method** (`F` empty format vs `C` adding `pri(5)` vs `%` yielding `0`), blank-qty tier gates, **array slot semantics** (e.g., `pr2[]` odd price slots vs even descriptor/qty slots), rounding/edit coercion (`doedit`), `CLK` `DateOrderTaken` date overrides (`mem(34/103/128)`) and empty/future/expired date edge cases, **Exception Expiration** (`(@td - exc(28)) <= exc(31)`), **Exclusive Member Exception filtering** (`priceLikeCheck="*"` & `exc(1)="Y"`), **STP statement pricing overrides** (`STPInvoice="Y"`, `exc(17..27)`), and dummy variable calculations (`!cd`, `!ce`, `!lp`, `!rp`, `!co`, `!cc`, `!cq`).
6. **Transaction Boundaries & Deferred Writes:** `FREE` / `WRITE` / `CLOSE` timing, `CALL` / `CHAIN` / `SYSTEM` state mutation, deferred `Push_2_SQL` (`Invoice.44`), `2BPrtd` queuing.
7. **Dead Code / Commented-Out Blocks:** Cite them accurately as `no-rule` or findings, never as active production rules.

## 2. Consolidation, Taxonomy & Findings Rules
* **Stable IDs:** Assign sequential IDs `R-<MOD>-001` (`Pricing`: `R-PRC-001`..; `Invoice`: `R-INV-001`..) grouped by business capability, preserving all chunk evidence refs (`ref` + verbatim `quote`).
* **Categories (`rule.schema.json`):** `validation | calculation | selection | defaulting | routing | authorization | integration | state-change`.
* **Findings (`finding.schema.json`):** Surface genuine code anomalies (`likely-bug`, `risk`, `question`, `info`) across **any** module: `mode_divergence`, `early_exit_bypass`, `dead_code`, `hardcoded_value`, `flag_overloading`, `missing_lock_or_flush`, `data_quality`, `naming_mismatch`. Do NOT search only for Pricing `F1–F6`.
* **Anti-Patterns (Forbidden):** Never cite a line number outside your chunk without verifying; never copy unmasked data; never omit a label inside your chunk scope (`Rule Coverage Gate` rejects uncovered labels).
