# Testing & Verification Protocol

**Version**: 1.0.0  
**Purpose**: Code testing and self-verification techniques for generated Skills  
**Called By**: skill.md Orchestrator when code is deployed  
**Last Updated**: 2025-12-31

---

## Overview

When a Skill includes executable code, deploy these testing techniques to ensure correctness and maintainability.

---

## Technique 1: End-to-End Pipeline Test

**Purpose**: Verify all stages execute correctly in sequence.

### When to Deploy
- Skill has multiple processing stages
- Pipeline architecture (Stage 1 → Stage 2 → ... → Stage N)

### Template

```python
#!/usr/bin/env python3
"""
End-to-End Test - [Skill Name]

Verifies all stages execute correctly.

Version: 1.0.0
Last Updated: [Date]
"""

import sys
from pathlib import Path

# Add module directory to path
MODULE_DIR = Path(__file__).parent
sys.path.insert(0, str(MODULE_DIR))

# Import all stages
from stage1_xxx import main_stage1
from stage2_xxx import main_stage2
# ... additional stages

def run_end_to_end_test():
    """Run complete pipeline test."""
    
    print("=" * 70)
    print(" [SKILL NAME] - END-TO-END TEST")
    print("=" * 70)
    
    # Sample input
    sample_input = """[Sample data for testing]"""
    
    # Stage 1
    print("\n" + "-" * 70)
    print("STAGE 1: [Stage Name]")
    print("-" * 70)
    
    result1 = main_stage1(sample_input)
    print(f"✓ Stage 1 complete: [verification metric]")
    
    # Stage 2
    print("\n" + "-" * 70)
    print("STAGE 2: [Stage Name]")
    print("-" * 70)
    
    result2 = main_stage2(result1)
    print(f"✓ Stage 2 complete: [verification metric]")
    
    # Continue for all stages...
    
    # Summary
    print("\n" + "=" * 70)
    print(" END-TO-END TEST COMPLETE ✓")
    print("=" * 70)
    
    return True

if __name__ == "__main__":
    success = run_end_to_end_test()
    sys.exit(0 if success else 1)
```

---

## Technique 2: Import Validation

**Purpose**: Verify all modules import correctly before execution.

### When to Deploy
- Skill has multiple Python modules
- Shared dependencies between modules

### Template

```python
def validate_imports():
    """Verify all required modules can be imported."""
    
    required_modules = [
        "stage1_xxx",
        "stage2_xxx",
        "shared_registry",
        "hooks"
    ]
    
    print("Validating imports...")
    
    for module in required_modules:
        try:
            __import__(module)
            print(f"  ✓ {module}")
        except ImportError as e:
            print(f"  ✗ {module}: {e}")
            return False
    
    return True
```

---

## Technique 3: Constant Registry Verification

**Purpose**: Ensure all constants are loaded from registry, not hardcoded.

### When to Deploy
- Skill uses shared_registry.py for constants
- Translation layer architecture is implemented

### Template

```python
def verify_constants():
    """Verify constants loaded from shared_registry."""
    
    from shared_registry import (
        CONSTANT_GROUP_1,
        CONSTANT_GROUP_2,
        # ... all expected constants
    )
    
    print("Verifying constants registry...")
    
    required_constants = {
        "CONSTANT_GROUP_1": CONSTANT_GROUP_1,
        "CONSTANT_GROUP_2": CONSTANT_GROUP_2,
    }
    
    for name, value in required_constants.items():
        if value is None or value == {}:
            print(f"  ✗ {name}: Empty or None")
            return False
        print(f"  ✓ {name}: {type(value).__name__}")
    
    return True
```

---

## Technique 4: Terminology Resolution Test

**Purpose**: Verify alias resolution works correctly.

### When to Deploy
- Skill has terminology_registry.json
- TerminologyHook is implemented

### Template

```python
def test_terminology_resolution():
    """Test alias resolution from terminology registry."""
    
    from hooks import TerminologyHook
    
    hook = TerminologyHook()
    
    test_cases = [
        # (input_alias, expected_canonical)
        ("alias1", "CANONICAL_TERM_1"),
        ("Alias 2", "CANONICAL_TERM_2"),
        ("unknown", "unknown"),  # Should return as-is
    ]
    
    print("Testing terminology resolution...")
    
    for alias, expected in test_cases:
        result = hook.resolve(alias)
        if result == expected:
            print(f"  ✓ '{alias}' → '{result}'")
        else:
            print(f"  ✗ '{alias}' → '{result}' (expected '{expected}')")
            return False
    
    return True
```

---

## Technique 5: Grep Verification (Post-Refactor)

**Purpose**: Search codebase for deprecated or incorrect terminology after refactoring.

### When to Deploy
- After renaming terms (e.g., NEM-7 → ECIS-7)
- After updating constant names

### Command Pattern

```bash
# Find all occurrences of deprecated term
grep -r "DEPRECATED_TERM" --include="*.py" --include="*.json" --include="*.md"

# Verify new term is used
grep -r "NEW_TERM" --include="*.py" | wc -l

# Check for hardcoded magic numbers
grep -rn "= 0\." --include="*.py" | grep -v "shared_registry"
```

### Python Wrapper

```python
import subprocess

def grep_verify(pattern: str, expected_count: int = 0) -> bool:
    """Verify grep pattern matches expected count."""
    
    result = subprocess.run(
        ["grep", "-r", pattern, "--include=*.py", "--include=*.json"],
        capture_output=True,
        text=True
    )
    
    matches = len(result.stdout.strip().split("\n")) if result.stdout else 0
    
    if matches == expected_count:
        print(f"  ✓ '{pattern}': {matches} occurrences (expected {expected_count})")
        return True
    else:
        print(f"  ✗ '{pattern}': {matches} occurrences (expected {expected_count})")
        return False
```

---

## Technique 6: Exit Code Validation

**Purpose**: Return proper exit codes for CI/CD integration.

### When to Deploy
- Always for test scripts
- When skill will be used in automated pipelines

### Template

```python
if __name__ == "__main__":
    try:
        success = run_all_tests()
        if success:
            print("\n✅ ALL TESTS PASSED")
            sys.exit(0)
        else:
            print("\n❌ TESTS FAILED")
            sys.exit(1)
    except Exception as e:
        print(f"\n💥 EXCEPTION: {e}")
        sys.exit(2)
```

---

## Technique 7: Schema Validation

**Purpose**: Verify JSON knowledge bases match expected schema.

### When to Deploy
- Skill uses JSON knowledge bases
- Data structure is critical for processing

### Template

```python
def validate_json_schema(filepath: str, required_keys: list) -> bool:
    """Validate JSON file has required keys."""
    
    import json
    from pathlib import Path
    
    path = Path(filepath)
    
    if not path.exists():
        print(f"  ✗ {filepath}: File not found")
        return False
    
    with open(path) as f:
        data = json.load(f)
    
    missing = [k for k in required_keys if k not in data]
    
    if missing:
        print(f"  ✗ {filepath}: Missing keys: {missing}")
        return False
    
    print(f"  ✓ {filepath}: Schema valid")
    return True
```

---

## Master Test Script Template

Deploy this script as `scripts/run_tests.py`:

```python
#!/usr/bin/env python3
"""
Master Test Suite - [Skill Name]

Runs all verification tests.
"""

import sys
from pathlib import Path

# Add paths
ROOT = Path(__file__).parent.parent
sys.path.insert(0, str(ROOT / "algorithms"))
sys.path.insert(0, str(ROOT / "translation"))

def run_all_tests():
    """Execute all test suites."""
    
    results = []
    
    print("=" * 70)
    print(" [SKILL NAME] - TEST SUITE")
    print("=" * 70)
    
    # Test 1: Import Validation
    print("\n[1/5] Import Validation")
    results.append(validate_imports())
    
    # Test 2: Constants Registry
    print("\n[2/5] Constants Registry")
    results.append(verify_constants())
    
    # Test 3: Terminology Resolution
    print("\n[3/5] Terminology Resolution")
    results.append(test_terminology_resolution())
    
    # Test 4: Schema Validation
    print("\n[4/5] Schema Validation")
    results.append(validate_schemas())
    
    # Test 5: End-to-End Pipeline
    print("\n[5/5] End-to-End Pipeline")
    results.append(run_end_to_end_test())
    
    # Summary
    passed = sum(results)
    total = len(results)
    
    print("\n" + "=" * 70)
    print(f" RESULTS: {passed}/{total} PASSED")
    print("=" * 70)
    
    return all(results)

if __name__ == "__main__":
    success = run_all_tests()
    sys.exit(0 if success else 1)
```

---

## Deployment Checklist

When generating a skill with code, include:

| Test Type | Script | Required? |
|-----------|--------|-----------|
| Import Validation | In master test | ✅ Always |
| Constants Registry | In master test | ✅ If constants exist |
| Terminology Resolution | In master test | ✅ If hooks exist |
| Schema Validation | In master test | ✅ If JSON knowledge bases |
| End-to-End Pipeline | In master test | ✅ If multi-stage |
| Grep Verification | Manual/CI | ⚠️ After refactoring |

---

**END OF PROTOCOL**
