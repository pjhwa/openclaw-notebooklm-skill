# OpenClaw NotebookLM Skill

This repository contains the [OpenClaw](https://github.com/openclaw/openclaw) skill definition for [NotebookLM MCP](https://github.com/jacob-bd/notebooklm-mcp-cli).

## Features

- **Manage Notebooks**: List, create, and delete notebooks.
- **Query Sources**: Ask questions about your documents.
- **Download Artifacts**: Generate and download Audio Overviews, FAQs, Briefing Docs, and more.

## Installation

### Automatic (OpenClaw)

Clone this repository into your OpenClaw workspace's `skills` directory:

```bash
cd ~/.openclaw/workspace/skills
git clone https://github.com/jooksan-park/openclaw-notebooklm-skill.git notebooklm
```
*(Replace `jooksan-park` with your GitHub username)*

The OpenClaw agent will automatically detect the skill.

### Manual Setup (Linux/Headless)

If running on a headless server, you must set an environment variable for authentication:

1.  Extract cookies from your local browser's Network tab (request to `notebooklm.google.com`).
2.  Set the environment variable:
    ```bash
    export NOTEBOOKLM_COOKIES="Values..."
    ```
