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

## Sample Output

```markdown
# Sprint Retrospective: Sprint 42

## Sprint Summary
The team completed 18 of 23 planned issues (78% completion rate). The sprint goal —
shipping the new billing module — was partially achieved. Core functionality was
delivered but CSV export (PROJ-356) was moved to Sprint 43.

## What Went Well
- Bug resolution rate was strong: 7 of 8 bugs closed within the sprint
- PROJ-341 (payment gateway integration) was delivered ahead of schedule
- No unplanned work was added mid-sprint

## What Didn't Go Well
- PROJ-356 was scoped too broadly and carried over
- Two team members carried 60% of the ticket load (unbalanced distribution)
- Three P2 bugs were found in QA that should have been caught earlier

## Action Items
- Break PROJ-356 into smaller sub-tasks before Sprint 43 planning
- Cap any single developer at 30% of sprint capacity during assignment
- Add a pre-QA checklist for features with P2 risk

## Risk Flags
- PROJ-356 carry-over adds scope pressure to Sprint 43
- Unbalanced workload is a burnout risk — address in 1:1s
```

## Common Mistakes

- Forgetting to `pip install -r requirements.txt` before first run
- Omitting `AI_API_KEY` env var (will error at the AI step, not at startup)
- Using `--sprint-id` without `--board-id` — both are needed for Jira mode
