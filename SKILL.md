---
name: github-repo-manager
description: Use when managing GitHub repositories - listing, viewing details, searching, deleting, creating, renaming, changing visibility, forking, or archiving repos via gh CLI.
version: 1.1.0
author: Thanh Danh
tags:
  - github
  - cli
  - repo-management
tools:
  - Bash
  - Read
---

# GitHub Repo Manager

## Overview
Manages GitHub repositories using GitHub CLI (gh). Provides commands for viewing lists, creating, deleting, searching, and updating repo properties.

## When to Use

- User wants to list their or another's repositories
- Need to create a new repository on GitHub
- Need to delete a repository (irreversible!)
- Need to search repositories by keyword or owner
- Need to change visibility (public/private)
- Need to fork a repo to own account
- Need to rename or archive repository

## Quick Reference

| Task | Command |
|------|---------|
| List repos | `gh repo list <username> --limit 100` |
| View details | `gh repo view <owner>/<repo>` |
| Search repos | `gh search repos "<keyword>" --owner <username>` |
| Delete repo | `gh repo delete <owner>/<repo> --confirm` |
| Create repo | `gh repo create <name> --public\|--private` |
| Rename repo | `gh repo rename <new-name>` |
| Change visibility | `gh repo edit <owner>/<repo> --visibility public\|private` |
| Fork repo | `gh repo fork <owner>/<repo>` |
| Archive repo | `gh repo edit <owner>/<repo> --archive` |

## Prerequisites

Check authentication before use:
```bash
gh auth status
```

If not logged in:
```bash
gh auth login
```

## Commands

### List Repositories

List all repos for a user:
```bash
gh repo list <username> --limit 100
```

List with JSON for easy parsing:
```bash
gh repo list <username> --limit 100 --json name,visibility,description,createdAt,updatedAt
```

Filter by visibility:
```bash
gh repo list <username> --visibility public
gh repo list <username> --visibility private
```

### View Repository Details

View repo info:
```bash
gh repo view <owner>/<repo>
```

View as JSON:
```bash
gh repo view <owner>/<repo> --json name,description,visibility,defaultBranch,url,createdAt,updatedAt,pushedAt
```

### Search Repositories

Search by owner:
```bash
gh search repos --owner <username> --limit 20
```

Search by keyword:
```bash
gh search repos "<keyword>" --owner <username> --limit 20
```

### Delete Repository

**WARNING**: Permanent deletion, cannot be restored!
```bash
gh repo delete <owner>/<repo> --confirm
```

### Create Repository

Create new empty repo:
```bash
gh repo create <repo-name> --public
gh repo create <repo-name> --private
gh repo create <repo-name> --public --clone
```

Create from current directory (push code to GitHub):
```bash
gh repo create <repo-name> --source . --public
```

### Rename Repository

Rename (run from repo directory or specify owner/repo):
```bash
gh repo rename <new-name>
```

### Change Visibility

Switch between public and private:
```bash
gh repo edit <owner>/<repo> --visibility public
gh repo edit <owner>/<repo> --visibility private
```

### Fork Repository

Fork a repo to your account:
```bash
gh repo fork <owner>/<repo>
```

### Archive Repository

Archive repo (cannot push but can still clone):
```bash
gh repo edit <owner>/<repo> --archive
```

## Common Workflows

### List all repos for a user
```bash
gh repo list <username> --json name,visibility,description,createdAt
```

### Delete multiple repos
```bash
gh repo delete <owner>/<repo1> --confirm
gh repo delete <owner>/<repo2> --confirm
```

### Find unused repos (over 1 year old)
```bash
gh repo list <username> --limit 100 --json name,createdAt,updatedAt | jq '.[] | select(.updatedAt < "2025-01-01")'
```

### Check if repo is a fork
```bash
gh repo view <owner>/<repo-name> --json isFork
```

## Output Format

Use jq to parse JSON output:

```bash
# Get list of repo names
gh repo list <username> --json name | jq '.[].name'

# Filter private repos
gh repo list <username> --json name,visibility | jq '.[] | select(.visibility == "PRIVATE")'

# Count repos
gh repo list <username> --json name | jq 'length'

# Get repos with descriptions
gh repo list <username> --json name,description | jq '.[] | select(.description != null)'
```

## Tips

- Always use `--confirm` when deleting to avoid interactive prompts
- Use `gh api` for direct GitHub API calls for advanced operations
- Run `gh help <command>` for more options
- Check `gh auth status` before any operations
- For repos not owned by you, need admin permissions to make changes
