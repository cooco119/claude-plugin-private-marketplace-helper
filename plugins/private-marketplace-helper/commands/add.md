---
description: "Add a private GitHub marketplace using shell authentication"
argument-hint: "<github-url>"
---

Add a private GitHub marketplace plugin using your shell's git authentication context.

This works around Claude Code's limitation with private repository authentication by using your shell's existing git credentials.

## Supported URL Formats

- `github:org/repo` - GitHub shorthand (native Claude Code format)
- `org/repo` - Short format (assumes GitHub)
- `https://github.com/org/repo.git` - HTTPS URL
- `git@github.com:org/repo.git` - SSH URL

## Instructions

### Step 1: Parse the URL

Parse the user's input to extract the repository URL and marketplace name.

**URL Normalization Rules:**
| Input | Normalized URL | Name |
|-------|---------------|------|
| `github:org/repo` | `https://github.com/org/repo.git` | `repo` |
| `org/repo` | `https://github.com/org/repo.git` | `repo` |
| `https://github.com/org/repo.git` | (as-is) | `repo` |
| `https://github.com/org/repo` | add `.git` suffix | `repo` |
| `git@github.com:org/repo.git` | (as-is) | `repo` |

Extract the marketplace name from the repo name (last part of the path, without `.git`).

### Step 2: Check if Already Exists

```bash
ls ~/.claude/plugins/marketplaces/<marketplace-name> 2>/dev/null
```

If directory exists, ask user if they want to update (git pull) or skip.

### Step 3: Clone the Repository

Run git clone using the user's shell authentication:

```bash
cd ~/.claude/plugins/marketplaces && git clone <normalized-url> <marketplace-name>
```

**If HTTPS clone fails**, suggest trying SSH:
```
HTTPS clone failed. Try SSH URL instead:
/private-marketplace-helper:add git@github.com:org/repo.git
```

**If SSH clone fails**, suggest:
- Check repository access permissions
- Verify SSH keys are configured: `ssh -T git@github.com`
- Try HTTPS with credentials configured

### Step 3: Update known_marketplaces.json

Read `~/.claude/plugins/known_marketplaces.json`, then use Edit tool to add the new marketplace entry:

```json
{
  "<marketplace-name>": {
    "source": {
      "source": "git",
      "url": "<normalized-url>"
    },
    "installLocation": "<full-path-to-marketplace>",
    "lastUpdated": "<current-ISO-timestamp>"
  }
}
```

Merge with existing entries, do not overwrite the entire file.

### Step 4: Verify and Report Success

Check for valid marketplace structure:
```bash
cat ~/.claude/plugins/marketplaces/<name>/.claude-plugin/marketplace.json 2>/dev/null
```

Parse the marketplace.json to list available plugins.

**Success output:**
```
Marketplace '<name>' added successfully!

Available plugins:
  - plugin-name-1
  - plugin-name-2

Install with:
  claude plugin install <plugin-name>@<marketplace-name>
```

**If no marketplace.json found:**
```
Warning: No .claude-plugin/marketplace.json found.
This repository may not be a valid Claude Code marketplace.
```
