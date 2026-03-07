# Project Archaeology Prompt for Claude Code — v3.0

> **How to use:** Copy everything below the line into Claude Code when you're inside any project directory. Replace `[PROJECT_NAME]` with the actual name. Claude Code will scan the entire codebase and produce a detailed Markdown audit document.

---

## The Prompt

```
I need you to perform a complete "Project Archaeology" — a deep, line-by-line audit of this entire project and produce a single comprehensive Markdown document that captures everything someone would need to understand, maintain, and rebuild this tool from scratch.

## Who I Am

I am a non-coder professional (CA/GST consultant) who built this tool through AI-assisted conversations over time. The original chat history is lost. I need you to reverse-engineer not just WHAT the code does, but WHY certain decisions were made — infer the reasoning from the code patterns, comments, variable names, and structure.

## Your Process

1. First, scan the entire project directory tree — every file, every folder, **including hidden directories and dotfiles** (`.claude/`, `.github/`, `.vscode/`, `.env`, `.gitignore`, etc.). These reveal development tooling, AI-assisted development patterns, and environment assumptions.
2. Read every source file line by line. Do not skip or skim.
3. **CRITICAL: Do NOT assume what a function, method, or variable does based on its name alone.** A function called `validate_gstin()` might do far more (or far less) than validation. Read the actual implementation — every line, every condition, every return path. Document what the code ACTUALLY does, not what its name suggests it does. Names can be misleading, incomplete, or outdated from earlier versions. The code itself is the only source of truth.
4. **CRITICAL: When you encounter shared state — files, caches, configs, temp files, environment variables — trace BOTH the WRITE path and the READ path across every file that touches that state.** Confirm they are consistent: same file paths, same formats, same keys, same structure. Mismatches between the code that writes data and the code that reads it back are among the most common bugs in multi-file projects. Do not assume two files agree on a path or format — verify by reading both.
5. For configuration files (requirements.txt, package.json, .env, etc.), note every dependency and its likely purpose.
6. For data files (JSON, CSV, Excel templates), note their structure and role.
7. Look for comments, docstrings, TODOs, and FIXMEs — these are clues to original intent. But verify comments against actual code behaviour — comments can be stale or wrong.
8. Identify patterns — repeated validation logic, error handling approaches, naming conventions — these reveal design philosophy. **For every pattern you identify, verify it is applied consistently across ALL files. If File A handles a platform-specific import with a graceful fallback (try/except) but File B imports the same module unconditionally, flag the inconsistency — these are often bugs or incomplete work, not intentional choices.**
9. **Run `git log --oneline --all` to get the full commit list, then run `git show <commit_hash>` for EVERY SINGLE COMMIT — no exceptions.** Read every diff, every line added, every line removed, every file touched. Do not summarize from commit messages alone — commit messages can be vague or misleading. The actual diff is the truth. The git history is the chronological diary of this project's entire life — it reveals the order in which features were built, why files were restructured, what bugs were discovered in real use, which approaches were tried and abandoned, and most importantly, the REASONING behind every change. This step is not optional and not skimmable. Every commit matters.

## Output Structure

Produce a single Markdown file called `PROJECT_AUDIT_[PROJECT_NAME].md` with the following sections. Be thorough. Write in plain English that a non-programmer can understand. Where technical terms are unavoidable, add a brief parenthetical explanation.

---

### SECTION 1: EXECUTIVE SUMMARY

Write 5-8 sentences covering:
- What this tool does in plain language
- Who it's built for and what problem it solves
- The core technology stack (e.g., "Built with Python, uses PyQt5 for the desktop interface, openpyxl for Excel file handling")
- Current state: does this appear complete, work-in-progress, or abandoned?

### SECTION 2: PROJECT STRUCTURE MAP

- Full directory tree with annotations explaining what each folder and file does
- **Include hidden directories and dotfiles** (`.claude/`, `.github/`, `.vscode/`, `.env`, `.gitignore`, etc.). Tag them as [TOOLING] or [CONFIG] — they reveal development context and AI-assisted patterns.
- Mark files as: [CORE] (essential logic), [UI] (interface), [CONFIG] (settings), [DATA] (templates/samples), [UTIL] (helper/utility), [TEST] (testing), [DOCS] (documentation), [TOOLING] (development tools/IDE/CI), [GENERATED] (auto-created, safe to delete)
- Identify the entry point — which file do you run to start the application?
- **Identify dead code at every level**: dead files (never imported), dead functions/methods (defined but never called from anywhere — verify by searching for every call site), dead variables or constants (defined but never referenced). A function that only appears in its own definition is dead. Do not stop at file-level analysis.

### SECTION 3: FEATURE INVENTORY

For EACH distinct feature the tool provides, document:
- **Feature name**: A clear, descriptive name
- **What it does**: Plain English explanation
- **Where it lives**: Which files/functions implement this
- **Input → Output**: What goes in, what comes out
- **User interaction**: How does a user trigger or use this feature (button click, file selection, automatic, etc.)
- **Concrete trace**: For each feature, walk through ONE realistic example with specific data. Pick a concrete input (e.g., "user selects a folder with 3 Excel files — 2 valid, 1 with a missing GSTIN column") and trace it through every function call, every condition, every loop. Document what the output would actually be. This catches bugs that reading code "in general" misses. If the traced output differs from what the feature description claims, flag it as a potential bug.

Number each feature (F1, F2, F3...) for easy reference in later sections.

### SECTION 4: DATA FLOW

Trace the complete journey of data through the application:
- Where does data enter? (file upload, user input, API call, clipboard, etc.)
- What transformations happen at each step?
- Where does data exit? (file saved, displayed on screen, copied to clipboard, sent somewhere, etc.)
- Draw this as a simple text-based flow diagram using arrows (→)

### SECTION 5: BUSINESS RULES AND DOMAIN LOGIC

This is the most critical section for rebuilding. Document every business rule embedded in the code:
- Validation rules (e.g., "GSTIN must be exactly 15 characters", "tax rate must be one of: 0, 5, 12, 18, 28")
- Calculation logic (e.g., "IGST applies when supplier state ≠ recipient state")
- Formatting rules (e.g., "invoice numbers are zero-padded to 6 digits")
- Conditional logic (e.g., "if return type is B2B, include GSTIN; if B2C, use state code instead")
- Thresholds and limits (e.g., "B2C invoices above ₹2.5 lakh go in B2CL table")
- Any hardcoded values, constants, or lookup tables — **including operational constants** (retry counts, timeout values, file size limits, temp file paths, etc.) that affect runtime behaviour

For each rule, note the exact file and line number where it's implemented.

### SECTION 6: EDGE CASES AND DEFENSIVE CODE

List every edge case the code explicitly handles:
- What happens with empty files?
- What happens with missing columns or unexpected data formats?
- What happens with special characters, Unicode, or encoding issues?
- What happens with very large files or very small datasets?
- What happens with duplicate entries?
- What happens with null/blank/zero values?
- Any try-except blocks — what errors are caught and how are they handled?

Rate the defensive coding as: ROBUST (handles most edge cases), MODERATE (handles common cases), or FRAGILE (minimal error handling, likely to break with unexpected input).

### SECTION 7: EDGE CASES NOT HANDLED (GAPS AND VULNERABILITIES)

Equally important — what does the code NOT handle that it probably should?
- Missing validations that could cause silent data corruption
- Error conditions that would crash the application
- Input variations that real-world users might provide but aren't accounted for
- Performance bottlenecks with large datasets
- Security concerns (hardcoded passwords, unvalidated file paths, etc.)
- Accessibility or usability gaps in the interface
- **Silent logic bugs**: Any code that runs without errors but would produce incorrect output — discovered during the concrete traces in Section 3 or during the shared-state verification in Process Step 4. Include mismatched file paths between writer and reader, wrong data types being compared, and any place where the code's actual output differs from its apparent intent.

### SECTION 8: DESIGN DECISIONS (INFERRED)

Based on code patterns, infer and document the likely reasoning behind key decisions:
- Why was this framework/library chosen over alternatives?
- Why is the code structured this way (modular vs monolithic)?
- Why are certain features implemented in a complex way when simpler options exist? (Often there's a domain-specific reason)
- Any evident iterations — places where code was clearly refactored, patched, or evolved from an earlier approach
- Commented-out code blocks — what do they suggest about abandoned approaches?
- **Pattern consistency**: For every design pattern you identify (error handling style, import guards, config access method), verify it is applied consistently across ALL files. Flag any file that deviates — note what the established pattern is, which files follow it, and which files break it.

Mark each inference with a confidence level: HIGH (clear from code), MEDIUM (reasonable inference), or LOW (speculation based on limited evidence).

### SECTION 9: DEPENDENCIES AND ENVIRONMENT

- Complete list of external libraries with version numbers and purpose
- Python version requirements (if applicable)
- OS-specific dependencies or behaviours
- File system assumptions (paths, folder structures, network drives)
- Any external services, APIs, or tools the application depends on
- Setup steps needed to run this on a fresh machine
- **For OS-specific modules** (e.g., `pywin32`, `win32com`): Note which files import them and whether the import uses a graceful fallback (try/except with a helpful error message) or would crash on an unsupported platform. Flag any inconsistency — if one file handles it gracefully but another doesn't, that's a gap.
- **Dependencies used in code but missing from requirements.txt** (e.g., `import win32com` used but `pywin32` absent from requirements) — flag these as documentation gaps that would break setup on a fresh machine.

### SECTION 10: UI/INTERFACE DOCUMENTATION

If the tool has a user interface:
- Describe every screen, window, or dialog
- Document every button, input field, dropdown, checkbox — what it does and what triggers it
- Note the layout approach and any styling choices
- Identify the user workflow — the typical sequence of actions a user performs
- Screenshot descriptions (describe what each screen looks like since you can't take actual screenshots)

### SECTION 11: RECONSTRUCTION BLUEPRINT

This is the actionable section — a step-by-step plan for rebuilding this tool from scratch:

1. **Foundation**: What to set up first (environment, dependencies, project structure)
2. **Core Logic**: Build order for features (which depends on which)
3. **Feature Priority**: Rank features by importance — what's essential vs nice-to-have
4. **Known Improvements**: Based on gaps identified in Section 7, what should be done differently in a rebuild
5. **Estimated Complexity**: Rate each feature as SIMPLE (< 1 hour), MODERATE (1-3 hours), or COMPLEX (3+ hours) for AI-assisted rebuilding
6. **Suggested Skill**: For each major feature, suggest what a Claude Code Skill (SKILL.md) could encode to make this pattern reusable across future projects

> **CONSTRAINT FOR ITEM 4**: Before recommending any alternative approach or technology, check Section 8. If Section 8 explains why the current approach was chosen over the alternative you are about to recommend, do NOT recommend that alternative — you would be reintroducing the exact problem the original builder already solved. Instead, recommend a THIRD option that addresses the weakness without reintroducing the known problem. If no third option exists, state the trade-off honestly rather than recommending a known-bad alternative.

### SECTION 12: CHANGELOG ARCHAEOLOGY (FROM GIT HISTORY)

This section must be built from reading EVERY commit diff — not summaries, not samples, not just "important" ones. Every commit exists because a decision was made. Your job is to extract that decision.

**12A. Complete Development Timeline**

For EVERY commit, chronologically, document:
- **Commit hash and date**
- **What changed**: Files touched, lines added/removed — describe the actual code changes in plain English
- **Why it changed**: Infer the reasoning — was this a new feature, a bug fix, a refactor, a dependency update, a response to a real-world problem?
- **What it reveals**: What does this change tell us about the project's rules, logic, data flow, edge cases, or design philosophy?

Group commits into natural development phases as they emerge (e.g., "Phase 1: Initial scaffold and core data model", "Phase 2: Validation logic", "Phase 3: UI development", "Phase 4: Real-world bug fixes", etc.).

**12B. File Evolution Map**

Using the git history, trace how each core file evolved over time:
- When was it created? What was its original purpose?
- How did it change across commits? Did its role expand, shrink, or shift?
- Was it ever renamed, split into multiple files, or merged from other files?
- This reveals the architecture's evolution — how the project grew from a simple script into a structured application.

> **VERIFICATION**: If you describe any file as "unchanged since creation," verify this against the current code. If that file imports something added in a later commit, it was changed — update the map accordingly.

**12C. Logic and Rule Evolution**

Track how business rules, validation logic, and calculations changed across commits:
- Were rules added incrementally (suggesting they were discovered during real-world testing)?
- Were rules modified (suggesting the original understanding was wrong or regulations changed)?
- Were rules removed (suggesting they became obsolete or were replaced)?
- This is critical for rebuilding — it shows which rules were foundational vs. which were discovered through trial and error.

**12D. Edge Cases Discovered in Production**

Bug-fix commits are gold. For every commit that fixes a bug or handles a new edge case:
- What broke? What input or scenario caused the failure?
- How was it fixed? Was it a patch or a proper solution?
- Does the fix suggest other similar edge cases that might still be unhandled?

**12E. Abandoned Approaches and Reversals**

Identify commits where:
- Code was added and later removed or replaced in a subsequent commit
- A different library or approach was swapped in
- A feature was started but never completed (partial implementation still in codebase)
- **Dead methods or commented-out code** still sitting in the current codebase as remnants of earlier approaches
- Document what was tried, why it likely failed, and what replaced it — these are expensive lessons that should not be repeated in a rebuild.

**12F. Dependency Evolution**

Track changes to requirements.txt, package.json, or any dependency files:
- When were dependencies added? Why? (Correlate with feature commits)
- Were any dependencies removed or replaced? What triggered the switch?
- Version upgrades — were they deliberate (to fix something) or routine?
- **Dependencies used in code but missing from dependency files** — flag these as setup gaps.

**If no git history exists**: State this clearly and fall back to: version numbers or date stamps in code/comments, TODO/FIXME comments suggesting planned work, multiple approaches to the same problem (suggesting iteration), commented-out code blocks, and any README, HANDOVER.md, or documentation files.

---

## Self-Consistency Review (MANDATORY FINAL STEP)

**After completing all 12 sections, re-read the entire document from top to bottom and verify:**

1. **No cross-section contradictions**: If Section 3 describes a feature as working but Section 7 or a concrete trace reveals it is broken, update Section 3 and move the issue to Section 7.
2. **Section 11 respects Section 8**: If Section 8 explains why a particular approach was chosen, Section 11 must not recommend reverting to the rejected alternative.
3. **File evolution claims match reality**: If Section 12B says a file was "stable since creation" but Sections 3-5 show it using something added later, correct the claim.
4. **Dead code claims are accurate**: If Section 2 says "no dead code" but analysis in other sections found dead methods or unused constants, update Section 2.
5. **All concrete traces completed**: Every feature in Section 3 has been verified through one realistic example, not just described from reading function signatures.
6. **All shared state verified**: Every config file, temp file, or shared resource identified in Process Step 4 has had its write and read paths compared.

If any correction is needed, fix it in place and add a note: `[Corrected during self-review — originally stated X, but Section Y revealed Z]`.

---

## Formatting Rules

- Use clear Markdown headers (##, ###) for sections
- Use tables for structured comparisons
- Use code blocks with syntax highlighting for any code references
- Use > blockquotes for important warnings or notes
- Cross-reference sections (e.g., "See Feature F3 in Section 3")
- Keep language accessible — explain technical terms in parentheses on first use
- Be specific — "Line 45 of main.py" not "somewhere in the main file"

## Final Instruction

Save the completed audit as `PROJECT_AUDIT_[PROJECT_NAME].md` in the project root directory. After saving, give me a brief verbal summary of the three most important findings — things I'd want to know first before diving into the full document.
```
