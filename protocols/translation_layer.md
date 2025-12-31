# Translation Layer Architecture Protocol

**Version**: 1.0.0  
**Purpose**: Design pattern for decoupling constants/variables from code via registries and hooks  
**Called By**: skill.md Orchestrator during skill generation  
**Last Updated**: 2025-12-31

---

## Overview

The Translation Layer pattern separates **domain knowledge** (constants, terminology, mappings) from **code logic**. Changes to domain knowledge do NOT require code changes.

```
┌─────────────────────────────────────────┐
│  KNOWLEDGE (JSON)                       │  ← Domain experts edit here
│  terminology_registry.json              │
│  constants.json                         │
├─────────────────────────────────────────┤
│  TRANSLATION LAYER (Python)             │  ← Hooks load & resolve
│  hooks.py                               │
├─────────────────────────────────────────┤
│  APPLICATION CODE                       │  ← Calls hooks, never hardcodes
│  algorithms/*.py                        │
└─────────────────────────────────────────┘
```

---

## Component 1: Terminology Registry

**File**: `translation/terminology_registry.json`

### Purpose
Maps aliases, synonyms, and external terms to canonical internal names.

### Structure
```json
{
  "registry_metadata": {
    "description": "[Skill Name] Terminology Registry",
    "version": "1.0.0"
  },
  "canonical_terms": {
    "TERM_1": {
      "description": "[What it means]",
      "aliases": ["alias1", "alias2", "Alias 1"],
      "external_mappings": {
        "external_system": "their_term"
      }
    }
  }
}
```

### Usage
```python
from hooks import TerminologyHook
hook = TerminologyHook()
canonical = hook.resolve("alias1")  # Returns "TERM_1"
```

---

## Component 2: Shared Registry (Constants)

**File**: `algorithms/shared_registry.py`

### Purpose
Centralize all magic numbers, weights, coefficients, and thresholds.

### Structure
```python
"""
Shared Registry - [Skill Name]
Single source of truth for constants.
"""

# =============================================================================
# DOMAIN CONSTANTS
# =============================================================================

CONSTANT_GROUP_1 = {
    "constant_a": 0.75,
    "constant_b": 100,
    "description": "What these represent"
}

CONSTANT_GROUP_2 = {
    "threshold": 0.5,
    "multiplier": 1.2
}

# =============================================================================
# DERIVED FROM KNOWLEDGE BASE
# =============================================================================

def load_from_knowledge(knowledge_file: str) -> dict:
    """Load constants from JSON knowledge base."""
    import json
    from pathlib import Path
    kb_path = Path(__file__).parent.parent / "knowledge" / knowledge_file
    with open(kb_path) as f:
        return json.load(f)
```

### Anti-Pattern (AVOID)
```python
# BAD: Hardcoded magic numbers scattered in code
score = value * 0.75 + threshold * 1.2
```

### Pattern (USE)
```python
# GOOD: Constants from registry
from shared_registry import CONSTANT_GROUP_1
score = value * CONSTANT_GROUP_1["constant_a"]
```

---

## Component 3: Hooks (Translation Functions)

**File**: `translation/hooks.py`

### Hook Types

| Hook | Purpose |
|------|---------|
| `TerminologyHook` | Resolve aliases to canonical terms |
| `NormalizationHook` | Convert external data to internal schema |
| `ValidationHook` | Enforce business rules |
| `OutputHook` | Format output according to standards |

### Base Hook Structure
```python
from dataclasses import dataclass
from pathlib import Path
import json

@dataclass
class HookResult:
    """Standard hook return type."""
    success: bool
    value: any
    message: str = ""

class BaseHook:
    """Base class for all hooks."""
    
    def __init__(self):
        self.registry = self._load_registry()
    
    def _load_registry(self) -> dict:
        registry_path = Path(__file__).parent / "terminology_registry.json"
        if registry_path.exists():
            with open(registry_path) as f:
                return json.load(f)
        return {}

class TerminologyHook(BaseHook):
    """Resolve aliases to canonical terms."""
    
    def resolve(self, term: str) -> str:
        for canonical, data in self.registry.get("canonical_terms", {}).items():
            if term.lower() in [a.lower() for a in data.get("aliases", [])]:
                return canonical
            if term.upper() == canonical:
                return canonical
        return term  # Return as-is if no mapping
```

---

## Component 4: Knowledge Bases

**Directory**: `knowledge/`

### Purpose
Store domain-specific data that may change without code changes.

### Structure
```json
{
  "document_metadata": {
    "title": "[Source Document]",
    "date": "YYYY-MM-DD",
    "version": "1.0.0"
  },
  "data": {
    "key": "value"
  },
  "metadata": {
    "created": "YYYY-MM-DD",
    "schema_version": "[Skill] v1.0.0"
  }
}
```

---

## Implementation Checklist

When generating a skill, include:

| Component | File | Required? |
|-----------|------|-----------|
| Terminology Registry | `translation/terminology_registry.json` | ✅ If aliases exist |
| Shared Registry | `algorithms/shared_registry.py` | ✅ If constants exist |
| Hooks | `translation/hooks.py` | ✅ If translation needed |
| Knowledge Bases | `knowledge/*.json` | ⚠️ Domain-dependent |

---

## Benefits

1. **Domain experts** can update `knowledge/*.json` without touching code
2. **Developers** never hardcode magic numbers
3. **Terminology** evolves without breaking code
4. **Testing** is easier with centralized constants

---

**END OF PROTOCOL**
