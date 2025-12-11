# Manage Cursor Rules

## Overview
Analyze project and manage cursor rules in `.cursor/rules/`. This command detects your project state and routes you to the appropriate workflow: initialization for new projects, refactoring for existing rules, cleanup for outdated content, or documentation organization. Each workflow is optimized for its specific purpose.

## Workflow Selection

### 1. Verify Git Repository
- Check for `.git` directory in project root
- Verify project is under git version control
- Exit if not git repository

### 2. Check for Existing Rules
- List all `.mdc` files in `.cursor/rules/` (if directory exists)
- Categorize:
  - **Shared rules:** `shared-*.mdc` (from workspace)
  - **Local rules:** `local-*.mdc` (project-specific)
  - **Old rules:** Without prefix (require migration)

### 3. Determine Workflow

**Priority 1: If old rules exist (without proper naming):**
- Show status: Old rules requiring migration
- Auto-select: **OLD RULES flow** (deep analysis and migration)
- See: [manage-cursor-rules-refactor.md](manage-cursor-rules-refactor.md) (handles old rules)

**Priority 2: If no old rules, but has local rules:**
- Show status: Local rules found
- Ask user: "What would you like to do? [refactor/cleanup/docs-check/init]"
  - **refactor** → [manage-cursor-rules-refactor.md](manage-cursor-rules-refactor.md)
  - **cleanup** → [manage-cursor-rules-cleanup.md](manage-cursor-rules-cleanup.md)
  - **docs-check** → [manage-cursor-rules-docs.md](manage-cursor-rules-docs.md)
  - **init** → [manage-cursor-rules-init.md](manage-cursor-rules-init.md) (start fresh)

**Priority 3: If no rules at all:**
- Show status: No local or old rules found
- Ask: "Generate rules from scratch? [Y/n]"
- If Yes → [manage-cursor-rules-init.md](manage-cursor-rules-init.md)
- If No → exit with message about running sync-shared-rules first

### 4. Check Shared Rules Present
- List all `shared-*.mdc` files in `.cursor/rules/`
- If no shared rules found:
  - Explain shared rules are required foundation
  - Ask: "Run sync-shared-rules now? [Y/n]"
  - If Yes: run sync, wait for completion
  - If No: exit (shared rules required)

### 5. Check for Deprecated Files
- Search for `.cursorrules` (deprecated format)
- If found: ask to delete, migrate to `.cursor/rules/*.mdc`
- Search for `.cursor/rules/` in subdirectories
- If found: ask to merge/delete/skip
- Ensure single rules directory at root

## Workflow Files

- **[manage-cursor-rules-init.md](manage-cursor-rules-init.md)** - Generate rules from scratch for new projects
- **[manage-cursor-rules-refactor.md](manage-cursor-rules-refactor.md)** - Validate and update existing rules, migrate old rules
- **[manage-cursor-rules-cleanup.md](manage-cursor-rules-cleanup.md)** - Quick removal of outdated content
- **[manage-cursor-rules-docs.md](manage-cursor-rules-docs.md)** - Comprehensive documentation audit and organization

## Checklist
- [ ] Git repository verified
- [ ] Existing rules categorized
- [ ] Workflow selected based on project state
- [ ] Shared rules present/synced
- [ ] Deprecated files handled
- [ ] Appropriate workflow file executed
- [ ] Results validated
