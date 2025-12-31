# Skill Creation Protocol

**Version**: 1.0.0  
**Purpose**: Step-by-step workflow for creating Cortex-architected Skills  
**Called By**: skill.md Orchestrator  
**Last Updated**: 2025-12-31

---

## Phase 1: Requirements Gathering

### Questions to Answer

| # | Question | Purpose |
|---|----------|---------|
| 1 | What domain expertise does this Skill provide? | Role definition |
| 2 | What are the 3-5 core workflows? | Protocol extraction |
| 3 | What output formats are expected? | Standard definition |
| 4 | What domain data is needed? | Resource planning |
| 5 | Who is the target user? | Tone/complexity calibration |

### Output
```yaml
skill_name: [lowercase-hyphenated]
domain: [expertise area]
workflows:
  - [workflow_1]
  - [workflow_2]
  - [workflow_3]
outputs:
  - [format_1]
  - [format_2]
resources:
  - [ontology/data files]
audience: [technical/executive/mixed]
```

---

## Phase 2: Directory Structure Creation

Create the following structure:

```
[skill-name]/
├── skill.md                    # Orchestrator (~75 lines)
├── protocols/
│   ├── [workflow_1].md        # Logic module 1
│   └── [workflow_2].md        # Logic module 2
├── standards/
│   └── output_format.md       # Presentation rules
├── ontology/                   # Domain definitions (if needed)
├── knowledge/                  # Reference data (if needed)
├── templates/                  # Boilerplate files (if needed)
└── examples/                   # Usage examples (if needed)
```

---

## Phase 3: Manifest Writing (skill.md)

### Required Sections

1. **YAML Frontmatter** (4 lines)

> ⚠️ **ALLOWED KEYS ONLY**: `name`, `description`, `license`, `allowed-tools`, `compatibility`, `metadata`
> **NOT ALLOWED**: `version`, `dependencies`, or any other keys

```yaml
---
name: [skill-name]
description: [Under 200 chars. Include trigger contexts.]
---
```

2. **Title & Metadata** (5 lines)
```markdown
# [Skill Name] - Claude SKILL Manifest

**Version**: 1.0.0  
**Purpose**: [One-line purpose]  
**Last Updated**: [Date]
```

3. **Architecture Table** (10 lines)
```markdown
## Architecture

| Layer | File | Load When |
|-------|------|-----------|
| **Orchestrator** | `skill.md` | Always |
| **Logic** | `protocols/[name].md` | During [task] |
| **Presentation** | `standards/[name].md` | During reporting |
```

4. **Role Definition** (8 lines)
```markdown
## Role

You are an **Expert [Role]** specializing in [domain].

**Prime Directive**: [Core principle]

> *"[Memorable motto]"*
```

5. **Domain Summary** (10-20 lines)
- Key concepts table or list
- Reference to full ontology

6. **Output Discipline** (8 lines)
```markdown
<output_discipline>
☐ [Checklist item 1]
☐ [Checklist item 2]
☐ [Checklist item 3]
</output_discipline>
```

### Target: 60-80 lines total

---

## Phase 4: Protocol Writing

For each workflow, create a protocol file:

### Protocol Template

```markdown
# [Workflow Name] Protocol

**Version**: 1.0.0  
**Purpose**: [What this protocol does]  
**Called By**: skill.md Orchestrator  
**Last Updated**: [Date]

---

## Stage 1: [First Stage Name]

### Purpose
[Why this stage exists]

### Your Task
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Output
```json
{
  "field": "value"
}
```

---

## Stage 2: [Second Stage Name]

[Repeat pattern...]

---

**END OF PROTOCOL**
```

---

## Phase 5: Standards Writing

### Standards Template

```markdown
# [Output Type] Standards

**Version**: 1.0.0  
**Purpose**: [Formatting rules for this output]  
**Called By**: skill.md Orchestrator  
**Last Updated**: [Date]

---

## Output Structure

[Describe the expected format]

---

## Required Elements

| Element | Description | Example |
|---------|-------------|---------|
| [Element 1] | [What it is] | [Example] |

---

## Formatting Rules

- [Rule 1]
- [Rule 2]
- [Rule 3]

---

**END OF STANDARDS**
```

---

## Phase 6: Validation Checklist

Before finalizing, verify:

| # | Check | Status |
|---|-------|--------|
| 1 | `skill.md` has YAML frontmatter | ☐ |
| 2 | `skill.md` under 100 lines | ☐ |
| 3 | All protocols in `protocols/` | ☐ |
| 4 | All standards in `standards/` | ☐ |
| 5 | Architecture table routes to all files | ☐ |
| 6 | Role definition is clear | ☐ |
| 7 | Output discipline checklist present | ☐ |

---

**END OF PROTOCOL**
