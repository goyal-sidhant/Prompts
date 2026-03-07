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
- `.build-docs/PROJECT_AUDIT_[name].md` — Full audit of original tool (rebuild only)
- `.build-docs/REBUILD_BRIEF_[name].md` — My rebuild decisions (rebuild only)
- `.build-docs/PROJECT_BRIEF.md` — What we're building and why (new project only)
- `.build-docs/DECISIONS_LOG.md` — Running log of every discussion and decision
- `.build-docs/context/` — Sample files, templates, reference documents

> Delete the lines above that don't apply (rebuild vs new project).

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

For small helpers (under 5 lines), shortened format:

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
# TODO(Sidhant, DD-MM-YYYY): Add GSTR-2B support. Deferred Phase 2.
# FIXME(DD-MM-YYYY): Breaks with merged Excel headers.
# HACK(DD-MM-YYYY): String date comparison — openpyxl mixed types.
# ASSUMPTION(DD-MM-YYYY): All files UTF-8. Revisit if errors appear.
# LEGAL(Section 16(2)): ITC only if supplier filed GSTR-1.
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
- Validate inputs FIRST
- Catch specific exceptions first, then general Exception
- Error messages in PLAIN ENGLISH

---

## Standard 8: Type Hints

```python
def validate_gstin(gstin_string: str, strict_mode: bool = False) -> dict:
def calculate_tax(amount: float, rate: float, is_interstate: bool) -> dict:
def get_state_name(state_code: str) -> str | None:
```

---

## Standard 9: Import Organisation

```python
# Group 1: Python standard library
import os
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
