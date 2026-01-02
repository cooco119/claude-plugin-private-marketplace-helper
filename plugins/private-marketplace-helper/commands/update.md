---
description: "Update a private marketplace to the latest version"
argument-hint: "[marketplace-name]"
---

Update a private marketplace by pulling the latest changes from its git repository.

## Instructions

### Step 1: Identify Target Marketplace(s)

If a marketplace name is provided, update that specific marketplace.

If no argument provided, list all installed marketplaces and ask which one to update:

```bash
ls ~/.claude/plugins/marketplaces/
```

Present the list and ask: "Which marketplace would you like to update? (or 'all' to update all)"

### Step 2: Verify Marketplace Exists

Check that the marketplace directory exists:

```bash
ls ~/.claude/plugins/marketplaces/<marketplace-name> 2>/dev/null
```

If not found:
```
Marketplace '<name>' not found.

Installed marketplaces:
  - marketplace-1
  - marketplace-2

Use: /private-marketplace-helper:add <github-url> to add a new marketplace.
```

### Step 3: Pull Latest Changes

Navigate to the marketplace directory and pull:

```bash
cd ~/.claude/plugins/marketplaces/<marketplace-name> && git pull
```

**If pull fails due to local changes:**
```bash
cd ~/.claude/plugins/marketplaces/<marketplace-name> && git fetch origin && git reset --hard origin/HEAD
```

Ask user for confirmation before running hard reset: "Local changes detected. Reset to remote version? (This will discard local changes)"

**If pull fails due to authentication:**
- Suggest checking git credentials
- For SSH: `ssh -T git@github.com`
- For HTTPS: Check credential helper configuration

### Step 4: Update known_marketplaces.json

Read `~/.claude/plugins/known_marketplaces.json` and update the `lastUpdated` timestamp for this marketplace:

```json
{
  "<marketplace-name>": {
    "lastUpdated": "<current-ISO-timestamp>"
  }
}
```

Only update the `lastUpdated` field, preserve all other existing fields.

### Step 5: Report Results

Check the current version in marketplace.json:
```bash
cat ~/.claude/plugins/marketplaces/<name>/.claude-plugin/marketplace.json
```

**Success output:**
```
Marketplace '<name>' updated successfully!

Current plugins:
  - plugin-name-1 (v1.0.0)
  - plugin-name-2 (v2.1.0)

To reinstall a plugin with the latest version:
  claude plugin install <plugin-name>@<marketplace-name>
```

### Updating All Marketplaces

If user chose 'all', loop through each marketplace and:
1. Run git pull
2. Update lastUpdated timestamp
3. Collect results

**Summary output:**
```
Update complete!

  [OK] marketplace-1 - updated
  [OK] marketplace-2 - already up to date
  [FAIL] marketplace-3 - authentication failed

Failed updates may need manual intervention.
```
