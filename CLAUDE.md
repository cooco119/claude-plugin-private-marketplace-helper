# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Claude Code plugin marketplace that provides a workaround for private GitHub repository authentication when adding marketplaces. It uses the shell's existing git credentials (SSH keys, credential helpers) instead of Claude Code's native authentication.

## Repository Structure

```
.claude-plugin/marketplace.json     # Marketplace manifest (defines available plugins)
plugins/
  private-marketplace-helper/
    .claude-plugin/plugin.json      # Plugin manifest
    commands/add.md                 # Skill definition for /private-marketplace-helper:add
```

## Architecture

This is a **pure-markdown plugin** with no build system or runtime code:

- **Marketplace manifest** (`.claude-plugin/marketplace.json`): Registers this repo as a Claude Code marketplace and lists the available plugin
- **Plugin manifest** (`plugins/.../.claude-plugin/plugin.json`): Defines plugin metadata
- **Skill files** (`commands/*.md`): Markdown files with step-by-step instructions that Claude Code follows
  - `add.md`: `/private-marketplace-helper:add` - adds a private marketplace
  - `update.md`: `/private-marketplace-helper:update` - pulls latest changes for installed marketplaces

## How the Plugin Works

**add.md** - Adds a new private marketplace:
1. Parse and normalize various GitHub URL formats (github:org/repo, org/repo, HTTPS, SSH)
2. Clone the repository to `~/.claude/plugins/marketplaces/<name>` using shell git authentication
3. Register the marketplace in `~/.claude/plugins/known_marketplaces.json`
4. Verify the marketplace structure and list available plugins

**update.md** - Updates existing marketplaces:
1. List installed marketplaces or accept a specific name
2. Run `git pull` in the marketplace directory
3. Update the `lastUpdated` timestamp in `known_marketplaces.json`
4. Report results with current plugin versions

## Development Notes

- Skill files use YAML frontmatter for metadata (`description`, `argument-hint`)
- URL normalization must handle: `github:org/repo`, `org/repo`, HTTPS URLs (with/without .git), SSH URLs
- The marketplace name is extracted from the repo name (last path segment, without .git)
