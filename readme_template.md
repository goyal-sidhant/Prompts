# README.md Template for Projects

> **When to create this:** After the tool is built and working. Not during the build.
> **Who reads this:** Your team members, your future self, anyone opening this project folder.
> **Who does NOT read this:** Claude Code (that's what CLAUDE.md is for).

---

## The Template

```markdown
# [TOOL NAME]

**What it does:** [One sentence. e.g., "Reads client invoice Excel files and produces
a GST Portal-ready upload file for GSTR-1 filing."]

**Built by:** Adv. Sidhant Goyal, Goyal Tax Services Pvt. Ltd.
**Last updated:** [Date]
**Version:** [e.g., 1.0 / 1.1 / 2.0]
**Status:** [Working / In Progress / Needs Fixes]

---

## How to Use

### Step 1: Open the Tool
[Exactly how to start it. e.g., "Double-click `run.bat` in the project folder."
or "Open terminal in this folder and type `python main.py`"]

### Step 2: [First Action]
[e.g., "Click 'Select Folder' and choose the folder containing client Excel files."]

### Step 3: [Next Action]
[e.g., "Click 'Process'. The tool will read all Excel files and show a summary."]

### Step 4: [Continue until complete workflow is documented]
[e.g., "Review any errors in the table below. Fix the source files and click
'Re-Process', or click 'Export' to generate the final output."]

### Step 5: Output
[Where to find the output. e.g., "The output file is saved in the same folder
as the input files, named `GSTR1_Upload_[date].xlsx`."]

> Add as many steps as needed. Each step = one user action.
> Include what the screen looks like after each step if helpful.

---

## Input Requirements

| What | Details |
|------|---------|
| **File format** | [e.g., ".xlsx only. Not .xls, not .csv."] |
| **Required columns** | [e.g., "Invoice No, Date, GSTIN, Taxable Value, Tax Rate"] |
| **Column names** | [e.g., "Must match exactly. See sample file for reference."] |
| **File location** | [e.g., "All files must be in one folder. Subfolders are not scanned."] |
| **Sample file** | [e.g., "See `sample_input.xlsx` in the project folder."] |

---

## Output Description

| What | Details |
|------|---------|
| **File name** | [e.g., "`GSTR1_Upload_DDMMYYYY.xlsx`"] |
| **Location** | [e.g., "Saved in the same folder as input files."] |
| **Format** | [e.g., "Matches GST Portal bulk upload template."] |
| **Sheets/Tabs** | [e.g., "Sheet 1: B2B, Sheet 2: B2CL, Sheet 3: B2CS, Sheet 4: Errors"] |

---

## Common Problems and Solutions

### [Problem 1: e.g., "Tool shows 'No valid files found'"]
**Why:** [e.g., "The selected folder has no .xlsx files, or all files have errors."]
**Fix:** [e.g., "Check that the folder contains .xlsx files (not .xls). Open one
file and verify the column headers match the expected names."]

### [Problem 2: e.g., "GSTIN validation errors on every row"]
**Why:** [e.g., "The GSTIN column may have extra spaces or formatting."]
**Fix:** [e.g., "Open the Excel file, select the GSTIN column, and do
Find & Replace: find a space, replace with nothing."]

### [Problem 3: e.g., "Tool freezes with large files"]
**Why:** [e.g., "Files with more than 10,000 rows take longer to process."]
**Fix:** [e.g., "Wait 1-2 minutes. The tool is working but has no progress bar
for this operation. (This is a known limitation.)"]

> Add 3-5 of the most likely problems. Don't try to cover everything —
> cover what will actually happen in practice.

---

## What This Tool Does NOT Do

- [e.g., "Does not file the return — only prepares the upload file."]
- [e.g., "Does not connect to the internet — works fully offline."]
- [e.g., "Does not handle amendments or credit/debit notes."]
- [e.g., "Does not auto-update — if GST rules change, the tool needs to be updated."]

---

## Setup (for a new computer)

### Requirements
- Windows 10 or 11
- Python [version] — [link or "already installed on office computers"]
- [Any other requirement]

### Installation Steps
1. [e.g., "Copy the entire project folder to your computer."]
2. [e.g., "Open terminal (Command Prompt) in the folder."]
3. [e.g., "Run: `pip install -r requirements.txt`"]
4. [e.g., "Run: `python main.py` to start the tool."]

> If you've created a .bat or .exe launcher, document that instead.

---

## Version History

| Version | Date | What Changed |
|---------|------|--------------|
| 1.0 | [Date] | Initial version. Handles B2B and B2CS invoices. |
| 1.1 | [Date] | Added B2CL support. Fixed GSTIN space-trimming bug. |
| [etc.] | | |

---

## Project Files (what's in this folder)

| File/Folder | Purpose | Do I need to touch it? |
|-------------|---------|----------------------|
| `main.py` | Starts the application | No — just run it |
| `gst_rules.py` | All GST validation rules | Only if GST rules change |
| `ui/` | Interface screens | No |
| `sample_input.xlsx` | Example input file | Use as reference |
| `CLAUDE.md` | Instructions for AI assistant | Only when rebuilding |
| `DECISIONS_LOG.md` | Build history | Read-only reference |
| `requirements.txt` | Software dependencies | Only during setup |
| `README.md` | This file | You're reading it |

> The "Do I need to touch it?" column is specifically for non-coders.
> It tells your team member what's safe to ignore.

---

## Contact

For issues or changes: [Your contact info or "Ask Sidhant"]
```

---

## When to Add This to the Pipeline

Add this instruction to the build prompt, after Step D (After Each Feature),
as a final project step:

```
## Step G: Final Documentation
When all features are complete and tested:
- Generate a `README.md` using the project's README template
- Fill in every section based on what was actually built (not what was planned)
- Include the 3-5 most likely problems a user will encounter (based on
  edge cases discovered during the build)
- Include a version history starting at 1.0
- Commit the README as the final git commit with message:
  "Add user-facing README — project complete"
```
