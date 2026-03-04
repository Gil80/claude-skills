---
name: release-notes-ai
description: Use when the user wants to generate release notes, changelog entries, or user-facing change summaries from Git commit history or Jira fixVersion data.
---

# Release Notes AI

## Overview

Generates professional, user-facing release notes from Git commits or Jira fixVersion issues using AI. Output is categorized Markdown (Features, Bug Fixes, Improvements).

## Skill Structure

```
~/.claude/skills/release-notes-ai/
├── SKILL.md
├── scripts/
│   └── release_notes.py        # Main script
└── examples/
    └── commits_example.json    # Demo data for file mode
```

**Script:** `~/.claude/skills/release-notes-ai/scripts/release_notes.py`

## Required Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `AI_API_KEY` | Yes | OpenAI or Anthropic API key |
| `AI_PROVIDER` | No | `openai` (default) or `anthropic` |
| `JIRA_BASE_URL` | For Jira mode | e.g. `https://yourcompany.atlassian.net` |
| `JIRA_EMAIL` | For Jira mode | Jira account email |
| `JIRA_API_TOKEN` | For Jira mode | Jira API token |

## Usage

### Demo / file mode (no Git/Jira needed)
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --from-file ~/.claude/skills/release-notes-ai/examples/commits_example.json \
  --version "2.4.0"
```

### Git mode — since a tag to HEAD
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --version "2.4.0"
```

### Git mode — between two tags
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --until-tag v2.4.0 --version "2.4.0"
```

### Git mode — different repo
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --repo-path /path/to/repo --since-tag v1.0.0 --version "1.1.0"
```

### Jira mode — by fixVersion
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --jira-version "App 3.2" --version "3.2.0"
```

### Save to file
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --version "2.4.0" --output RELEASE_NOTES.md
```

## Output Structure

```markdown
# Release Notes - 2.4.0
**Release Date:** 2026-03-04

## Highlights
Brief 2–3 sentence summary.

## New Features
- Feature items...

## Bug Fixes
- Fix items...

## Improvements
- Performance/refactor items...

## Other Changes
- Docs, CI/CD, deps...
```

Sections with no items are omitted automatically.

## Tips

- Use [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `perf:`) for best categorization in Git mode
- Free-form commit messages still work but categorization is less accurate
- `--export-data` saves the parsed commit/issue JSON — useful for rerunning without hitting APIs

## Common Mistakes

- `--git` mode requires at least `--since-tag` to limit scope; without it, returns ALL commits
- `--from-file`, `--git`, and `--jira-version` are mutually exclusive — pick one
- Forgetting `--version` flag (defaults to `1.0.0` if omitted)
