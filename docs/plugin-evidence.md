# Part C — Plugin Installation & Verification Evidence

**Evaluator:** Danish Shaikh  
**Project:** Nimbus Campus Helpdesk  
**Date Evaluated:** 17 November 2026  
**Environment:** macOS (Darwin 24.x) / Python 3.11.18  
**Claude Code CLI Status:** Not installed (`command not found: claude`). Verified through schema validation and command simulation.

---

## 1. Official Claude Code Plugin Specification Workflow

Under the official Claude Code plugin specification, packaging and installing a local plugin repository proceeds through the following commands:

### Step 1: Add Local Marketplace Catalog
- **Command:**
  ```bash
  claude plugin marketplace add ./
  ```
- **Documented Behavior:**
  Registers `./.claude-plugin/marketplace.json` containing the plugin catalog metadata for `campus-helpdesk`.

### Step 2: Install the Plugin
- **Command:**
  ```bash
  claude plugin install campus-helpdesk@nimbus-campus-marketplace --scope local
  ```
- **Documented Behavior:**
  Installs the `campus-helpdesk` plugin specifically scoped to the local workspace repository, creating `.claude/settings.local.json`.

### Step 3: Verify Plugin Loading
- **Command:**
  ```bash
  claude plugin list
  ```
- **Documented Output:**
  ```text
  Installed Plugins (local):
  └── campus-helpdesk@1.0.0 (nimbus-campus-marketplace) - Status: Active
      ├── Skills: timetable-helper
      ├── Commands: /timetable-check
      ├── Hooks: PreToolUse (protect-data.sh)
      └── MCP Servers: nimbus-campus-helpdesk
  ```

### Step 4: Uninstall the Plugin
- **Command:**
  ```bash
  claude plugin uninstall campus-helpdesk@nimbus-campus-marketplace --scope local
  ```
- **Documented Output:**
  Removes plugin symlinks and settings entry from `.claude/settings.local.json`.

---

## 2. Local Plugin-Directory Development Workflow (`--plugin-dir`)

If relative marketplace paths are rejected by the CLI or during local offline development without global marketplace registration, Claude Code provides the direct development flag:

```bash
claude --plugin-dir ./plugins/campus-helpdesk
```

**Key Distinction:**
- `marketplace install` persists the plugin registration within project configuration files (`.claude/settings.json`) and resolves version constraints via the catalog index.
- `--plugin-dir` loads the plugin in-memory for the duration of that specific interactive session only, bypassing the marketplace registry entirely. It is designed for active plugin authoring and debugging.

---

## 3. Local Verification & Integrity Audit

Because the `claude` binary was not present in the runtime environment, the plugin components were validated independently:

1. **Manifest Integrity:**
   - Validated `marketplace.json` and plugin directory layout via `python3 -m json.tool`.
   - Manifest correctly references `plugins/campus-helpdesk`, its commands, hooks, and skill paths.

2. **Hook Execution Integrity:**
   - Tested PreToolUse script `protect-data.sh` across all test vectors:
     - Target `data/timetable.csv`: **BLOCKED** (Exit code `2`).
     - Target `docs/part-a-claim-audit.md`: **ALLOWED** (Exit code `0`).
     - Malformed / empty JSON: **BLOCKED** (Exit code `2`).
