# Release Notes AI — Claude Code Skill

A Claude Code skill that generates professional, user-facing release notes from Git commit history or Jira fixVersion data using AI.

## What It Does

Reads commits (Git log or JSON file) or Jira issues, and uses an AI model (OpenAI or Anthropic) to produce categorized, user-facing Markdown release notes:

- Highlights summary
- New Features
- Bug Fixes
- Improvements
- Other Changes (docs, CI/CD, deps)

Sections with no items are omitted automatically.

## Installation

Copy the skill into your Claude skills directory and the agent file into your Claude agents directory:

```bash
cp -r release-notes-ai ~/.claude/skills/
cp agents/release-notes.md ~/.claude/agents/
```

Install Python dependencies:

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
- "Generate release notes since tag v2.3.0"
- "Create a changelog from the last 30 commits"
- "Write release notes for Jira fixVersion 3.2"

### Run manually

**Demo mode (no Git/Jira needed):**
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --from-file ~/.claude/skills/release-notes-ai/examples/commits_example.json \
  --version "2.4.0"
```

**Git mode — since a tag:**
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --version "2.4.0"
```

**Git mode — between two tags:**
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --until-tag v2.4.0 --version "2.4.0"
```

**Jira mode:**
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --jira-version "App 3.2" --version "3.2.0"
```

**Save to file:**
```bash
python ~/.claude/skills/release-notes-ai/scripts/release_notes.py \
  --git --since-tag v2.3.0 --version "2.4.0" --output RELEASE_NOTES.md
```

## Tips

- Use [Conventional Commits](https://www.conventionalcommits.org/) (`feat:`, `fix:`, `perf:`) for best categorization in Git mode
- `--export-data` saves the parsed data to JSON — useful for rerunning without hitting APIs again

## Files

```
release-notes-ai/
├── SKILL.md                    # Claude skill definition
├── README.md                   # This file
├── scripts/
│   └── release_notes.py        # Main script
└── examples/
    └── commits_example.json    # Demo data for file mode

agents/
└── release-notes.md            # Claude agent definition (interactive wrapper)
```

## License

MIT
