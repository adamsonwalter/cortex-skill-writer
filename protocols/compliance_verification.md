# Compliance Verification Protocol

**Version**: 1.0.0  
**Purpose**: Self-verifying report to ensure Claude Skill Template compliance  
**Called By**: skill.md Orchestrator at skill generation completion  
**Last Updated**: 2025-12-31

---

## Overview

Every generated skill MUST pass a compliance verification before delivery. This protocol generates a self-verifying report.

---

## Verification Checklist

### A. YAML Frontmatter (REQUIRED)

| Check | Requirement | Verification |
|-------|-------------|--------------|
| `name` | lowercase-hyphenated, ≤64 chars | `grep "^name:" skill.md` |
| `description` | ≤200 chars, includes trigger contexts | Manual review |
| `version` | Semantic versioning (X.Y.Z) | `grep "^version:" skill.md` |

### B. Structural Requirements

| Check | Requirement | Target |
|-------|-------------|--------|
| H1 heading | Exactly one per file | ✅ |
| H2/H3 hierarchy | Proper nesting | ✅ |
| `skill.md` length | ≤100 lines | ✅ |
| Total skill length | ≤600 lines | ✅ |

### C. Required Sections

| Section | Location | Status |
|---------|----------|--------|
| YAML Frontmatter | `skill.md` lines 1-5 | ☐ |
| Role Definition | `skill.md` | ☐ |
| Architecture Table | `skill.md` | ☐ |
| Output Discipline | `skill.md` | ☐ |
| Protocols | `protocols/*.md` | ☐ |
| Standards | `standards/*.md` | ☐ |

### D. Cortex Architecture Compliance

| Check | Requirement | Status |
|-------|-------------|--------|
| Manifest ≤100 lines | `skill.md` is lightweight | ☐ |
| Logic extracted | `protocols/` exists | ☐ |
| Presentation extracted | `standards/` exists | ☐ |
| Progressive disclosure | External file references | ☐ |

### E. Translation Layer (If Applicable)

| Check | Requirement | Status |
|-------|-------------|--------|
| No hardcoded constants | All in `shared_registry.py` | ☐ |
| Terminology registry | `translation/terminology_registry.json` | ☐ |
| Hooks implemented | `translation/hooks.py` | ☐ |

---

## Verification Report Template

Generate this report at skill completion:

```markdown
# Skill Compliance Report

**Skill Name**: [name]  
**Generated**: [timestamp]  
**Status**: [PASS / FAIL / WARNINGS]

---

## Summary

| Category | Checks | Passed | Failed |
|----------|--------|--------|--------|
| YAML Frontmatter | 3 | X | Y |
| Structure | 4 | X | Y |
| Required Sections | 6 | X | Y |
| Cortex Architecture | 4 | X | Y |
| Translation Layer | 3 | X | Y |
| **TOTAL** | 20 | X | Y |

---

## Detailed Results

### ✅ Passed
- [Check 1]
- [Check 2]

### ❌ Failed
- [Check]: [Reason]

### ⚠️ Warnings
- [Warning]: [Recommendation]

---

## File Metrics

| File | Lines | Target | Status |
|------|-------|--------|--------|
| skill.md | XX | ≤100 | ✅/❌ |
| protocols/*.md | XX | ≤300 | ✅/❌ |
| standards/*.md | XX | ≤200 | ✅/❌ |
| **Total** | XXX | ≤600 | ✅/❌ |

---

## Recommendation

[DEPLOY / FIX REQUIRED ITEMS / REVIEW WARNINGS]
```

---

## Automated Verification Script

When code deployment is beneficial, generate this script:

```python
#!/usr/bin/env python3
"""
Skill Compliance Verifier
Automatically checks Claude Skill Template compliance.
"""

import os
import re
from pathlib import Path

def verify_skill(skill_dir: str) -> dict:
    """Verify skill compliance and return report."""
    results = {
        "passed": [],
        "failed": [],
        "warnings": [],
        "metrics": {}
    }
    
    skill_path = Path(skill_dir)
    skill_md = skill_path / "skill.md"
    
    # Check 1: skill.md exists
    if not skill_md.exists():
        results["failed"].append("skill.md not found")
        return results
    
    content = skill_md.read_text()
    lines = content.split("\n")
    
    # Check 2: YAML frontmatter
    if content.startswith("---"):
        results["passed"].append("YAML frontmatter present")
        
        # Check name
        name_match = re.search(r'^name:\s*(.+)$', content, re.MULTILINE)
        if name_match:
            name = name_match.group(1).strip()
            if len(name) <= 64 and name == name.lower():
                results["passed"].append(f"name valid: {name}")
            else:
                results["failed"].append(f"name invalid: {name}")
        else:
            results["failed"].append("name field missing")
            
        # Check version
        version_match = re.search(r'^version:\s*(.+)$', content, re.MULTILINE)
        if version_match:
            results["passed"].append(f"version present: {version_match.group(1)}")
        else:
            results["failed"].append("version field missing")
    else:
        results["failed"].append("YAML frontmatter missing")
    
    # Check 3: Line count
    line_count = len(lines)
    results["metrics"]["skill_md_lines"] = line_count
    if line_count <= 100:
        results["passed"].append(f"skill.md {line_count} lines (≤100)")
    else:
        results["warnings"].append(f"skill.md {line_count} lines (target ≤100)")
    
    # Check 4: Protocols directory
    protocols_dir = skill_path / "protocols"
    if protocols_dir.exists() and any(protocols_dir.glob("*.md")):
        results["passed"].append("protocols/ exists with content")
    else:
        results["failed"].append("protocols/ missing or empty")
    
    # Check 5: Standards directory
    standards_dir = skill_path / "standards"
    if standards_dir.exists() and any(standards_dir.glob("*.md")):
        results["passed"].append("standards/ exists with content")
    else:
        results["failed"].append("standards/ missing or empty")
    
    return results

if __name__ == "__main__":
    import sys
    skill_dir = sys.argv[1] if len(sys.argv) > 1 else "."
    results = verify_skill(skill_dir)
    
    print("=" * 60)
    print("SKILL COMPLIANCE REPORT")
    print("=" * 60)
    
    print(f"\n✅ PASSED ({len(results['passed'])})")
    for item in results["passed"]:
        print(f"   - {item}")
    
    if results["failed"]:
        print(f"\n❌ FAILED ({len(results['failed'])})")
        for item in results["failed"]:
            print(f"   - {item}")
    
    if results["warnings"]:
        print(f"\n⚠️ WARNINGS ({len(results['warnings'])})")
        for item in results["warnings"]:
            print(f"   - {item}")
    
    print("\n" + "=" * 60)
    status = "PASS" if not results["failed"] else "FAIL"
    print(f"STATUS: {status}")
```

---

## When to Run

1. **After Phase 5** (Standards Writing) in skill creation
2. **Before delivery** to user
3. **After any modifications** to skill files

---

**END OF PROTOCOL**
