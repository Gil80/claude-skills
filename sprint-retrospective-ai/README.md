# Sprint Retrospective AI — Claude Code Skill

A Claude Code skill that generates AI-powered sprint retrospective reports from Jira sprint data. Output is structured Markdown ready to paste into Confluence.

## What It Does

Connects to Jira (or reads from a local JSON file), pulls completed sprint data, and uses an AI model (OpenAI or Anthropic) to generate a structured retrospective with:

- Sprint summary & completion rate
- What went well
- What didn't go well
- Key patterns & observations
- Action items for the next sprint
- Risk flags

## Installation

Copy this skill into your Claude skills directory:

```bash
cp -r sprint-retrospective-ai ~/.claude/skills/
```

Install Python dependencies (from the root `sprint-retro-ai` project):

```bash
pip install requests
```

## Configuration

Set the following environment variables:

| Variable | Required | Description |
|----------|----------|-------------|
| `AI_API_KEY` | Yes | OpenAI or Anthropic API key |
| `AI_PROVIDER` | No | `openai` (default) or `anthropic` |
| `JIRA_BASE_URL` | For Jira mode | e.g. `https://yourcompany.atlassian.net` |
| `JIRA_EMAIL` | For Jira mode | Jira account email |
| `JIRA_API_TOKEN` | For Jira mode | Jira API token |

## Usage

Once installed, Claude will automatically use this skill when you ask things like:
- "Generate a retrospective for last sprint"
- "Analyze sprint 42 from board 5"
- "Create a Confluence retro report"

### Run manually

**Demo mode (no Jira needed):**
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --from-file ~/.claude/skills/sprint-retrospective-ai/examples/sprint_example.json
```

**Jira mode — most recent closed sprint:**
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42
```

**Jira mode — specific sprint:**
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42 --sprint-id 123
```

**Save to file:**
```bash
python ~/.claude/skills/sprint-retrospective-ai/scripts/retro_analyzer.py \
  --board-id 42 --output retro.md
```

## Files

```
sprint-retrospective-ai/
├── SKILL.md                    # Claude skill definition
├── README.md                   # This file
├── scripts/
│   └── retro_analyzer.py       # Main script
└── examples/
    └── sprint_example.json     # Demo data for file mode
```

## License

MIT
