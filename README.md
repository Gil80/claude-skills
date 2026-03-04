# Claude Skills

A collection of Claude Code skills for AI-powered development workflows.

## Skills

### [sprint-retrospective-ai](./sprint-retrospective-ai)
Generates AI-powered sprint retrospective reports from Jira sprint data. Output is structured Markdown ready to paste into Confluence.

**Trigger:** Ask Claude to "generate a retrospective", "analyze last sprint", or "create a Confluence retro report".

### [release-notes-ai](./release-notes-ai)
Generates professional, user-facing release notes from Git commit history or Jira fixVersion data.

**Trigger:** Ask Claude to "generate release notes since tag v1.0", "create a changelog", or "write release notes for this version".

## Installation

Clone this repo and copy the skills you want into your Claude skills directory:

```bash
git clone https://github.com/Gil80/claude-skills.git
cp -r claude-skills/sprint-retrospective-ai ~/.claude/skills/
cp -r claude-skills/release-notes-ai ~/.claude/skills/
```

## Requirements

```bash
pip install requests
```

Set your API keys as environment variables — see each skill's README for details.
