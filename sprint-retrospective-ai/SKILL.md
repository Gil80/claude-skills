---
name: sprint-retrospective-ai
description: Use when the user wants to generate a sprint retrospective report, analyze a completed sprint, or create a Confluence-ready retro from Jira data.
---

# Sprint Retrospective AI

## Overview

Generates an AI-powered sprint retrospective report from Jira sprint data (or a local JSON file). Output is Markdown ready to paste into Confluence.

## Skill Structure

```
~/.claude/skills/sprint-retrospective-ai/
├── SKILL.md
├── scripts/
│   └── retro_analyzer.py       # Main script
└── examples/
    └── sprint_example.json     # Demo data for file mode
```

**Script:** `~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py`

## Required Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `AI_API_KEY` | Yes | OpenAI or Anthropic API key |
| `AI_PROVIDER` | No | `openai` (default) or `anthropic` |
| `JIRA_BASE_URL` | For Jira mode | e.g. `https://yourcompany.atlassian.net` |
| `JIRA_EMAIL` | For Jira mode | Jira account email |
| `JIRA_API_TOKEN` | For Jira mode | Jira API token |

## Usage

### Demo / file mode (no Jira needed)
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --from-file ~/.claude/skills/sprint-retrospective-ai/examples/sprint_example.json
```

### Jira mode — most recent closed sprint
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42
```

### Jira mode — specific sprint
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42 --sprint-id 123
```

### Save report to file
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42 --output retro.md
```

### Export raw data (for debugging or reuse)
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42 --export-data sprint_data.json
```

## Output Structure

The AI produces a Markdown report with these sections:
- **Sprint Summary** — completion rate + goal status
- **What Went Well** — 3–5 positive observations
- **What Didn't Go Well** — blockers, issues, concerns
- **Key Patterns & Observations** — workload, bugs, scope changes
- **Action Items** — 3–5 concrete improvements for next sprint
- **Risk Flags** — items that could affect next sprint

## Common Mistakes

- Forgetting to `pip install -r requirements.txt` before first run
- Omitting `AI_API_KEY` env var (will error at the AI step, not at startup)
- Using `--sprint-id` without `--board-id` — both are needed for Jira mode
