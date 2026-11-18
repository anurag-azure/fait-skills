---
name: filepro-menu-navigation
description: Interprets UNIX/Linux filePro user menus (24-slot menus, rclerk/rreport command lines), @menu triggers, chain/MENU calls, and Bash launch wrappers without confusing menus with table map schemas.
version: 2.0.0
applies_to: [WF-F1, WF-F2]
---

# Skill: `filepro-menu-navigation`

Use this **separate specialist skill** whenever a code drop includes filePro menu files (`menus/*`), Bash launch wrappers invoking `rclerk`/`Irclerk`/`rreport`/`dreport`, `@menu` subroutines, or `MENU` / `CHAIN` routing. Do not load when menu artifacts are absent unless analyzing entry-point dispatch in `prc.*`.

## 1. Real filePro Menu Architecture (`fPManual.pdf` p. 705–708, 859)
* **User Menus vs. `Table/map`:** filePro menus (up to `24` action slots per menu + script menus + help/heading `-H`) execute OS/filePro commands — they are NEVER `Table/map` files!
* **Command Signatures to Decode:**
  * `Irclerk <Table> -s<screen> -z <input_proc> -y <auto_proc>` (e.g., CSR order entry opening `Invoice` on `screen.I0` chaining from `input` → `inputI`).
  * `-rw <arg>` (`@pw`), `-rx <arg>` (`@px`), `-ry <arg>` (`@py`), `-rz <arg>` (`@pz`), `-S <sel_set>`, `-V <sel_proc>`, `-RO`, `-U`, `-BG`.
  * **`@menu` Processing Loops:** Global exit/retry patterns (`MENU` command or exit-flag check on `@menu` return from `fPManual.pdf` p. 859).
  * **`PUSHKEY` / `CHAIN` Transitions:** Automated keystroke redirection (`PushKey "[ENTR]"`, `chain "inputI"`) uniting interactive screens and background runs.

## 2. Fail-Closed Missing Menu Guard
* When menu binaries or `/appl/fp/menus` scripts are absent from the code drop, explicitly state `menu binaries not in drop — entry arguments inferred from Additional Context / KT frames only` rather than reconstructing menu trees speculatively.
