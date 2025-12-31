# Cortex Skill Writer

A Claude SKILL for creating other Claude Skills using the **Cortex Architecture** pattern.

## What is Cortex Architecture?

Cortex Architecture factors Skills into modular layers for **attention isolation**:

```
skill.md (Orchestrator)     ← Identity, routing (~75 lines)
    └── protocols/*.md      ← Logic workflows
    └── standards/*.md      ← Output formatting
    └── ontology/           ← Domain data
```

## Why Use It?

| Problem | Solution |
|---------|----------|
| Model distracted by formatting while calculating | Load standards only during reporting |
| Giant skill.md hard to maintain | Modular files, single responsibility |
| Can't swap output formats | Replace standards file without touching logic |

## Usage

Invoke this skill when you want to:
- Create a new Claude Skill from scratch
- Refactor a monolithic skill.md into Cortex pattern
- Generate boilerplate for skill components

## Files

| File | Purpose | Lines |
|------|---------|-------|
| `skill.md` | Orchestrator manifest | 92 |
| `protocols/skill_creation.md` | 6-phase creation workflow | 175 |
| `standards/skill_format.md` | YAML & Markdown conventions | 120 |
| `templates/skill_manifest.md` | Boilerplate for skill.md | 62 |
| `templates/protocol.md` | Boilerplate for protocols | 78 |
| `templates/standards.md` | Boilerplate for standards | 58 |

## Example

See the **ECIS-7 Maturity Analyst** skill in `/ecis-maturity-consultant/.ecis-analyst/` for a real-world implementation of Cortex Architecture.

## Quick Start

```
I want to create a Claude Skill for [domain].

Please use the Cortex Skill Writer to generate:
1. A skill.md manifest
2. Protocols for [workflow_1], [workflow_2]
3. Standards for [output_type]
```

---

**Version**: 1.0.0  
**Author**: Built with Cortex Architecture
