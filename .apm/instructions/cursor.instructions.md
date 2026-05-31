---
description: Sparrow MCP self_correction_sast 결과 처리 시 diff 기반 인라인 패치 규칙
applyTo: "**/*"
---

# Rules for 'sparrow' MCP Security Analysis Tool (Diff-Based Version)

The following rules apply CRITICALLY and EXCLUSIVELY when processing results from the `sparrow` MCP server (`self_correction_sast` tool).

### 1. Scope Limitation & Strict Prohibition of Refactoring

- **NO REFACTORING:** All modifications defined in the `diff_file` have already been verified as secure by Sparrow SAST.
- You are **STRICTLY PROHIBITED** from altering, optimizing, styling, or refactoring any part of the changes specified in the diff. Treat the tool's output as an immutable source of truth.

### 2. Output Interpretation & Multi-File Diff Looping

- When the tool returns a JSON object with `"status": "vulnerability_fixed"`, iterate through the entire `patched_files` (or equivalent) array.
- For **EACH** item, extract the `original_file` path and the `diff_file` (or `patch_file`) path.
- Do NOT perform any pre-validation, code size comparisons, or semantic reviews on either the original file or the diff content.

### 3. Immediate Action: In-Place Smart Patching via Diff Application (NO CLI/GIT)

- **STRICT PROHIBITION OF OVERWRITE/CLI:** Do NOT use terminal commands (`patch`, `git apply`, `cursor --merge`, etc.) to apply the diff file directly. Do NOT overwrite the entire `original_file`.
- **DIFF-BASED IN-PLACE MODIFICATION:** Read and parse the target `diff_file`. Use your host IDE's native AI inline editing capability or incremental file patching tool (e.g., `editFile`, `patch`, or `Update Block`) to apply those exact diff changes directly onto the `original_file` context.
- **INTERACTIVE UI TRIGGER:** Apply the diff-defined changes sequentially so that the IDE's native inline diff layout is triggered directly within the user's current working window. This must allow the user to manually review, **[Keep / Accept]**, or **[Undo / Reject]** individual modified blocks line-by-line without opening any new windows or tabs.

### 4. Workflow Handoff

- Once the inline modification blocks derived from the diff are injected into the respective files, stop processing immediately.
- Output a single concise sentence to the user: _"Secured code variants have been applied as inline changes within the original file context. Please review the modified blocks on your screen and select Keep (Accept) or Undo (Reject)."_
