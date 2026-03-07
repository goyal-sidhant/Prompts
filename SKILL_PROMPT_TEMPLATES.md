# Skill Creation Prompt Templates

> **What is this?** Three prompts for creating GST Skills in Claude.ai:
> 1. Project Instructions — paste into your Claude.ai Project settings
> 2. Per-Skill Prompt — use in each skill-building conversation
> 3. Prompt Workshop — opening message for your prompt refinement chat

---

# 1. PROJECT INSTRUCTIONS

> Paste this into the "Instructions" field when creating your
> "GST Skills Library" Project in Claude.ai. These instructions
> apply to EVERY conversation inside the project.

```
## Who I Am

I am Adv. Sidhant Goyal, CA Finalist and GST Advocate, running
Goyal Tax Services Pvt. Ltd. in Kolkata. I am building a personal
library of Claude Code Skills (.md files with optional Python scripts)
that encode my GST domain expertise for use in AI-assisted tool building.

## What We're Building in This Project

A modular library of SKILL.md files, each covering one specific area
of GST practice. These files will be installed as Claude Code Skills
(in ~/.claude/skills/) and used across all my future projects.

## Quality Standards — NON-NEGOTIABLE

1. **Legal Precision**: Every single rule must cite its exact legal
   source — Section number, Rule number, Notification number with date,
   or Circular number. No generic statements like "as per GST law."
   If you cannot find the exact source, say so — do not fabricate.

2. **Current Law**: Rules must reflect the law AS IT STANDS TODAY,
   including all amendments. If a section was amended, cite the
   amending Act/Notification. If unsure about amendments, say so.

3. **Practitioner's Perspective**: Include practical nuances — common
   errors, frequent misinterpretations, portal-specific quirks,
   and real-world edge cases only a practitioner would know.

4. **Verification Before Finalisation**: After drafting any skill,
   ALWAYS ask me to verify every rule before considering it final.
   I will cross-check against the Act, Rules, and current Notifications.
   Never assume a draft is correct until I explicitly confirm.

## Workflow for Each Skill

### Phase 1: Research
- Search for the latest legal position on the topic
- Check for recent amendments, notifications, and circulars
- Check for relevant Supreme Court / High Court decisions
- Present research findings to me BEFORE drafting
- I will confirm, correct, or supplement your research

### Phase 2: Draft
- Create the SKILL.md file following the SKILL Template format
- Organise rules by FUNCTION (validation, calculation, classification)
  — NOT by section number
- Every rule gets a legal citation
- Include edge cases and common errors
- Include a "Limitations" section

### Phase 3: Verify
- Present complete draft to me
- I verify every rule against my professional knowledge
- Iterate until I explicitly approve
- Only after my approval is the skill considered final

### Phase 4: Python Script (if applicable)
- For rule-based skills, create a companion Python script
- Script implements every rule from the verified SKILL.md
- Must be importable as a module
- Must handle edge cases (empty input, wrong types, spaces)
- I test against real data before approving

## SKILL.md Format

Every skill follows this structure:

### YAML Frontmatter (required):
- name: gst-[topic] (lowercase, hyphenated, max 64 chars)
- description: Clear trigger description (max 200 chars)

### Markdown Body (required):
- Purpose (2-3 sentences)
- Legal Basis (table of all citations)
- Last Updated (date, basis, next review)
- Rules (grouped by function, each with rule/source/edge cases/example)
- Common Errors and Pitfalls
- Limitations
- Scripts (if applicable)
- Change Log

## What NOT to Do

- Do NOT create a skill without researching current law first
- Do NOT combine unrelated topics into one skill
- Do NOT use outdated rates, thresholds, or provisions
- Do NOT skip the verification step
- Do NOT include Python code inside SKILL.md — scripts are separate files
- Do NOT fabricate legal citations — if unsure, say "I could not verify"
```

---

# 2. PER-SKILL PROMPT

> Use this prompt to start each skill-building conversation
> inside the project. Copy and fill in the brackets.

```
I want to create the [SKILL NAME] skill.

## Scope
This skill should cover:
- [Topic area 1]
- [Topic area 2]
- [Topic area 3]

## This skill should NOT cover:
- [Out of scope item 1]
- [Out of scope item 2]

## Key sections I know are relevant:
- [Section X — brief description of relevance]
- [Section Y — brief description of relevance]
- [Rule Z — brief description of relevance]
- [Notification if known]

## Start with Phase 1: Research

Search for the current legal position on ALL aspects listed above.
Check for:
- Latest amendments to the relevant sections
- Recent notifications that change rates, thresholds, or procedures
- Relevant circulars that clarify interpretation
- Important court decisions affecting these provisions

Present your complete research findings BEFORE drafting anything.
Organise findings by topic area, not by source type.
For each finding, state the source, date, and what it means practically.
Flag anything you are uncertain about.

I will verify your research and provide corrections before we
proceed to Phase 2 (drafting).
```

---

# 3. PROMPT WORKSHOP OPENING MESSAGE

> Use this as the first message in a dedicated "Prompt Workshop" chat
> inside the project. This chat is for crafting and refining prompts —
> NOT for building actual skills.

```
This chat is my Prompt Workshop. Its purpose is to help me craft,
refine, and store prompts that I will use in OTHER chats within
this project.

## How This Chat Works

1. I describe what I want to build next (which GST skill, what it
   covers, what I know about the topic)
2. You help me craft a precise, complete prompt for that skill
3. We refine it together until I'm satisfied
4. I copy the final prompt and use it in a NEW conversation

## Rules for This Chat

- Do NOT build the actual skill here — only craft the prompt
- Do NOT research GST law here — that happens in the execution chat
- DO challenge my scope — if I'm packing too much into one skill,
  tell me to split it
- DO remind me if I'm missing something — edge cases, boundaries,
  legal sources, verification steps
- DO keep a running list of all prompts we've crafted, formatted as:

| # | Skill Name | Status | Notes |
|---|------------|--------|-------|
| 1 | gst-gstin-validator | Drafted / Refined / Used / Needs Update | |
| 2 | gst-tax-calculator | | |

## My Skill Creation Roadmap

Here are the skills I plan to build, in order:

1. gst-gstin-validator (GSTIN format, structure, checksum, state codes)
2. gst-tax-calculator (levy, rates, IGST/CGST/SGST split, RCM basics)
3. gst-invoice-rules (Rule 46, mandatory fields, e-invoicing, time of supply)
4. gst-return-mapper (GSTR-1 table classification, thresholds, field specs)
5. gst-appeal-drafter (Section 107/112, timelines, pre-deposit, demand stay)
6. [more as I identify them]

## Let's Start

Help me craft the prompt for Skill 1: gst-gstin-validator.

Here's what I know should be covered:
- [List what you know about this topic]
- [Key sections and rules]
- [Edge cases you've encountered]
- [What should be out of scope]

What am I missing? What edge cases should the prompt mention?
What scope boundaries should I set?
```

---

# 4. SKILL VERIFICATION PROMPT

> Use this after Claude presents a draft SKILL.md. Copy and fill in
> your verification notes.

```
Reviewing your draft:

- Rule [X] ([topic]): ✅ Correct
- Rule [X] ([topic]): ✅ Correct but incomplete — also add [detail]
- Rule [X] ([topic]): ❌ Wrong — [what's wrong and what the correct position is]
- Rule [X] ([topic]): ⚠️ Outdated — amended by [Notification/Act] dated [date]
- Rule [X] ([topic]): ❓ Cannot verify — need to check [source]

Additional rules to add:
- [Rule you know that Claude missed]

Edge cases to add:
- [Practical scenario Claude didn't cover]

Please update the draft with all corrections and additions.
After updating, present the revised version for final review.
```

---

# 5. SCRIPT CREATION PROMPT

> Use this after the SKILL.md is verified and approved, to create
> the companion Python script.

```
The SKILL.md for [SKILL NAME] is approved. Proceed to Phase 4.

Create a Python script called [script_name].py that implements
every verified rule from the SKILL.md. The script must:

1. Be importable as a module: `from [script_name] import [main_function]`
2. Follow ALL coding standards from my CLAUDE.md:
   - File-level documentation (PURPOSE, CONTAINS, DEPENDS ON, USED BY)
   - Function-level documentation (WHAT, WHY, CALLED BY, CALLS,
     EDGE CASES, ASSUMPTIONS, PARAMETERS, RETURNS)
   - Type hints on every function
   - Named constants (no magic numbers)
   - Consistent error handling (return dicts with success/error/data)
3. Handle edge cases: empty input, None, wrong types, spaces,
   lowercase, special characters
4. Include every rule from the SKILL.md — reference the rule number
   in comments (e.g., "# Rule 1.3: State code must be 01-38, 97, or 99")
5. NOT depend on any third-party libraries — pure Python only
   (so it works everywhere without pip install)

After creating, show me 5 test cases I can verify manually:
- 2 valid inputs with expected output
- 3 invalid inputs with expected error messages
```
