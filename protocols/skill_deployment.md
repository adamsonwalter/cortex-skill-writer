# Skill Deployment Protocol

**Version**: 1.0.0  
**Purpose**: Package skills correctly for Claude.ai upload  
**Called By**: skill.md Orchestrator at deployment  
**Last Updated**: 2025-12-31

---

## The Problem

Claude Skills have a **200 file limit**. Git repositories contain hundreds of internal files that are NOT part of the skill.

```
my-skill-repo/
├── .git/           ← 200+ files (Git internal database)
├── my-skill/       ← 40 files (THE ACTUAL SKILL)
│   └── skill.md
└── .gitignore
```

**If you zip the repo folder, you include `.git/` and exceed 200 files.**

---

## Correct Packaging

### Option 1: Zip Only the Skill Folder

```bash
cd /path/to/repo
zip -r ~/Desktop/my-skill.zip my-skill-folder/ -x "*.DS_Store"
```

### Option 2: Use Packaging Script

Create `package-skill.sh` in skill root:

```bash
#!/bin/bash
# Package skill for Claude.ai upload

SKILL_NAME="my-skill"
OUTPUT="$HOME/Desktop/${SKILL_NAME}.zip"

# Remove old zip
rm -f "$OUTPUT"

# Create zip excluding git and system files
zip -r "$OUTPUT" . \
    -x ".git/*" \
    -x ".DS_Store" \
    -x "__pycache__/*" \
    -x "*.pyc" \
    -x ".env" \
    -x "package-skill.sh"

# Verify
echo "Created: $OUTPUT"
unzip -l "$OUTPUT" | tail -1
```

---

## Deployment Checklist

Before uploading a skill to Claude.ai:

| # | Check | Command | Expected |
|---|-------|---------|----------|
| 1 | File count < 200 | `unzip -l skill.zip \| tail -1` | < 200 files |
| 2 | No `.git/` included | `unzip -l skill.zip \| grep -c ".git/"` | 0 |
| 3 | `skill.md` in root | `unzip -l skill.zip \| grep "skill.md"` | Found |
| 4 | YAML frontmatter valid | Manual check | Only allowed keys |

---

## Directory Structure Best Practice

### ❌ BAD: Skill at repo root

```
my-repo/           ← Git repo
├── .git/          ← Will contaminate zip
├── skill.md       ← At root
└── protocols/
```

### ✅ GOOD: Skill in subfolder

```
my-repo/           ← Git repo
├── .git/          ← Stays here
└── .my-skill/     ← Zip THIS folder only
    ├── skill.md
    └── protocols/
```

### ✅ BEST: Hidden skill folder

Use a dot-prefix (e.g., `.my-skill/`) so the skill folder is hidden from casual view but clearly separated from git.

---

## Why `.git/` Has So Many Files

| Git Component | What It Stores | File Count |
|---------------|----------------|------------|
| `.git/objects/` | Every version of every file | 100+ |
| `.git/refs/` | Branch and tag pointers | 10+ |
| `.git/logs/` | History of all ref changes | 10+ |
| `.git/hooks/` | Git hook scripts | 10+ |

**Every commit creates new object files. After many commits, this accumulates to 200+ files.**

---

## Integration with Compliance Verification

Add to `protocols/compliance_verification.md` checklist:

```markdown
### F. Deployment Readiness

| Check | Requirement | Status |
|-------|-------------|--------|
| File count | < 200 | ☐ |
| No .git | 0 matches | ☐ |
| No .DS_Store | 0 matches | ☐ |
| skill.md in zip root | Found | ☐ |
```

---

**END OF PROTOCOL**
