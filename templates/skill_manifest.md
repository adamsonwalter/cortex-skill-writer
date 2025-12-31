---
name: [skill-name]
description: [Under 200 chars. Describe what this skill does and when Claude should use it. Include trigger contexts like file types, task types, or domain keywords.]
---

# [Skill Name] - Claude SKILL Manifest

**Version**: 1.0.0  
**Purpose**: [One-line purpose statement]  
**Last Updated**: [YYYY-MM-DD]

---

## Architecture

| Layer | File | Load When |
|-------|------|-----------|
| **Orchestrator** | `skill.md` (this) | Always |
| **Logic** | `protocols/[workflow].md` | During [task] |
| **Presentation** | `standards/[output].md` | During reporting |

---

## Role

You are an **Expert [Role Name]** specializing in [domain/expertise].

**Prime Directive**: [Core principle]  
**Hierarchy**: [Priority order if applicable]

> *"[Memorable motto or guiding principle]"*

---

## Core Concepts

| # | Concept | Definition |
|---|---------|------------|
| 1 | **[Concept 1]** | [Brief definition] |
| 2 | **[Concept 2]** | [Brief definition] |
| 3 | **[Concept 3]** | [Brief definition] |

*Full definitions: `ontology/[file].json`*

---

## Workflow Summary

1. **[Phase 1]**: [Brief description]
2. **[Phase 2]**: [Brief description]
3. **[Phase 3]**: [Brief description]

*Full workflow: `protocols/[workflow].md`*

---

<output_discipline>
☐ [Verification check 1]
☐ [Verification check 2]
☐ [Verification check 3]
☐ [Verification check 4]
☐ [Verification check 5]
</output_discipline>

---

**END OF MANIFEST (v1.0.0)**
