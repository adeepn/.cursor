# Manage GitHub Workflows

## Overview
Manage GitHub workflow runs with three operational modes: check status, auto-fix failures, or watch running workflows. Choose your workflow based on what you need to accomplish.

## Workflow Selection

Ask user: "What would you like to do? [check/fix/watch]"

**CHECK** - Quick status report:
- Shows all workflow runs for current branch/commit
- Lists defined workflows and their status
- Reports passed/failed/running summary
- Exits immediately (no waiting, no fixes)

**FIX** - Complete automated fix cycle:
- Waits for running workflows to complete
- Creates fix branch if failures exist
- Applies fixes iteratively until all pass
- Triggers workflows if none exist
- Handles merge or PR creation at end

**WATCH** - Monitor running workflows:
- Identifies currently running workflows
- Waits for all to complete
- Reports final pass/fail summary
- No fixes applied

## Key Commands

- List workflows: `gh workflow list`
- Check runs: `gh run list --branch=$(git branch --show-current) --commit=$(git rev-parse HEAD)`
- View run details: `gh run view <run-id>`
- View failed logs: `gh run view <run-id> --log-failed`
- Trigger workflow: `gh workflow run <workflow-name> --ref <branch-name>`
- Watch run: `gh run watch <run-id>`
- Create fix branch: `git checkout -b autofix/<description>`
- Create PR: `gh pr create`

## Common Functions

### Ensure Manual Trigger Capability
- Check each workflow file in `.github/workflows/`
- Verify `workflow_dispatch` in the `on:` section
- If missing: add it, commit, push
- If submodules exist: ensure proper checkout configuration

### Create Fix Branch
- Check current branch: `git branch --show-current`
- If already in autofix branch: stay on it
- If not: create new `autofix/<description>` branch
- Ensure workflow_dispatch enabled in fix branch

### Identify Failure Cause
- View failed job logs: `gh run view <run-id> --log-failed`
- Analyze error messages
- Categorize: build, test, lint, syntax, timeout, dependency, or unknown

### Apply Fixes Iteratively
Loop until all workflows pass or manual intervention needed:
1. Identify failure cause
2. Apply appropriate fix
3. Commit changes
4. Push to trigger workflows
5. Wait for workflows to complete
6. Check if all passed
7. If failed: repeat from step 1

**Fix strategies by type:**
- Build errors: fix syntax, imports, dependencies
- Test failures: fix tests or code
- Lint errors: run linter and fix
- Timeout: optimize or increase timeout
- Dependencies: update manifests
- Unknown: ask user for guidance

### Complete Workflow Fixes
After all workflows pass:
- Ask user: "Merge fix branch or create PR? [merge/pr]"
- If merge: merge to main and delete fix branch
- If pr: create PR and keep branch

## Workflow Configuration for Submodules

If project uses git submodules, workflows need proper configuration:

```yaml
steps:
  - uses: actions/checkout@v3
    with:
      submodules: recursive  # or 'true'
  
  # Rest of workflow steps...
```

Check all workflows for submodule checkout configuration if project has submodules.

## CHECK Flow

1. **Verify Git Repository**
   - Check for `.git` directory
   - Exit if not git repository

2. **List All Workflows**
   - `gh workflow list`
   - Show available workflows

3. **Check Current Branch Runs**
   - `gh run list --branch=$(git branch --show-current) --limit 10`
   - Show recent runs for current branch

4. **Check Current Commit Runs**
   - `gh run list --commit=$(git rev-parse HEAD)`
   - Show runs for current commit

5. **Report Status**
   - Count passed/failed/running/pending
   - Display summary
   - Exit

## FIX Flow

1. **Verify Git Repository and Clean State**
   - Check for `.git` directory
   - Verify no uncommitted changes
   - Exit if dirty

2. **Ensure Manual Trigger Capability**
   - Call: [Ensure Manual Trigger Capability](#ensure-manual-trigger-capability)
   - Commit and push if changes made

3. **Wait for Running Workflows**
   - List all running workflows for current commit
   - For each running workflow: `gh run watch <run-id>`
   - Wait for all to complete

4. **Check Workflow Status**
   - Get all workflow runs for current commit
   - If all passed: exit with success
   - If no workflows: trigger them and wait
   - If failures: proceed to fixes

5. **Create Fix Branch and Apply Fixes**
   - Call: [Create Fix Branch](#create-fix-branch)
   - Call: [Apply Fixes Iteratively](#apply-fixes-iteratively)
   - Continue until all pass or manual intervention needed

6. **Complete Workflow Fixes**
   - Call: [Complete Workflow Fixes](#complete-workflow-fixes)
   - Merge or create PR based on user choice

## WATCH Flow

1. **Verify Git Repository**
   - Check for `.git` directory
   - Exit if not git repository

2. **Check for Running Workflows**
   - `gh run list --commit=$(git rev-parse HEAD) --status in_progress`
   - If no running workflows: exit with message

3. **Watch Each Running Workflow**
   - For each running workflow:
     - Show workflow name and run ID
     - `gh run watch <run-id>`
   - Wait for all to complete

4. **Report Final Status**
   - Count passed/failed
   - Display summary
   - Exit

## Submodule Handling

If project uses git submodules:
1. Check if `.gitmodules` file exists
2. Verify each workflow has proper submodule checkout
3. Add checkout configuration if missing:

```yaml
- uses: actions/checkout@v3
  with:
    submodules: recursive
```

4. Commit and push changes

## Checklist

### CHECK
- [ ] Git repository verified
- [ ] Workflows listed
- [ ] Current branch/commit runs checked
- [ ] Status summary displayed

### FIX
- [ ] Git repository verified (clean state)
- [ ] Manual trigger capability ensured
- [ ] Running workflows waited for
- [ ] Failures identified
- [ ] Fix branch created
- [ ] Fixes applied iteratively
- [ ] All workflows passing
- [ ] Changes merged or PR created

### WATCH
- [ ] Git repository verified
- [ ] Running workflows identified
- [ ] All workflows watched until completion
- [ ] Final status reported
