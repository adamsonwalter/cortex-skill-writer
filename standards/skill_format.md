# Skill File Format Standards

**Version**: 1.0.0  
**Purpose**: Formatting rules for generated Skill files  
**Called By**: skill.md Orchestrator  
**Last Updated**: 2025-12-31

---

## YAML Frontmatter Format

```yaml
---
name: [lowercase-hyphenated-name]
description: [Under 200 chars. Include trigger words, file types, task contexts.]
license: [optional, e.g., MIT]
allowed-tools: [optional, list of tools skill can use]
compatibility: [optional, version compatibility]
metadata: [optional, object for additional metadata like version]
---
```

### Allowed Frontmatter Keys (STRICT)

| Key | Required | Description |
|-----|----------|-------------|
| `name` | ✅ | Skill identifier (lowercase, hyphens) |
| `description` | ✅ | What skill does, when to use |
| `license` | ❌ | License type |
| `allowed-tools` | ❌ | Tool restrictions |
| `compatibility` | ❌ | Version compatibility |
| `metadata` | ❌ | Additional metadata object |

> ⚠️ **CRITICAL**: `version` is NOT an allowed key. Put version in `metadata` or in the markdown body.
- Lowercase letters only
- Hyphens for word separation
- No underscores, spaces, or special characters
- Maximum 64 characters

### Description Rules
- Under 200 characters recommended
- Include trigger contexts (e.g., "Use when...")
- Include key capabilities
- Include domain keywords for discoverability

---

## Markdown Formatting

### Heading Hierarchy

| Level | Usage |
|-------|-------|
| H1 (`#`) | Document title only (one per file) |
| H2 (`##`) | Major sections |
| H3 (`###`) | Subsections |
| H4 (`####`) | Rarely used; prefer bullets |

### Tables

Use tables for:
- Configuration options
- Mappings
- Checklists
- Comparisons

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Value 1 | Value 2 | Value 3 |
```

### Code Blocks

Use fenced code blocks with language identifiers:

````markdown
```yaml
key: value
```

```json
{"key": "value"}
```

```python
def example():
    pass
```
````

---

## Special Sections

### XML Tags for Behavioral Sections

```markdown
<analytical_paradigm>
[Core reasoning principles]
</analytical_paradigm>

<output_discipline>
[Pre-output checklist]
</output_discipline>
```

### Blockquotes for Mottos/Emphasis

```markdown
> *"Memorable principle or motto."*
```

---

## File Length Guidelines

| File Type | Target | Maximum |
|-----------|--------|---------|
| `skill.md` (manifest) | 60-80 lines | 100 lines |
| `protocols/*.md` | 100-200 lines | 300 lines |
| `standards/*.md` | 80-150 lines | 200 lines |
| Total per skill | 300-500 lines | 600 lines |

---

## Directory Naming

| Directory | Purpose | Naming |
|-----------|---------|--------|
| `protocols/` | Logic workflows | `[action]_[noun].md` |
| `standards/` | Output formats | `[output_type]_standards.md` |
| `ontology/` | Domain definitions | `[domain]_[type].json` |
| `knowledge/` | Reference data | `[source]_[topic].json` |
| `templates/` | Boilerplate | `[template_name].md` |
| `examples/` | Usage examples | `[example_name].md` |

---

## Version Numbering

Use Semantic Versioning:

| Version | When to Increment |
|---------|-------------------|
| MAJOR (X.0.0) | Breaking changes to skill behavior |
| MINOR (0.X.0) | New capabilities added |
| PATCH (0.0.X) | Bug fixes, clarifications |

---

**END OF STANDARDS**
