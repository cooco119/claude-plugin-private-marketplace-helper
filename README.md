# Private Marketplace Helper

A Claude Code plugin to install private GitHub marketplace plugins using your shell's git authentication.

## Why?

Claude Code currently [doesn't support private repository authentication](https://github.com/anthropics/claude-code/issues/9756) when adding marketplaces. This plugin works around that limitation by using your shell's existing git credentials.

## Installation

```bash
# Add this marketplace
claude plugin marketplace add jaejun-lee/claude-plugin-private-marketplace-helper

# Install the plugin
claude plugin install private-marketplace-helper@private-marketplace-helper
```

## Usage

```
/private-marketplace-helper:add <github-url>
```

### Supported URL Formats

| Format | Example |
|--------|---------|
| GitHub shorthand | `github:org/repo` |
| Short format | `org/repo` |
| HTTPS URL | `https://github.com/org/repo.git` |
| SSH URL | `git@github.com:org/repo.git` |

### Examples

```bash
# Using GitHub shorthand (same as native Claude Code format)
/private-marketplace-helper:add github:myorg/my-private-marketplace

# Using short format
/private-marketplace-helper:add myorg/my-private-marketplace

# Using SSH (if HTTPS fails)
/private-marketplace-helper:add git@github.com:myorg/my-private-marketplace.git
```

### After Adding

Once the marketplace is added, install plugins normally:

```bash
claude plugin install my-plugin@my-private-marketplace
```

## How It Works

1. Clones the repository using your shell's git authentication (SSH keys, credential helper, etc.)
2. Registers the marketplace in `~/.claude/plugins/known_marketplaces.json`
3. Makes the marketplace available for `claude plugin install`

## Requirements

- Git configured with access to the private repository
- For SSH: SSH keys set up for GitHub
- For HTTPS: Git credential helper configured

## License

MIT
