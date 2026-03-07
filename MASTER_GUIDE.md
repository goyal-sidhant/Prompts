# Master Guide — How to Use Everything

> This is your map. It tells you which file to use, when, and in what order.
> Keep this handy. When in doubt, come back here.

---

## YOUR COMPLETE TOOLKIT

| # | File | What It Is | When You Use It |
|---|------|-----------|----------------|
| 1 | `rebuild_pipeline_FINAL.md` | Complete rebuild workflow | Remaking an existing tool |
| 2 | `new_project_pipeline_FINAL.md` | Complete new project workflow | Building something from scratch |
| 3 | `project_audit_prompt_v3.md` | Prompt for code archaeology | Before rebuilding — audits old code |
| 4 | `CLAUDE_TEMPLATE.md` | Coding standards + project memory | Every project — copy to project root as `CLAUDE.md` |
| 5 | `DECISIONS_LOG_TEMPLATE.md` | Auto-maintained conversation log | Every project — copy to `.build-docs/` |
| 6 | `REBUILD_BRIEF_TEMPLATE.md` | Your decisions for a rebuild | After reading an audit — before rebuilding |
| 7 | `PROJECT_BRIEF_TEMPLATE.md` | Your spec for a new project | Before starting any new tool |
| 8 | `readme_template.md` | User-facing documentation | After a tool is complete |
| 9 | `SKILL_TEMPLATE.md` | Format for Claude Code Skills | When creating GST knowledge skills |
| 10 | `SKILL_PROMPT_TEMPLATES.md` | Prompts for skill creation | When building skills in Claude.ai |

---

## WORKFLOW A: REBUILDING AN EXISTING TOOL

**Use when:** You have an old tool (code exists) and want to rebuild it better.

```
Step 1: AUDIT THE OLD CODE
├── Open Claude Code in the old project's folder
├── Paste the prompt from: project_audit_prompt_v3.md
├── Wait for the audit to complete
├── Read the output: PROJECT_AUDIT_[name].md
└── Time: 30-60 minutes

Step 2: MAKE YOUR DECISIONS
├── Read the audit (start with Sections 1, 7, 5, 11)
├── Copy REBUILD_BRIEF_TEMPLATE.md → .build-docs/REBUILD_BRIEF_[name].md
├── Fill in every section — this is YOUR thinking, not Claude's
└── Time: 30-45 minutes

Step 3: SET UP THE NEW PROJECT
├── Create a new empty folder for the rebuild
├── Copy CLAUDE_TEMPLATE.md → project root as CLAUDE.md
│   └── Fill in: project name, tech stack, remove inapplicable lines
├── Copy DECISIONS_LOG_TEMPLATE.md → .build-docs/DECISIONS_LOG.md
│   └── Just fill in the project name
├── Copy the audit file → .build-docs/
├── Copy your rebuild brief → .build-docs/
├── Create .build-docs/context/ folder
│   └── Add any sample files, templates, reference docs
└── Time: 10 minutes

Step 4: BUILD
├── Open Claude Code in the new project folder
├── Paste the Rebuild Prompt from: rebuild_pipeline_FINAL.md (Part 2)
├── Follow Steps A → G as Claude Code guides you
├── Use the mid-build templates (Part 3) when things come up
└── Time: Multiple sessions over days/weeks

Step 5: FINISH
├── Claude Code generates README.md (Step G)
├── Final git commit
└── Tool is complete
```

---

## WORKFLOW B: BUILDING A NEW TOOL FROM SCRATCH

**Use when:** No existing code. Fresh idea, fresh build.

```
Step 1: WRITE YOUR BRIEF
├── Copy PROJECT_BRIEF_TEMPLATE.md → .build-docs/PROJECT_BRIEF.md
├── Fill in every section — spend real time on this
├── Attach sample input files to .build-docs/context/
└── Time: 30-45 minutes

Step 2: SET UP THE PROJECT
├── Create a new folder for the project
├── Copy CLAUDE_TEMPLATE.md → project root as CLAUDE.md
│   └── Fill in: project name, tech stack, remove rebuild-specific lines
├── Copy DECISIONS_LOG_TEMPLATE.md → .build-docs/DECISIONS_LOG.md
└── Time: 10 minutes

Step 3: BUILD
├── Open Claude Code in the project folder
├── Paste the Build Prompt from: new_project_pipeline_FINAL.md (Part 2)
├── Follow Steps A → G as Claude Code guides you
├── Use mid-build templates (Part 3) when things come up
└── Time: Multiple sessions over days/weeks

Step 4: FINISH
├── Claude Code generates README.md (Step G)
├── Final git commit
└── Tool is complete
```

---

## WORKFLOW C: CREATING GST SKILLS

**Use when:** Building your reusable GST knowledge library.

```
Step 1: SET UP THE PROJECT IN CLAUDE.AI
├── Go to Claude.ai → Projects → Create New Project
├── Name: "GST Skills Library"
├── Paste Project Instructions from: SKILL_PROMPT_TEMPLATES.md (Section 1)
└── Time: 5 minutes

Step 2: OPEN THE PROMPT WORKSHOP
├── Start a new chat inside the project
├── Paste the Prompt Workshop message from: SKILL_PROMPT_TEMPLATES.md (Section 3)
├── Craft prompts for each skill before building
└── This chat stays open permanently — keeps growing

Step 3: BUILD EACH SKILL (one chat per skill)
├── Start a NEW chat inside the project
├── Paste the Per-Skill Prompt from: SKILL_PROMPT_TEMPLATES.md (Section 2)
├── Phase 1: Claude researches → you verify research
├── Phase 2: Claude drafts SKILL.md → you verify every rule
│   └── Use Verification Prompt: SKILL_PROMPT_TEMPLATES.md (Section 4)
├── Phase 3: Iterate until you approve
├── Phase 4: Claude creates Python script
│   └── Use Script Prompt: SKILL_PROMPT_TEMPLATES.md (Section 5)
├── Save final files using SKILL_TEMPLATE.md format
└── Time: 1-2 hours per skill

Step 4: INSTALL THE SKILL
├── Create folder: ~/.claude/skills/gst-[name]/
├── Copy SKILL.md into the folder
├── Copy reference .md files into the folder
├── Copy .py scripts into the folder
├── Restart Claude Code — skill is now active globally
└── Time: 2 minutes

RECOMMENDED BUILD ORDER:
1. gst-gstin-validator (most self-contained, immediately useful)
2. gst-tax-calculator (builds on state code knowledge from #1)
3. gst-invoice-rules (builds on both #1 and #2)
4. gst-return-mapper (needs all three above)
5. gst-appeal-drafter (most complex, benefits from practice)
```

---

## WORKFLOW D: CONTINUING A PREVIOUS SESSION

**Use when:** Starting a new Claude Code chat to continue a project.

```
├── Open Claude Code in the project folder
├── Paste Template 6 (Session Handover) from the pipeline you're using
├── Fill in: what was done last, current state, what to do next
├── Claude Code reads CLAUDE.md and DECISIONS_LOG.md automatically
└── Continue building
```

---

## WORKFLOW E: AUDITING A COMPLETED TOOL (for future reference)

**Use when:** Tool is done, you want to document it for posterity.

```
├── Open Claude Code in the project folder
├── Paste the prompt from: project_audit_prompt_v3.md
├── The audit captures everything — features, rules, edge cases, git history
├── Save the audit alongside the project
└── If you ever need to rebuild, the audit + rebuild brief pipeline is ready
```

---

## QUICK REFERENCE: WHICH TEMPLATE FOR WHICH SITUATION

| Situation | Use This |
|-----------|----------|
| "I want to rebuild my old Sahaj app" | Workflow A (Rebuild) |
| "I have an idea for a new ITC reconciliation tool" | Workflow B (New Project) |
| "I want to create reusable GST validation rules" | Workflow C (Skills) |
| "I'm continuing yesterday's build session" | Workflow D (Session Handover) |
| "My tool is done, I want to document it" | Workflow E (Audit for reference) |
| "Something broke during testing" | Template 3 (Bug Report) |
| "I want to change how a feature works" | Template 2 (Change Request) |
| "I just thought of a new feature" | Template 1 (New Feature) or Template 5 (Parking Lot) |
| "Claude built something I don't understand" | Template 7 (Explain Differently) |
| "We need to choose between two approaches" | Template 4 (Design Decision) |

---

## FOLDER STRUCTURE WHEN EVERYTHING IS SET UP

```
my-project/
├── CLAUDE.md                           ← From CLAUDE_TEMPLATE.md
├── README.md                           ← Generated at the end (Step G)
├── main.py                             ← Your tool's entry point
├── config/                             ← Settings, constants, mappings
├── core/                               ← Business logic
├── models/                             ← Data structures
├── readers/                            ← Input handling
├── writers/                            ← Output generation
├── gui/                                ← Interface
├── utils/                              ← Shared helpers
│
└── .build-docs/                        ← Builder documents (not user-facing)
    ├── PROJECT_AUDIT_[name].md         ← (Rebuild only) Audit of old code
    ├── REBUILD_BRIEF_[name].md         ← (Rebuild only) Your decisions
    ├── PROJECT_BRIEF.md                ← (New project only) Your spec
    ├── DECISIONS_LOG.md                ← Auto-maintained conversation log
    └── context/                        ← Reference materials
        ├── sample_input.xlsx
        ├── portal_template.xlsx
        └── [other reference files]
```

```
~/.claude/skills/                       ← Your global Skills library
├── gst-gstin-validator/
│   ├── SKILL.md
│   ├── gstin_rules.md
│   └── validate_gstin.py
├── gst-tax-calculator/
│   ├── SKILL.md
│   ├── tax_rates.md
│   └── calculate_tax.py
├── gst-invoice-rules/
│   ├── SKILL.md
│   └── invoice_rules.md
├── gst-return-mapper/
│   ├── SKILL.md
│   ├── return_tables.md
│   └── classify_invoice.py
└── gst-appeal-drafter/
    ├── SKILL.md
    └── appeal_rules.md
```

---

## MAINTENANCE REMINDERS

| When | What to Do |
|------|-----------|
| After every GST Council meeting | Review skills for rate/threshold changes |
| After every Finance Act | Check for section amendments in all skills |
| After completing a project | Run the audit prompt for documentation |
| After each build session | Verify DECISIONS_LOG was updated |
| Every 30 DECISIONS_LOG entries | Summarise older entries, archive to separate file |
| When starting a new project | Copy fresh templates — never reuse filled ones |
