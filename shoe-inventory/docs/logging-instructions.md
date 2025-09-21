# Logging Instructions for Next AI

## Comprehensive Change Tracking Requirements

All subsequent AI interactions must maintain detailed, structured logging of changes made to the project. This ensures complete traceability and helps in understanding the evolution of the codebase.

## Required Logging Format

### 1. JSON-Based Change Entries
- **Format**: Each change must be documented as a JSON object in CHANGELOG.md
- **Required Fields**:
  - `timestamp`: ISO 8601 format (YYYY-MM-DDTHH:MM:SSZ)
  - `file`: Full path to the file that was changed
  - `change_type`: One of "creation", "modification", "deletion"
  - `summary`: Brief description of what was changed and why
  - `diff`: Git-style diff output showing exact changes (optional but recommended for modifications)
  - `author`: Name of the AI or person making the change

### 2. Example Change Entry
```json
{
  "timestamp": "2025-09-20T22:15:30Z",
  "file": "src/utils/helpers.py",
  "change_type": "modification",
  "summary": "Refactored helper function to improve readability and add type hints",
  "diff": "@@ -12,7 +12,9 @@\n- def old_function(a, b):\n-     return a+b\n+ def add_numbers(a: int, b: int) -> int:\n+     \"\"\"Adds two integers.\"\"\"\n+     return a + b\n",
  "author": "AI: DeepSeek Reasoner"
}
```

### 3. Change Log Maintenance
- **File**: `CHANGELOG.md` in the project root
- **Frequency**: Update with EVERY change, no matter how small
- **Structure**: Use a JSON array to store all change entries for easy parsing

### 4. Todo List Integration
- **File**: `TODO.md` in the project root
- **Purpose**: Track current progress and pending tasks
- **Update Frequency**: After each completed task
- **Requirements**: Each completed task must reference the corresponding change entry in CHANGELOG.md

### 5. Session Logging
- **File**: `SESSION_LOG.md` (to be created)
- **Content**: Summary of each AI session with references to change entries
- **Includes**: Timestamp, duration, tasks completed, issues encountered, and links to changes

## Instructions for Next AI
1. **Start each session** by reading CHANGELOG.md and TODO.md to understand recent changes
2. **Document all changes** using the JSON format in CHANGELOG.md
3. **Include specific details** about what was created, modified, or deleted
4. **Provide diffs** for modifications to show exact changes
5. **Update TODO.md** with references to completed changes
6. **Maintain consistency** with existing project structure

## Validation Checklist
- [ ] All changes documented in CHANGELOG.md with JSON format
- [ ] Required fields included: timestamp, file, change_type, summary, author
- [ ] Diffs provided for modifications where possible
- [ ] TODO.md updated with references to changes
- [ ] Session logs maintained with change references

By maintaining this structured change tracking, we ensure complete transparency and make it easier for both AIs and humans to understand and contribute to the project.