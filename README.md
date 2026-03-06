# Claude Skills

A collection of Claude Code skills for AI-powered development workflows.

## Skills

### [sprint-retrospective-ai](./sprint-retrospective-ai)
Generates AI-powered sprint retrospective reports from Jira sprint data. Output is structured Markdown ready to paste into Confluence.

**Trigger:** Ask Claude to "generate a retrospective", "analyze last sprint", or "create a Confluence retro report".

### [release-notes-ai](./release-notes-ai)
Generates professional, user-facing release notes from Git commit history or Jira fixVersion data.

**Trigger:** Ask Claude to "generate release notes since tag v1.0", "create a changelog", or "write release notes for this version".

### [nano-banana-images](./nano-banana-images)
A formalized skill for generating hyper-realistic, highly-controlled images using the Nano Banana 2 (Gemini 3.1 Flash) model through parameterized JSON prompting. Supports multiple backends including Kie.ai, Google Gemini API, SDXL, and Gemini web UI export.

**Trigger:** Ask Claude to "generate a realistic image", "create a hyper-realistic portrait", or "generate an image using Nano Banana".

## Installation

Clone this repo and copy the skills you want into your Claude skills directory:

```bash
git clone https://github.com/Gil80/claude-skills.git
cp -r claude-skills/sprint-retrospective-ai ~/.claude/skills/
cp -r claude-skills/release-notes-ai ~/.claude/skills/
cp -r claude-skills/nano-banana-images ~/.claude/skills/
```

## Requirements

```bash
pip install requests
```

Set your API keys as environment variables — see each skill's README for details.
