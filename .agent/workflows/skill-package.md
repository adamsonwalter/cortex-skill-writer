---
description: Prepare and package a Claude Skill for upload (clean archive, frontmatter check, file count limit)
---

This workflow automates the preparation of a Claude Skill for upload to Claude.ai, ensuring compliance with file count limits and YAML frontmatter rules.

### Steps

1. **Locate Skill root**
   Identify the directory containing `skill.md`.

2. **Verify YAML Frontmatter**
   Check `skill.md` for allowed keys: `name`, `description`, `license`, `allowed-tools`, `compatibility`, `metadata`.
   // turbo
   3. Run frontmatter validation:
   ```bash
   # Check for invalid keys (like version) in frontmatter
   head -n 10 skill.md | grep -E "^version:|^dependencies:"
   ```

4. **Check File Count**
   Ensure the total file count (excluding .git) is under 200.
   // turbo
   5. Count files:
   ```bash
   find . -type f -not -path '*/.*' | wc -l
   ```

6. **Create Clean Archive**
   // turbo
   7. Generate zip on Desktop:
   ```bash
   SKILL_NAME=$(grep "^name:" skill.md | cut -d' ' -f2)
   zip -r ~/Desktop/${SKILL_NAME}.zip . -x ".git/*" -x "*.DS_Store" -x "__pycache__/*" -x "*.pyc"
   ```

8. **Final Verification**
   // turbo
   9. Verify the zip:
   ```bash
   unzip -l ~/Desktop/${SKILL_NAME}.zip | tail -1
   ```
