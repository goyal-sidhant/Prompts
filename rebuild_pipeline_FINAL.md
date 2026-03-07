# The Complete Rebuild Pipeline — FINAL VERSION

> **When to use this:** You have an existing tool that you want to rebuild
> from scratch with new perspective. You'll first audit the old code, then
> make decisions about what to keep/change/drop, then build fresh.
>
> This document covers:
> 1. What documents you prepare BEFORE giving the rebuild prompt
> 2. The Rebuild Prompt itself — what goes in it
> 3. The Structured Prompting Style for mid-build changes

---

# PART 1: DOCUMENTS YOU PREPARE BEFORE THE BUILD

You will provide Claude Code with **four documents** when starting a rebuild.
Three you prepare upfront, one grows automatically during the build.

---

## Document 1: The Audit Report (already done)

**File:** `.build-docs/PROJECT_AUDIT_[PROJECT_NAME].md`
**Created by:** The Project Archaeology prompt (v3)
**Your role:** Read it carefully. Highlight or note sections you agree with,
disagree with, or want to change. You don't modify this file — it's your
reference material.

---

## Document 2: The Rebuild Brief (YOU write this)

**File:** `.build-docs/REBUILD_BRIEF_[PROJECT_NAME].md`
**Created by:** You, after reading the audit
**Purpose:** This is your decision document. It tells Claude Code what to keep,
what to change, what to drop, and what to add new.

**Reading guide for the audit:** Start with Section 1 (Executive Summary) for
the overview. Then go directly to Section 7 (Gaps) — that's where the rebuild
value is. Then read Section 5 (Business Rules) to verify your domain knowledge.
Then Section 11 (Reconstruction Blueprint) for the suggested build order.
Skim the rest as needed.

### Template:

```markdown
# Rebuild Brief: [PROJECT_NAME]
# Date: [TODAY'S DATE]
# Author: Adv. Sidhant Goyal

---

## 1. WHAT THIS TOOL DOES (in my own words)

[Write 3-4 sentences describing the tool's purpose. Don't copy from the audit —
restate it in YOUR understanding. If your understanding differs from the audit's
description, that gap itself is valuable information.]

---

## 2. WHO USES THIS AND HOW

- **Primary user:** [e.g., "Me and my team at Goyal Tax Services"]
- **Technical comfort:** [e.g., "Non-coders. Must work with simple clicks, no terminal commands."]
- **Frequency of use:** [e.g., "Daily during filing season, monthly otherwise"]
- **Environment:** [e.g., "Windows 10/11 desktops, files on local drives and sometimes network server"]

---

## 3. FEATURE DECISIONS

For each feature from the audit (Section 3), mark your decision:

| Feature | Audit Ref | Decision | Notes |
|---------|-----------|----------|-------|
| [Feature name] | F1 | KEEP AS-IS | [Why — what works well] |
| [Feature name] | F2 | KEEP BUT IMPROVE | [What to change and why] |
| [Feature name] | F3 | DROP | [Why — no longer needed or was never useful] |
| [Feature name] | F4 | REBUILD DIFFERENTLY | [What was wrong and what you want instead] |

---

## 4. NEW FEATURES TO ADD

Features that didn't exist in the original but you want in the rebuild:

| New Feature | What it should do | Why I need it | Priority |
|-------------|-------------------|---------------|----------|
| [Name] | [Plain English description] | [The problem it solves] | MUST HAVE / NICE TO HAVE |

---

## 5. BUSINESS RULES — KEEP / CHANGE / ADD

Reference audit Section 5. For each rule:

### Rules to KEEP exactly as-is:
- [Rule from audit] — (Audit: file.py, line XX)

### Rules to CHANGE:
- **Old rule:** [What the audit found]
- **New rule:** [What it should be now]
- **Why:** [Regulation changed / original was wrong / better understanding now]

### Rules to ADD (not in original):
- [New rule and why it's needed]

---

## 6. EDGE CASES — LESSONS FROM THE AUDIT

Reference audit Sections 6 and 7.

### Must handle (were handled in original — keep):
- [Edge case from audit Section 6]

### Must handle (were NOT handled — gaps from Section 7 that bit you):
- [Gap that caused real problems]

### Not worth handling (over-engineering for my use case):
- [Edge case that's theoretically possible but practically irrelevant]

---

## 7. DESIGN PREFERENCES

- **Framework:** [e.g., "PyQt5 again" or "Want to try something simpler"]
- **Interface style:** [e.g., "Single window, big buttons, minimal tabs"]
- **Output format:** [e.g., "Excel (.xlsx) with formatting, not just raw CSV"]
- **File handling:** [e.g., "Drag-and-drop preferred over file browse dialogs"]
- **Error display:** [e.g., "Show errors in the main window, not pop-up dialogs"]

---

## 8. WHAT I LEARNED (from reading the audit)

Write 3-5 sentences about what surprised you, what you'd do differently,
and what principles you want the rebuild to follow. This is your "new
perspective" section.

---

## 9. PARKING LOT

[Leave empty. Ideas that pop up during the build go here.]

| Idea | When it came up | Priority | Status |
|------|----------------|----------|--------|
| | | | |

---
```

---

## Document 3: CLAUDE.md (project memory and coding standards)

**File:** `CLAUDE.md` (placed in the project root — MUST be root, not a subfolder)
**Created by:** You, before starting the build
**Purpose:** Claude Code reads this automatically at the start of every session.
It contains your identity, project context, coding standards, and documentation rules.

### Template:

```markdown
# Project: [PROJECT_NAME] — Rebuild

---

## About Me
I am a CA/GST consultant, not a programmer. I build tools through
conversation with AI. Explain technical decisions in plain English.
Ask before making architectural choices. Never assume I understand
technical jargon — explain it on first use.

## Tech Stack
- Python 3.x
- PyQt5 for desktop interface
- openpyxl for Excel handling
- [any other libraries you've decided on]

## Reference Documents
- `.build-docs/PROJECT_AUDIT_[name].md` — Full audit of the original tool
- `.build-docs/REBUILD_BRIEF_[name].md` — My decisions for the rebuild
- `.build-docs/DECISIONS_LOG.md` — Running log of every discussion and decision
- `.build-docs/context/` — Additional reference files (samples, templates, specs)

## Current Phase
Phase 1: [Update this as you progress]

---

# CODING STANDARDS — MANDATORY FOR ALL CODE

These standards apply to EVERY file and EVERY function in this project.
No exceptions. No shortcuts. These exist so that a non-programmer can
read the code six months later and understand what everything does,
why it exists, how it connects to everything else, and what assumptions
were made.

---

## Standard 1: Modular Folder Structure From Day One

NEVER build in a single file. NEVER start flat and split later.
Always use folder-based structure from the very first line of code:

```
my-tool/
├── main.py                      ← Entry point ONLY. Starts the app.
│
├── config/                      ← All settings, constants, mappings
│   ├── __init__.py
│   ├── settings.py              ← Paths, thresholds, environment config
│   ├── constants.py             ← Fixed values (tax rates, state codes, limits)
│   └── mappings.py              ← Lookup tables (state code → name, HSN → rate)
│
├── core/                        ← Business logic, one file per domain area
│   ├── __init__.py
│   ├── gstin_validator.py
│   ├── tax_calculator.py
│   ├── invoice_validator.py
│   └── return_classifier.py
│
├── models/                      ← Data structures / object definitions
│   ├── __init__.py
│   ├── invoice.py               ← What an Invoice object looks like
│   ├── error_entry.py           ← What an Error record looks like
│   └── client.py                ← What a Client record looks like
│
├── readers/                     ← Input handling, one file per format
│   ├── __init__.py
│   ├── excel_reader.py
│   ├── csv_reader.py
│   └── json_reader.py
│
├── writers/                     ← Output generation, one file per type
│   ├── __init__.py
│   ├── excel_writer.py
│   ├── report_writer.py
│   └── json_writer.py
│
├── gui/                         ← Interface, one file per screen/dialog
│   ├── __init__.py
│   ├── main_window.py
│   ├── settings_dialog.py
│   ├── error_viewer.py
│   └── progress_bar.py
│
└── utils/                       ← Shared helpers, split by concern
    ├── __init__.py
    ├── file_helpers.py
    └── format_helpers.py
```

Not every project needs every folder. Propose only what's relevant.
But NEVER combine responsibilities into one file.

Each file does ONE thing. If you can't describe its purpose in one
sentence, split it. Before creating any new file or reorganising
the structure, tell me and get approval.

---

## Standard 2: Naming Conventions

### Files and Folders
- All lowercase with underscores: `gstin_validator.py`, `excel_reader.py`
- Folder names short and descriptive: `core/`, `gui/`, `config/`
- NO abbreviations unless universally known: `gst` is fine, `inv_val` is not
  (use `invoice_validator` instead)

### Functions and Variables
- All lowercase with underscores (snake_case):
  `validate_gstin()`, `tax_rate`, `input_file_path`
- Function names start with a VERB: `validate_`, `calculate_`, `read_`,
  `write_`, `export_`, `parse_`, `format_`, `check_`, `get_`, `set_`
- Boolean variables start with `is_` or `has_`:
  `is_valid`, `has_errors`, `is_interstate`
- NEVER use single-letter variables except `i`, `j` in short loops
- NEVER use vague names: `data`, `result`, `temp`, `info`, `stuff`
  Instead: `invoice_data`, `validation_result`, `temp_file_path`

### Classes
- PascalCase (capitalise each word): `InvoiceValidator`, `ExcelReader`,
  `MainWindow`, `GSTINResult`

### Constants
- ALL_UPPERCASE with underscores: `MAX_FILE_SIZE`, `GST_RATES`,
  `STATE_CODES`, `B2CL_THRESHOLD`
- ALL constants live in `config/constants.py` — NEVER hardcode values
  directly in logic files

---

## Standard 3: File-Level Documentation

Every Python file must start with this header block:

```python
"""
FILE: core/gstin_validator.py

PURPOSE: Validates GSTIN (Goods and Services Tax Identification Number)
         format, structure, and check digit.

CONTAINS:
- validate_gstin()     — Main validation, returns valid/invalid with details
- extract_components() — Breaks GSTIN into state, PAN, entity, check digit
- verify_checksum()    — Runs the government-specified check digit algorithm

DEPENDS ON:
- config/constants.py  → STATE_CODES, GSTIN_LENGTH, CHECKSUM_CHARS
- utils/format_helpers.py → clean_string()

USED BY:
- core/invoice_validator.py → calls validate_gstin() for each invoice row
- gui/error_viewer.py → displays validation error messages

CHANGE LOG:
| Date       | Change                                    | Why                              |
|------------|-------------------------------------------|----------------------------------|
| DD-MM-YYYY | Created — initial validation rules        | Section 25 CGST Act compliance   |
| DD-MM-YYYY | Added old state code "00" handling         | Client data contained code "00"  |
| DD-MM-YYYY | Added composition taxpayer identification  | Needed for GSTR-4 projects       |
"""
```

---

## Standard 4: Function-Level Documentation

Every function MUST have a documentation block with ALL of these fields:

```python
def validate_gstin(gstin_string, strict_mode=False):
    """
    WHAT:
        Validates a GSTIN string against format rules and checksum.
        Returns a result dict with valid/invalid status, error details,
        and extracted components (state, PAN, entity type).

    WHY ADDED:
        Clients frequently submit invoices with malformed GSTINs —
        wrong length, invalid state codes, typos in PAN portion.
        This catches errors before they reach the GST Portal upload.
        Legal basis: Section 25 CGST Act, Rule 10 CGST Rules.

    CALLED BY:
        → core/invoice_validator.py → validate_invoice_row()
        → gui/main_window.py → on_single_gstin_check()

    CALLS:
        → config/constants.py → STATE_CODES, GSTIN_LENGTH
        → utils/format_helpers.py → clean_string()
        → self → verify_checksum() (private helper in this file)

    EDGE CASES HANDLED:
        - None or empty input → returns invalid, message: "GSTIN is empty"
        - Leading/trailing spaces → auto-trimmed before validation
        - Lowercase input → auto-converted to uppercase
        - Old state code "00" → flagged: "use state-specific code"
        - Valid format but wrong check digit → specific error message
        - Non-string input (int, float) → converted to string first

    ASSUMPTIONS:
        - State code list is current as of [date]. New codes require
          updating config/constants.py → STATE_CODES
        - Check digit algorithm follows government specification v1.0.
          If algorithm changes, this function needs manual update.
        *** ASSUMPTION: strict_mode defaults to False because most
            client data has minor formatting issues that should be
            auto-corrected, not rejected. Decided in Session 2. ***

    PARAMETERS:
        gstin_string (str): The GSTIN to validate. May contain spaces,
                            lowercase, or other formatting issues.
        strict_mode (bool): If True, rejects auto-correctable issues
                            instead of fixing them. Default: False.

    RETURNS:
        dict: {
            "valid": bool,          — True if GSTIN passes all checks
            "error": str or None,   — Error message if invalid
            "original": str,        — Input as received
            "cleaned": str,         — Input after auto-correction
            "state_code": str,      — First 2 characters
            "state_name": str,      — Full state name from lookup
            "pan": str,             — Characters 3-12
            "entity_code": str,     — Character 13
            "entity_type": str,     — "Regular"/"Composition"/"Government"/etc.
            "check_digit": str      — Last character
        }
    """
```

For small helper functions (under 5 lines), a shortened format is acceptable:

```python
def clean_string(text):
    """
    WHAT: Strips whitespace and converts to uppercase.
    CALLED BY: core/gstin_validator.py, core/invoice_validator.py
    RETURNS: Cleaned string, or empty string if input is None.
    """
    if text is None:
        return ""
    return str(text).strip().upper()
```

---

## Standard 5: In-Line Code Comments

Comments inside the code follow these rules:

```python
# GOOD — explains WHY (not obvious from the code)
# GST Portal rejects amounts with more than 2 decimal places
amount = round(amount, 2)

# GOOD — cites business rule
# B2CL threshold: Section 37 read with Table 5 of GSTR-1
# Invoices above ₹2,50,000 to unregistered persons → B2CL
if amount > B2CL_THRESHOLD and not recipient_gstin:
    table = "B2CL"

# GOOD — warns about non-obvious behaviour
# WARNING: openpyxl reads merged cells as None for all but the
# top-left cell. We handle this in _unmerge_and_fill() below.

# BAD — describes WHAT (obvious from the code, adds no value)
# increment counter by 1
counter += 1

# BAD — vague
# fix the thing
data = clean_data(data)
```

### Special Comment Tags (searchable markers):

```python
# TODO(Sidhant, DD-MM-YYYY): Add support for GSTR-2B reconciliation
#   Context: Client requested this, deferred to Phase 2.
#   See Parking Lot entry #3 in Rebuild Brief.

# FIXME(DD-MM-YYYY): This breaks if Excel file has merged headers.
#   Workaround: manually unmerge before processing.
#   Proper fix: implement auto-unmerge in readers/excel_reader.py

# HACK(DD-MM-YYYY): Using string comparison for dates because
#   openpyxl returns mixed types (datetime vs string) depending
#   on Excel cell formatting. Proper fix: normalise in reader.

# ASSUMPTION(DD-MM-YYYY): Assuming all input files use UTF-8.
#   If client sends ANSI-encoded files, this will break.
#   Decided in Session 3 — revisit if encoding errors appear.

# LEGAL(Section 16(2) CGST Act): ITC available only if supplier
#   has filed GSTR-1 and tax is reflected in recipient's GSTR-2B.
```

---

## Standard 6: No Magic Numbers

NEVER hardcode values directly in logic. Always use named constants
from `config/constants.py`:

```python
# BAD — magic number, no one knows what 250000 means
if amount > 250000:
    table = "B2CL"

# GOOD — named constant with clear meaning
# In config/constants.py:
B2CL_THRESHOLD = 250000  # ₹2,50,000 — Section 37 / GSTR-1 Table 5

# In core/return_classifier.py:
from config.constants import B2CL_THRESHOLD
if amount > B2CL_THRESHOLD:
    table = "B2CL"
```

This applies to ALL values: tax rates, thresholds, file size limits,
retry counts, timeout values, column names, sheet names, date formats,
state codes — everything. If a value might ever change or needs
explanation, it belongs in config/.

---

## Standard 7: Consistent Error Handling

Every function that can fail follows this pattern:

```python
def read_excel_file(file_path):
    """
    [Standard docstring as per Standard 4]
    """
    # --- Input validation ---
    if not file_path:
        return {"success": False, "error": "No file path provided", "data": None}

    if not os.path.exists(file_path):
        return {"success": False, "error": f"File not found: {file_path}", "data": None}

    if not file_path.endswith(('.xlsx', '.xls')):
        return {"success": False, "error": f"Not an Excel file: {file_path}", "data": None}

    # --- Main logic ---
    try:
        workbook = openpyxl.load_workbook(file_path)
        # ... processing ...
        return {"success": True, "error": None, "data": processed_data}

    except PermissionError:
        # File is open in another program
        return {"success": False,
                "error": f"Cannot read file — it may be open in Excel: {file_path}",
                "data": None}

    except Exception as e:
        # Unexpected error — log details, show friendly message
        return {"success": False,
                "error": f"Unexpected error reading {file_path}: {str(e)}",
                "data": None}
```

Rules:
- NEVER let exceptions crash the application
- ALWAYS return a consistent result format: `{"success": bool, "error": str, "data": ...}`
- ALWAYS validate inputs at the top of the function, before any processing
- ALWAYS catch specific exceptions first (PermissionError, FileNotFoundError),
  then catch general Exception as a safety net
- Error messages must be in PLAIN ENGLISH — the user is not a programmer.
  "File not found" not "FileNotFoundError". "File may be open in Excel" not
  "PermissionError: [Errno 13]"

---

## Standard 8: Type Hints

Every function signature must include type hints. These make the code
self-documenting — even a non-programmer can see what goes in and what
comes out:

```python
# Without type hints — unclear what this expects or returns
def validate_gstin(gstin_string, strict_mode):
    ...

# With type hints — immediately clear
def validate_gstin(gstin_string: str, strict_mode: bool = False) -> dict:
    ...

# More examples:
def read_excel_file(file_path: str) -> dict:
def calculate_tax(amount: float, rate: float, is_interstate: bool) -> dict:
def format_date(date_value: str, output_format: str = "DD-MM-YYYY") -> str:
def get_state_name(state_code: str) -> str | None:
```

---

## Standard 9: Import Organisation

Every file organises imports in this order, with blank lines between groups:

```python
# Group 1: Python standard library
import os
import sys
from datetime import datetime
from pathlib import Path

# Group 2: Third-party libraries
import openpyxl
from PyQt5.QtWidgets import QMainWindow, QPushButton

# Group 3: This project's modules
from config.constants import STATE_CODES, B2CL_THRESHOLD
from config.settings import OUTPUT_PATH
from core.gstin_validator import validate_gstin
from utils.format_helpers import clean_string
```

NEVER use `from module import *` — always import specific names.
NEVER use relative imports (`.module`) — always use full paths from
the project root.

---

## Standard 10: Git Commit Messages

Every git commit follows this format:

```
[TYPE] Short description (what changed)

WHY: Why this change was made
AFFECTS: Which files/features are impacted
EDGE CASES: Any new edge cases handled (if applicable)
DECIDED: Reference to decision (if applicable)
```

Types:
- `[FEATURE]` — New feature added
- `[FIX]` — Bug fix
- `[REFACTOR]` — Code restructure without behaviour change
- `[CONFIG]` — Settings, constants, dependency changes
- `[DOCS]` — Documentation only
- `[STYLE]` — Formatting, naming (no logic change)

Example:
```
[FEATURE] Add GSTIN checksum validation

WHY: Format-only validation missed GSTINs with valid structure but wrong
     check digit. 3 client files had this issue in January batch.
AFFECTS: core/gstin_validator.py (new verify_checksum function),
         config/constants.py (added CHECKSUM_CHARS mapping)
EDGE CASES: Handles old-format GSTINs from pre-2019 registrations
DECIDED: Session 2, Message 14 — chose algorithmic check over API lookup
```

---

## Standard 11: Assumptions Must Be Visible

If Claude Code makes ANY assumption — about data format, user behaviour,
business rules, file structure, edge case handling, or anything else —
it MUST appear in THREE places:

1. **In the code** — in the ASSUMPTIONS field of the function docstring,
   or as an `# ASSUMPTION(date):` inline comment
2. **In the DECISIONS_LOG** — in that message's log entry
3. **Verbally in the chat** — "I'm assuming X because Y. Is that correct?"

No silent assumptions. Ever. If the Brief doesn't cover something and
Claude Code has to make a judgment call, that judgment must be visible
and traceable. Silent assumptions become invisible bugs.

---
```

---

## Document 4: DECISIONS_LOG.md (auto-maintained by Claude Code)

**File:** `.build-docs/DECISIONS_LOG.md`
**Created by:** You create the empty file with the header below. Claude Code
fills it automatically after every single message.
**Purpose:** This is your project's memory. Every exchange gets logged here in
real time. When a session ends and the chat history is gone, this file preserves
what happened.

**Why this exists:** Human memory is not a hard drive. You will forget why you
chose dropdown over radio buttons. You will forget which edge case prompted that
if-statement. This file remembers so you don't have to.

### Template:

```markdown
# Decisions Log: [PROJECT_NAME]

> This file is auto-maintained by Claude Code. After EVERY message exchange,
> Claude Code appends a 2-3 line summary below. Do not edit manually unless
> correcting an error.

---

## Session 1 — [DATE]

(Entries will be added automatically below)

```

---

# PART 2: THE REBUILD PROMPT

This is what you paste into Claude Code to start the actual rebuild. The four
documents above should already be in the project folder.

```
I am rebuilding a tool from scratch based on a detailed audit of the
original version and my rebuild decisions. Before writing any code,
read these four documents in this order:

1. `CLAUDE.md` — My preferences, coding standards, and project principles
2. `.build-docs/PROJECT_AUDIT_[PROJECT_NAME].md` — Complete audit of the original tool
3. `.build-docs/REBUILD_BRIEF_[PROJECT_NAME].md` — My decisions: what to keep, change, drop, add
4. `.build-docs/DECISIONS_LOG.md` — Running log of all past discussions (read latest entries to understand where we left off)

Also check `.build-docs/context/` for any sample files, templates, or
reference documents. Read them before building any related feature.

After reading everything, do the following:

## Step A: Confirm Understanding
Summarize back to me in 5-6 sentences:
- What this tool does
- The key differences between the original and what I want in the rebuild
- The build order you recommend (which feature first, second, third...)
- Any questions or conflicts you spotted between the audit and my brief

Do NOT start writing code until I confirm your understanding is correct.

## Step B: Build Plan
Once I confirm, produce a build plan in TWO parts:

### Part 1: File Structure
Propose the complete folder and file structure BEFORE any code.
Use the modular folder-based organisation defined in CLAUDE.md:
- `main.py` in root (entry point only)
- `config/` folder for settings, constants, lookup tables
- `core/` folder with one file per business logic domain
- `models/` folder for data structure definitions
- `readers/` folder with one file per input format
- `writers/` folder with one file per output type
- `gui/` folder with one file per screen or dialog
- `utils/` folder for shared helpers, split by concern
For each file, write ONE sentence describing its single responsibility.
Not every folder is needed — propose only what's relevant to this project.
But NEVER propose a flat or single-file structure.

### Part 2: Feature Checklist
- Each feature as a numbered item
- Which files from the structure handle this feature
- Dependencies noted (e.g., "F3 requires F1 to be complete first")
- Edge cases from the Rebuild Brief listed under each feature

Wait for my approval on BOTH the structure AND the plan before coding.

## Step C: Build Incrementally
After I approve the plan:
- Build ONE feature at a time
- After each feature, show me what was created and explain in plain English
- Ask me to test or confirm before moving to the next feature
- If you encounter a decision point not covered in my brief, ASK me — do not assume
- **Put each piece of logic in the correct file from the approved structure.
  Do NOT dump new logic into main.py or combine unrelated logic in one file.
  If a new file is needed that wasn't in the approved structure, tell me
  WHY before creating it. Never reorganise or move files without my approval.**
- **Follow ALL coding standards in CLAUDE.md — file headers, function
  docstrings with CALLED BY/CALLS/EDGE CASES/ASSUMPTIONS, type hints,
  named constants, consistent error handling. No exceptions. No shortcuts.**

## Step D: After Each Feature
Once a feature is working:
- Update CLAUDE.md with any new conventions or decisions we made
- Note any new edge cases we discovered in the Parking Lot section of the Rebuild Brief
- Commit to git with a clear message following the format in CLAUDE.md Standard 10

## Step E: MANDATORY — Log Every Exchange
**After EVERY response you give me — no exceptions — append a 2-3 line entry
to `.build-docs/DECISIONS_LOG.md` using this format:**

### [DATE] | Session [N] | Message [N]
- **Discussed:** [What we talked about in this exchange]
- **Decided/Built:** [What was decided or what code was written]
- **Open:** [Any unresolved question or next step]

**Do this silently after every single response. Do not ask permission.
Do not skip it because the exchange seemed minor. Do not announce that
you are doing it. Every exchange matters — the minor ones are often the
ones we forget first.**

**At the start of each new session, add a session header:**

## Session [N] — [DATE]

## Step F: Testing Guidance
After building each feature, tell me how to test it:
- Give me specific steps: "Click this button, select this file, check this output"
- Tell me what the correct result should look like
- Tell me what to try that SHOULD fail (to verify error handling works)
- If I can provide a sample file for testing, tell me what it should contain

I am not a programmer — do not tell me to "run the test suite" or
"check the console." Tell me what to CLICK and what to LOOK AT.

## Step G: Final Documentation
When all features are complete and tested:
- Generate a `README.md` using the standard README template
- Fill in every section based on what was actually built (not what was planned)
- Include the 3-5 most likely problems a user will encounter
- Include a version history starting at 1.0
- Commit the README as the final git commit with message:
  "[DOCS] Add user-facing README — project complete"
```

---

# PART 3: STRUCTURED PROMPTING STYLE FOR MID-BUILD CHANGES

When something pops up during the build — a new idea, a correction, a change of
mind — use these templates. The structure ensures Claude Code gets complete
context every time.

---

## Template 1: NEW FEATURE REQUEST (mid-build)

```
## New Feature Request

**What I want:**
[Describe in plain English]

**Why I need it:**
[The problem this solves or the scenario that triggered this idea]

**Where it fits:**
[Which existing feature does it relate to? Or is it standalone?]

**Input → Output:**
[What goes in, what should come out]

**Edge cases to handle:**
[Any scenarios you already know about]

**Priority:**
MUST HAVE — block everything until done
NICE TO HAVE — add to Parking Lot for later

**Scope check:**
Is this listed in the Brief's future possibilities? If yes, add to Parking Lot.
```

---

## Template 2: CHANGE EXISTING FEATURE

```
## Change Request

**Which feature:**
[Feature name or F-number from the build plan]

**What it currently does:**
[Brief description of current behaviour]

**What I want instead:**
[Describe the new behaviour]

**Why the change:**
[What triggered this — a real-world scenario, a better idea, user feedback?]

**Impact check:**
Does this change affect any other feature? If unsure, check before implementing.
```

---

## Template 3: BUG OR UNEXPECTED BEHAVIOUR

```
## Bug Report

**What I did:**
[Step by step — what buttons I clicked, what file I selected, etc.]

**What I expected:**
[What should have happened]

**What actually happened:**
[What went wrong — error message, wrong output, crash, etc.]

**Sample data:**
[If possible, describe or attach the input that caused the problem]

**Reproducible?**
[Does it happen every time, or only sometimes?]
```

---

## Template 4: DESIGN DECISION CHECKPOINT

```
## Design Decision

**Decision:** [What was decided]

**Options considered:**
1. [Option A] — [Pro / Con]
2. [Option B] — [Pro / Con]
3. [Option C] — [Pro / Con]

**Chosen:** [Which option and why]

**Trade-off accepted:** [What downside we're living with]

> Add this decision to CLAUDE.md so future sessions remember it.
```

---

## Template 5: PARKING LOT IDEA (not urgent, save for later)

```
## Parking Lot Entry

**Idea:** [Brief description]
**Triggered by:** [What made you think of this during the current work]
**Priority:** HIGH — do after current feature / LOW — revisit at end
**Notes:** [Any additional context]

> Add to the Parking Lot table in REBUILD_BRIEF.
> Do NOT implement now — continue with the current feature.
```

---

## Template 6: SESSION HANDOVER (when starting a new chat/session)

```
## Session Continuation

Read CLAUDE.md for project context and coding standards.
Read .build-docs/DECISIONS_LOG.md for full history of discussions and decisions.

**Last session we completed:**
[Feature name or what was done]

**Current state:**
[What's working, what's partially done]

**This session I want to:**
[What to build or fix next]

**Any open decisions from last session:**
[Check the "Open" field in the last few DECISIONS_LOG entries]
```

---

## Template 7: I DON'T UNDERSTAND WHAT YOU BUILT

```
## Explain This Differently

I don't follow what you just built or why. Please explain it as if I'm
a CA reviewing a client's books — walk me through what each part does
using a real-world analogy from accounting or GST practice.

Specifically:
- What does this code do in plain English?
- Why was it needed?
- How does it connect to the features I asked for?
- Walk me through one example with real data
```

---

# THE COMPLETE PIPELINE — VISUAL SUMMARY

```
STAGE 1: ARCHAEOLOGY          STAGE 2: DECISIONS           STAGE 3: REBUILD
┌─────────────────┐          ┌─────────────────┐          ┌──────────────────┐
│  Run Audit       │          │  Read Audit      │          │  Provide 4 docs  │
│  Prompt (v3)     │───────→  │  Write Rebuild   │───────→  │  to Claude Code   │
│  on old project  │          │  Brief           │          │  Run Build Prompt │
│                  │          │  Write CLAUDE.md │          │  Build feature    │
│  OUTPUT:         │          │  Create empty    │          │  by feature       │
│  Audit .md file  │          │  DECISIONS_LOG   │          │                   │
│                  │          │  Add context/    │          │  DECISIONS_LOG    │
│                  │          │  sample files    │          │  grows after      │
│                  │          │                  │          │  EVERY message    │
│                  │          │  YOUR DECISIONS: │          │                   │
│                  │          │  Keep / Change / │          │  Code follows ALL │
│                  │          │  Drop / Add      │          │  11 standards     │
└─────────────────┘          └─────────────────┘          └──────────────────┘
     You observe                 You decide                   You build
                                                              (Claude remembers)
```
