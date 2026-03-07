# SKILL.md Template

> **What is this?** A Claude Code / Antigravity Skill is a folder containing
> a SKILL.md file (instructions + rules) plus optional Python scripts and
> reference files. When installed, Claude Code reads it automatically and
> applies the knowledge whenever relevant.
>
> **Where to install:**
> - Claude Code (global): `~/.claude/skills/[skill-name]/SKILL.md`
> - Claude Code (project): `.claude/skills/[skill-name]/SKILL.md`
> - Antigravity (global): `~/.gemini/antigravity/skills/[skill-name]/SKILL.md`
> - Antigravity (workspace): `.agent/skills/[skill-name]/SKILL.md`
>
> **Folder structure:**
> ```
> gst-gstin-validator/
> ├── SKILL.md              ← This file (required)
> ├── gstin_rules.md        ← Reference material (optional)
> ├── state_codes.md        ← Additional reference (optional)
> └── validate_gstin.py     ← Python script (optional)
> ```

---

## The Template

```yaml
---
name: gst-[topic-name]
description: >
  [Clear description of what this skill does and WHEN Claude should use it.
  Be specific about trigger words and scenarios. Max 200 characters.
  Example: "Validates Indian GSTIN format, structure, and check digit.
  Use when validating GSTINs, checking tax registrations, or processing
  GST invoice data."]
---
```

# [Skill Title]

## Purpose

[2-3 sentences: what this skill enables Claude to do. Write for an AI audience —
what task does this skill help with, and what would Claude get wrong without it?]

## Legal Basis

All sections, rules, notifications, and circulars referenced in this skill:

| Source | Reference | Topic |
|--------|-----------|-------|
| CGST Act | Section XX | [Brief topic] |
| CGST Rules | Rule XX | [Brief topic] |
| Notification | No. XX/YYYY-CT dated DD-MM-YYYY | [Brief topic] |
| Circular | No. XX/YYYY-GST dated DD-MM-YYYY | [Brief topic] |

## Last Updated

- **Date:** DD-MM-YYYY
- **Based on:** [Latest amendment/notification considered]
- **Next review due:** [After next GST Council meeting / specific date]
- **Maintained by:** Adv. Sidhant Goyal, Goyal Tax Services Pvt. Ltd.

---

## Rules

### [Category 1: e.g., Validation Rules]

**Rule 1.1: [Rule name]**
- **Rule:** [Plain English statement of the rule]
- **Legal source:** [Section X / Rule Y / Notification Z dated DD-MM-YYYY]
- **Edge cases:**
  - [Practical nuance 1]
  - [Practical nuance 2]
- **Example:** [One concrete example showing the rule in action]
  - Input: [specific input]
  - Expected result: [what should happen]

**Rule 1.2: [Rule name]**
- **Rule:** [Plain English statement]
- **Legal source:** [Citation]
- **Edge cases:** [Nuances]
- **Example:** [Concrete example]

### [Category 2: e.g., Calculation Rules]

[Same structure as above]

### [Category 3: e.g., Classification Rules]

[Same structure as above]

---

## Common Errors and Pitfalls

Mistakes practitioners and taxpayers commonly make related to this topic:

1. **[Error name]:** [What goes wrong and why]
   - **Correct approach:** [What to do instead]

2. **[Error name]:** [What goes wrong and why]
   - **Correct approach:** [What to do instead]

---

## Limitations

What this skill does NOT cover — explicit boundaries:
- [e.g., "Does not check if GSTIN is active/cancelled — requires API"]
- [e.g., "Does not cover UIN registration for embassies"]
- [e.g., "Rate changes after [date] are not reflected — check next review date"]

---

## Scripts

[If this skill includes Python scripts, list them here:]

- `validate_gstin.py` — Takes a GSTIN string, returns validation result
  with extracted components. Importable as module.
  Usage: `from validate_gstin import validate_gstin`

- `[other_script.py]` — [Description and usage]

---

## Change Log

| Date | Change | Legal Basis | Verified By |
|------|--------|-------------|-------------|
| DD-MM-YYYY | Initial version | [Base Act/Rules] | Adv. Sidhant Goyal |
| | | | |
