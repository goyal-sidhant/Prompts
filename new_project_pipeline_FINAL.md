# The New Project Pipeline — FINAL VERSION

> **When to use this:** You're building a brand new tool. No existing code, no audit,
> no previous version. Just an idea and a problem to solve.
>
> **When NOT to use this:** If you're rebuilding an existing tool, use the
> Rebuild Pipeline instead — it includes the audit step which extracts knowledge
> from the old codebase.
>
> This document covers:
> 1. What documents you prepare BEFORE giving the build prompt
> 2. The Build Prompt itself — what goes in it
> 3. The Structured Prompting Style for mid-build changes

---

# PART 1: DOCUMENTS YOU PREPARE BEFORE THE BUILD

You will provide Claude Code with **three documents** when starting a new project.
Two you prepare upfront, one starts empty and grows automatically.

---

## Document 1: The Project Brief (YOU write this — most important document)

**File:** `.build-docs/PROJECT_BRIEF.md`
**Created by:** You, before opening Claude Code
**Purpose:** This is the single source of truth for what you're building and why.
Every decision during the build traces back to this document.

**Before filling the template, answer these questions to yourself:**
- What problem am I solving? (Not "what tool" — the PROBLEM)
- Who will use this? Just me? My team? Clients?
- What does the input actually look like? (Open a real file and look at it)
- What should the output look like? (Sketch it on paper if needed)
- What are the rules? (Domain-specific business rules that must be right)
- What goes wrong in practice? (Messy real-world scenarios)

### Template:

```markdown
# Project Brief: [PROJECT_NAME]
# Date: [TODAY'S DATE]
# Author: Adv. Sidhant Goyal

---

## 1. THE PROBLEM

[Write 3-5 sentences about the problem this tool solves. Be specific.
Not "I need a GST tool" but "Every month I manually copy invoice data
from 15 client Excel files into the GST Portal format. This takes 4 hours,
and I've made copy-paste errors twice that required revised returns."]

---

## 2. THE SOLUTION (in plain English)

[Describe what the tool should do, as if explaining to a colleague.
"I want a desktop app where I select a folder of client Excel files,
the tool reads them, validates the data against GST rules, and produces
one combined Excel file ready for upload to the GST Portal."]

---

## 3. WHO USES THIS AND HOW

- **Primary user:** [e.g., "Me and 2 team members at Goyal Tax Services"]
- **Technical comfort:** [e.g., "Non-coders. Must work with clicks, no terminal."]
- **Frequency:** [e.g., "Monthly during GSTR-1 filing, daily during annual season"]
- **Environment:** [e.g., "Windows 10/11, files on local drives, sometimes network"]
- **Typical scenario:** [e.g., "User opens app → selects folder → clicks Process →
  reviews errors → fixes source files → re-processes → exports final output"]

---

## 4. INPUT — WHAT GOES IN

Describe every type of input the tool will handle:

### Input 1: [Name, e.g., "Client Invoice Excel Files"]
- **Format:** [e.g., ".xlsx files, one per client"]
- **Structure:** [e.g., "Headers in row 1: Invoice No, Date, GSTIN, Amount, Tax Rate"]
- **Variations you've seen:** [e.g., "Some clients use row 2 for headers. Some use
  'Inv No' instead of 'Invoice No'. Some leave Tax Amount blank."]
- **Volume:** [e.g., "15-20 files per month, 50-500 rows each"]
- **Sample file:** [e.g., "See `.build-docs/context/sample_input.xlsx`"]

### Input 2: [If additional inputs exist, document each one the same way]

---

## 5. OUTPUT — WHAT COMES OUT

### Output 1: [Name, e.g., "GST Portal Upload File"]
- **Format:** [e.g., ".xlsx matching GST Portal bulk upload template"]
- **Structure:** [e.g., "Columns: GSTIN, Invoice No, Date, Value, Rate, IGST, CGST, SGST"]
- **Sample/Template:** [e.g., "Must match `.build-docs/context/portal_template.xlsx`"]

### Output 2: [e.g., "Error Report"]
- **Format:** [e.g., "Displayed in app window AND saved as Excel"]
- **Content:** [e.g., "Each error: client name, row number, what's wrong, suggested fix"]

---

## 6. BUSINESS RULES AND DOMAIN LOGIC

List every rule the tool must follow. These are what make the tool CORRECT.

### Validation Rules:
- [e.g., "GSTIN must be exactly 15 characters: 2 state + 10 PAN + 1 entity + 1 blank + 1 check"]
- [e.g., "Tax rate must be one of: 0, 0.25, 3, 5, 12, 18, 28"]
- [e.g., "Invoice date must not be future-dated"]

### Calculation Rules:
- [e.g., "Supplier state = Recipient state → CGST + SGST (each = rate / 2)"]
- [e.g., "Supplier state ≠ Recipient state → IGST (= full rate)"]

### Classification Rules:
- [e.g., "B2B: Invoice where recipient has GSTIN"]
- [e.g., "B2CL: B2C invoice with value > ₹2,50,000 — report with state code"]
- [e.g., "B2CS: B2C invoice with value ≤ ₹2,50,000 — report aggregated"]

### Formatting Rules:
- [e.g., "Amounts rounded to 2 decimal places"]
- [e.g., "Dates in DD-MM-YYYY for portal"]

---

## 7. EDGE CASES — WHAT CAN GO WRONG

Real-world scenarios you've actually encountered:

- [e.g., "Client sends .xls instead of .xlsx"]
- [e.g., "Merged cells in header row"]
- [e.g., "GSTIN column has extra spaces"]
- [e.g., "Amount stored as text, not number"]
- [e.g., "Empty rows between data rows"]
- [e.g., "File is locked — open in Excel by someone"]

For each: what should happen?
- **Crash?** Never.
- **Skip and warn?** [When]
- **Auto-fix?** [When]
- **Ask the user?** [When]

---

## 8. INTERFACE PREFERENCES

- **Type:** [e.g., "Desktop app (PyQt5), NOT web, NOT command-line"]
- **Complexity:** [e.g., "Single window. No tabs unless necessary."]
- **Key elements:**
  - [e.g., "Browse folder button — large and obvious"]
  - [e.g., "Process button — disabled until folder selected"]
  - [e.g., "Error table — sortable by severity"]
  - [e.g., "Export button — appears only after successful processing"]
  - [e.g., "Progress indicator for large batches"]
- **Style:** [e.g., "Professional, clean. Large fonts."]
- **Error display:** [e.g., "In the main window, not pop-up dialogs"]

---

## 9. WHAT THIS TOOL IS NOT

Define the boundaries:
- [e.g., "Does NOT file the return — only prepares the upload file"]
- [e.g., "Does NOT connect to the internet — fully offline"]
- [e.g., "Does NOT handle amendments — only original invoices"]

---

## 10. FUTURE POSSIBILITIES (NOT for this build)

Ideas explicitly deferred:
- [e.g., "GSTR-2B reconciliation — Phase 2"]
- [e.g., "Auto-email output to clients — maybe later"]

This section prevents scope creep. If an idea pops up during the build,
check here — if listed, it goes to Parking Lot, not current sprint.

---

## 11. PARKING LOT

[Leave empty. Ideas during the build go here.]

| Idea | When it came up | Priority | Status |
|------|----------------|----------|--------|
| | | | |

---
```

---

## Document 2: CLAUDE.md (project memory and coding standards)

**File:** `CLAUDE.md` (placed in the project root — MUST be root, not a subfolder)
**Created by:** You, before starting the build
**Purpose:** Claude Code reads this automatically at every session start.
Contains identity, project context, ALL coding standards and documentation rules.

### Template:

```markdown
# Project: [PROJECT_NAME]

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
- `.build-docs/PROJECT_BRIEF.md` — What we're building and why
- `.build-docs/DECISIONS_LOG.md` — Running log of every discussion and decision
- `.build-docs/context/` — Sample files, templates, reference documents

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

### Functions and Variables
- snake_case: `validate_gstin()`, `tax_rate`, `input_file_path`
- Function names start with a VERB: `validate_`, `calculate_`, `read_`,
  `write_`, `export_`, `parse_`, `format_`, `check_`, `get_`, `set_`
- Booleans start with `is_` or `has_`: `is_valid`, `has_errors`
- NEVER single-letter variables except `i`, `j` in short loops
- NEVER vague names: `data`, `result`, `temp`, `info`
  Instead: `invoice_data`, `validation_result`, `temp_file_path`

### Classes
- PascalCase: `InvoiceValidator`, `ExcelReader`, `MainWindow`

### Constants
- ALL_UPPERCASE: `MAX_FILE_SIZE`, `GST_RATES`, `STATE_CODES`
- ALL constants live in `config/constants.py` — NEVER hardcode values
  directly in logic files

---

## Standard 3: File-Level Documentation

Every Python file must start with this header:

```python
"""
FILE: core/gstin_validator.py

PURPOSE: Validates GSTIN format, structure, and check digit.

CONTAINS:
- validate_gstin()     — Main validation, returns valid/invalid with details
- extract_components() — Breaks GSTIN into state, PAN, entity, check digit
- verify_checksum()    — Runs government check digit algorithm

DEPENDS ON:
- config/constants.py  → STATE_CODES, GSTIN_LENGTH, CHECKSUM_CHARS
- utils/format_helpers.py → clean_string()

USED BY:
- core/invoice_validator.py → calls validate_gstin() for each row
- gui/error_viewer.py → displays validation error messages

CHANGE LOG:
| Date       | Change                              | Why                            |
|------------|-------------------------------------|--------------------------------|
| DD-MM-YYYY | Created — initial validation rules  | Section 25 CGST Act            |
| DD-MM-YYYY | Added state code "00" handling      | Client data had code "00"      |
"""
```

---

## Standard 4: Function-Level Documentation

Every function MUST have ALL of these fields:

```python
def validate_gstin(gstin_string: str, strict_mode: bool = False) -> dict:
    """
    WHAT:
        Validates a GSTIN string against format rules and checksum.
        Returns result with valid/invalid status and extracted components.

    WHY ADDED:
        Clients submit invoices with malformed GSTINs — wrong length,
        invalid state codes, PAN typos. Catches errors before Portal upload.
        Legal basis: Section 25 CGST Act, Rule 10 CGST Rules.

    CALLED BY:
        → core/invoice_validator.py → validate_invoice_row()
        → gui/main_window.py → on_single_gstin_check()

    CALLS:
        → config/constants.py → STATE_CODES, GSTIN_LENGTH
        → utils/format_helpers.py → clean_string()
        → self → verify_checksum()

    EDGE CASES HANDLED:
        - None or empty input → invalid, "GSTIN is empty"
        - Leading/trailing spaces → auto-trimmed
        - Lowercase → auto-uppercased
        - Old state code "00" → "use state-specific code"
        - Valid format, wrong check digit → specific error
        - Non-string input (int, float) → converted to string first

    ASSUMPTIONS:
        - State codes current as of [date]. New codes need
          config/constants.py update.
        - Check digit uses government spec v1.0.
        *** strict_mode defaults False — most client data has minor
            issues that should be auto-corrected. Decided Session 2. ***

    PARAMETERS:
        gstin_string (str): GSTIN to validate. May be messy.
        strict_mode (bool): If True, rejects instead of auto-fixing.

    RETURNS:
        dict: {"valid": bool, "error": str|None, "state_code": str,
               "state_name": str, "pan": str, "entity_type": str}
    """
```

For small helpers (under 5 lines), shortened format is acceptable:

```python
def clean_string(text: str) -> str:
    """
    WHAT: Strips whitespace, converts to uppercase.
    CALLED BY: core/gstin_validator.py, core/invoice_validator.py
    RETURNS: Cleaned string, or "" if input is None.
    """
```

---

## Standard 5: In-Line Code Comments

```python
# GOOD — explains WHY
# GST Portal rejects amounts beyond 2 decimal places
amount = round(amount, 2)

# GOOD — cites legal basis
# LEGAL(Section 37 / GSTR-1 Table 5): B2CL threshold ₹2,50,000
if amount > B2CL_THRESHOLD and not recipient_gstin:
    table = "B2CL"

# BAD — describes WHAT (obvious from code)
# increment counter
counter += 1
```

### Special Comment Tags:
```python
# TODO(Sidhant, DD-MM-YYYY): Add GSTR-2B support. Deferred to Phase 2.
# FIXME(DD-MM-YYYY): Breaks with merged Excel headers. Workaround: manual unmerge.
# HACK(DD-MM-YYYY): String date comparison because openpyxl returns mixed types.
# ASSUMPTION(DD-MM-YYYY): All input files UTF-8. Revisit if encoding errors appear.
# LEGAL(Section 16(2)): ITC only if supplier filed and reflected in GSTR-2B.
```

---

## Standard 6: No Magic Numbers

```python
# BAD
if amount > 250000:
    table = "B2CL"

# GOOD — in config/constants.py:
B2CL_THRESHOLD = 250000  # ₹2,50,000 — Section 37 / GSTR-1 Table 5

# GOOD — in core/return_classifier.py:
from config.constants import B2CL_THRESHOLD
if amount > B2CL_THRESHOLD:
    table = "B2CL"
```

ALL values in config/. Tax rates, thresholds, limits, column names,
date formats, state codes — everything.

---

## Standard 7: Consistent Error Handling

```python
def read_excel_file(file_path: str) -> dict:
    # --- Input validation first ---
    if not file_path:
        return {"success": False, "error": "No file path provided", "data": None}
    if not os.path.exists(file_path):
        return {"success": False, "error": f"File not found: {file_path}", "data": None}

    # --- Main logic in try/except ---
    try:
        workbook = openpyxl.load_workbook(file_path)
        return {"success": True, "error": None, "data": processed_data}
    except PermissionError:
        return {"success": False,
                "error": f"File may be open in Excel: {file_path}", "data": None}
    except Exception as e:
        return {"success": False,
                "error": f"Unexpected error reading {file_path}: {str(e)}", "data": None}
```

Rules:
- NEVER let exceptions crash the app
- ALWAYS return: `{"success": bool, "error": str|None, "data": ...}`
- Validate inputs FIRST, before processing
- Catch specific exceptions first, then general Exception as safety net
- Error messages in PLAIN ENGLISH: "File may be open in Excel" not "PermissionError"

---

## Standard 8: Type Hints

```python
def validate_gstin(gstin_string: str, strict_mode: bool = False) -> dict:
def calculate_tax(amount: float, rate: float, is_interstate: bool) -> dict:
def format_date(date_value: str, output_format: str = "DD-MM-YYYY") -> str:
def get_state_name(state_code: str) -> str | None:
```

---

## Standard 9: Import Organisation

```python
# Group 1: Python standard library
import os
import sys
from datetime import datetime

# Group 2: Third-party libraries
import openpyxl
from PyQt5.QtWidgets import QMainWindow

# Group 3: This project's modules
from config.constants import STATE_CODES, B2CL_THRESHOLD
from core.gstin_validator import validate_gstin
```

NEVER use `from module import *`. NEVER use relative imports.

---

## Standard 10: Git Commit Messages

```
[TYPE] Short description

WHY: Why this change was made
AFFECTS: Which files/features
EDGE CASES: New edge cases handled (if any)
DECIDED: Session/decision reference (if any)
```

Types: [FEATURE], [FIX], [REFACTOR], [CONFIG], [DOCS], [STYLE]

---

## Standard 11: Assumptions Must Be Visible

If Claude Code makes ANY assumption, it MUST appear in THREE places:
1. In the code (ASSUMPTIONS field or `# ASSUMPTION()` comment)
2. In the DECISIONS_LOG entry
3. Verbally in the chat: "I'm assuming X because Y. Correct?"

No silent assumptions. Ever.

---
```

---

## Document 3: DECISIONS_LOG.md (auto-maintained by Claude Code)

**File:** `.build-docs/DECISIONS_LOG.md`
**Created by:** You create the empty file. Claude Code fills it automatically.
**Purpose:** After every single message exchange, Claude Code appends a summary.
This is your project's memory.

### Template:

```markdown
# Decisions Log: [PROJECT_NAME]

> Auto-maintained by Claude Code. After EVERY message exchange,
> a 2-3 line summary is appended. Do not edit manually unless
> correcting an error.

---

## Session 1 — [DATE]

(Entries added automatically below)

```

---

# PART 2: THE BUILD PROMPT

This is what you paste into Claude Code to start building. The three documents
above should already be in the project folder.

```
I am building a new tool from scratch. Before writing any code,
read these three documents in this order:

1. `CLAUDE.md` — My preferences, coding standards, and project principles
2. `.build-docs/PROJECT_BRIEF.md` — Complete specification of what I want built
3. `.build-docs/DECISIONS_LOG.md` — If this has entries from previous sessions, read them

Also check `.build-docs/context/` for any sample files, templates, or
reference documents. Read them before building any related feature.

After reading everything, do the following:

## Step A: Confirm Understanding
Summarize back to me in 5-6 sentences:
- What this tool does and the problem it solves
- The key features you will build
- The build order you recommend (which feature first, second, third...)
- Any ambiguities, contradictions, or gaps you spotted in my brief

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
Not every folder is needed — propose only what's relevant.
But NEVER propose a flat or single-file structure.

### Part 1.5: Interface Prototype
Before building any logic, create the GUI skeleton first:
- All screens, windows, and dialogs with correct layout
- All buttons, inputs, dropdowns, tables — positioned but non-functional
- Placeholder text and dummy data where real data will go
- Show me every screen. Let me approve the layout BEFORE adding real logic.
This prevents rebuilding the interface after logic is written.

### Part 2: Feature Checklist
- Each feature as a numbered item
- Which files from the structure handle this feature
- Dependencies noted (e.g., "F3 requires F1 to be complete first")
- Edge cases from the Brief listed under each feature
- Which business rules from Brief Section 6 apply to each feature

Wait for my approval on structure, prototype, AND plan before coding logic.

## Step C: Build Incrementally
After I approve the plan:
- Build ONE feature at a time
- After each feature, show me what was created and explain in plain English
- Ask me to test or confirm before moving to the next feature
- If you encounter a decision point not covered in my brief, ASK me — do not assume
- **Put each piece of logic in the correct file from the approved structure.
  Do NOT dump logic into main.py or combine unrelated logic in one file.
  If a new file is needed, tell me WHY before creating it.
  Never reorganise or move files without my approval.**
- **Follow ALL 11 coding standards in CLAUDE.md — file headers, function
  docstrings with CALLED BY/CALLS/EDGE CASES/ASSUMPTIONS, type hints,
  named constants, consistent error handling. No exceptions.**

## Step D: After Each Feature
Once a feature is working:
- Update CLAUDE.md with any new conventions or decisions
- Note new edge cases in the Parking Lot section of the Brief
- Commit to git following Standard 10 format in CLAUDE.md

## Step E: MANDATORY — Log Every Exchange
**After EVERY response you give me — no exceptions — append a 2-3 line entry
to `.build-docs/DECISIONS_LOG.md` using this format:**

### [DATE] | Session [N] | Message [N]
- **Discussed:** [What we talked about in this exchange]
- **Decided/Built:** [What was decided or what code was written]
- **Open:** [Any unresolved question or next step]

**Do this silently after every single response. Do not ask permission.
Do not skip it because the exchange seemed minor. Do not announce that
you are doing it. Every exchange matters.**

**At the start of each new session, add a session header:**

## Session [N] — [DATE]

## Step F: Testing Guidance
After building each feature, tell me how to test it with THREE checks:

### Check 1: Happy Path
- Exact steps: what to click, what file to select, what to look for
- What the correct output should look like

### Check 2: Edge Case
- What messy/unusual input to try (from Brief Section 7)
- What the tool should do (skip? warn? auto-fix?)

### Check 3: Error Handling
- What deliberately wrong input to feed it
- What error message should appear
- Confirm the app does NOT crash

I am not a programmer. Tell me what to CLICK and what to LOOK AT.
Do NOT tell me to "run tests" or "check the console."

## Step G: Final Documentation
When all features are complete and tested:
- Generate `README.md` using the standard README template
- Fill in based on what was ACTUALLY built
- Include 3-5 most likely user problems
- Version history starting at 1.0
- Final git commit: "[DOCS] Add user-facing README — project complete"
```

---

# PART 3: STRUCTURED PROMPTING STYLE FOR MID-BUILD CHANGES

---

## Template 1: NEW FEATURE REQUEST

```
## New Feature Request

**What I want:**
[Describe in plain English]

**Why I need it:**
[Problem it solves or scenario that triggered the idea]

**Where it fits:**
[Which existing feature does it relate to? Standalone?]

**Input → Output:**
[What goes in, what comes out]

**Edge cases to handle:**
[Scenarios you already know about]

**Priority:**
MUST HAVE — block everything / NICE TO HAVE — Parking Lot

**Scope check:**
Listed in Brief Section 10 (Future)? If yes → Parking Lot.
```

---

## Template 2: CHANGE EXISTING FEATURE

```
## Change Request

**Which feature:**
[Feature name or F-number]

**What it currently does:**
[Current behaviour]

**What I want instead:**
[New behaviour]

**Why:**
[Real-world scenario, better idea, or user feedback?]

**Impact check:**
Does this affect other features? Check before implementing.
```

---

## Template 3: BUG REPORT

```
## Bug Report

**What I did:**
[Step by step — buttons clicked, files selected]

**What I expected:**
[Correct behaviour]

**What actually happened:**
[Error message, wrong output, crash]

**Sample data:**
[Input that caused the problem]

**Reproducible?**
[Every time, or sometimes?]
```

---

## Template 4: DESIGN DECISION

```
## Design Decision

**Decision:** [What was decided]

**Options considered:**
1. [Option A] — [Pro / Con]
2. [Option B] — [Pro / Con]

**Chosen:** [Which and why]
**Trade-off accepted:** [Downside we're living with]

> Add to CLAUDE.md so future sessions remember it.
```

---

## Template 5: PARKING LOT

```
## Parking Lot Entry

**Idea:** [Brief description]
**Triggered by:** [What prompted this]
**Priority:** HIGH / LOW
> Add to Brief Parking Lot. Do NOT implement now.
```

---

## Template 6: SESSION HANDOVER

```
## Session Continuation

Read CLAUDE.md for context and standards.
Read .build-docs/DECISIONS_LOG.md for full history.

**Last session completed:** [What was done]
**Current state:** [Working / partially done]
**This session:** [What to build next]
**Open decisions:** [Check DECISIONS_LOG "Open" fields]
```

---

## Template 7: I DON'T UNDERSTAND

```
## Explain This Differently

I don't follow what you just built. Explain as if I'm a CA reviewing
a client's books — use real-world analogies from accounting/GST.

- What does this code do in plain English?
- Why was it needed?
- How does it connect to the features I asked for?
- Walk through one example with real data.
```

---

# THE COMPLETE PIPELINE — VISUAL SUMMARY

```
NEW PROJECT PIPELINE

┌──────────────────┐          ┌──────────────────┐
│  Write PROJECT    │          │  Provide 3 docs  │
│  BRIEF            │───────→  │  to Claude Code   │
│  Write CLAUDE.md  │          │  Run Build Prompt │
│  (with 11         │          │                   │
│   coding          │          │  Step A: Confirm  │
│   standards)      │          │  Step B: Structure│
│  Create empty     │          │       + Prototype │
│  DECISIONS_LOG    │          │       + Plan      │
│  Add context/     │          │  Step C: Build    │
│  sample files     │          │  Step D: Commit   │
│                   │          │  Step E: Log      │
│                   │          │  Step F: Test     │
│                   │          │  Step G: README   │
└──────────────────┘          └──────────────────┘
    You think                     You build
    (30 min here saves            (Claude follows
     3 hours there)                all 11 standards,
                                   logs everything)
```
