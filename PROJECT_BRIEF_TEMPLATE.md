# Project Brief: [PROJECT_NAME]
# Date: [TODAY'S DATE]
# Author: Adv. Sidhant Goyal

---

## How to Fill This Brief

**Before filling, answer these questions to yourself (don't skip):**
- What problem am I solving? (Not "what tool" — the PROBLEM)
- Who will use this? Just me? My team? Clients?
- What does the input actually look like? (Open a real file and look)
- What should the output look like? (Sketch on paper if needed)
- What are the rules? (Domain-specific business rules that must be right)
- What goes wrong in practice? (Messy real-world scenarios)

**Spend 30 minutes on this brief. It saves 3 hours during the build.**

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

### Input 2: [If additional inputs, document each one the same way]

> IMPORTANT: Attach a real sample file to `.build-docs/context/` before starting.
> Include 2-3 intentionally messy rows. A real file is worth more than
> 500 words of description.

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

List every rule. These make the tool CORRECT, not just functional.

### Validation Rules:
- [e.g., "GSTIN must be exactly 15 characters: 2 state + 10 PAN + 1 entity + 1 blank + 1 check"]
- [e.g., "Tax rate must be one of: 0, 0.25, 3, 5, 12, 18, 28"]
- [e.g., "Invoice date must not be future-dated"]

### Calculation Rules:
- [e.g., "Supplier state = Recipient state → CGST + SGST (each = rate / 2)"]
- [e.g., "Supplier state ≠ Recipient → IGST (= full rate)"]

### Classification Rules:
- [e.g., "B2B: Invoice where recipient has GSTIN"]
- [e.g., "B2CL: B2C invoice with value > ₹2,50,000"]
- [e.g., "B2CS: B2C invoice with value ≤ ₹2,50,000"]

### Formatting Rules:
- [e.g., "Amounts rounded to 2 decimal places"]
- [e.g., "Dates in DD-MM-YYYY for portal"]

---

## 7. EDGE CASES — WHAT CAN GO WRONG

Real-world scenarios you've actually encountered:

- [e.g., "Client sends .xls instead of .xlsx"]
- [e.g., "Merged cells in header row"]
- [e.g., "GSTIN has extra spaces"]
- [e.g., "Amount stored as text, not number"]
- [e.g., "Empty rows between data"]
- [e.g., "File locked — open in Excel"]

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
  - [e.g., "Error table — sortable"]
  - [e.g., "Export button — appears after successful processing"]
  - [e.g., "Progress indicator"]
- **Style:** [e.g., "Professional, clean. Large fonts."]
- **Error display:** [e.g., "In main window, not pop-up dialogs"]

---

## 9. WHAT THIS TOOL IS NOT

Define the boundaries:
- [e.g., "Does NOT file the return — only prepares upload file"]
- [e.g., "Does NOT connect to internet — fully offline"]
- [e.g., "Does NOT handle amendments — only original invoices"]

---

## 10. FUTURE POSSIBILITIES (NOT for this build)

Ideas explicitly deferred:
- [e.g., "GSTR-2B reconciliation — Phase 2"]
- [e.g., "Auto-email to clients — maybe later"]

> This prevents scope creep. If an idea pops up during build,
> check here first — if listed, it goes to Parking Lot.

---

## 11. PARKING LOT

[Leave empty. Ideas during the build go here.]

| Idea | When it came up | Priority | Status |
|------|----------------|----------|--------|
| | | | |
