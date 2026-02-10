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

### 1. Install NotebookLM MCP

This skill requires the `notebooklm-mcp-cli` Python package. OpenClaw will attempt to install it automatically via `uv` or `pip`, but you can also install it manually:

```bash
# Recommended (requires uv)
uv tool install notebooklm-mcp-cli

# Or via pip
pip install notebooklm-mcp-cli
```

### 2. Authentication

#### Desktop (Interactive)
Run the login command and follow the browser prompts:
```bash
nlm login
```

#### Server / Headless (Linux)
Since you cannot open a browser on a headless server, you must provide authentication credentials manually.

**Step 2a: Extract Cookies**
1. Open [NotebookLM](https://notebooklm.google.com) in your **local computer's browser** (Chrome recommended).
2. Open Developer Tools (`F12` or right-click -> Inspect).
3. Go to the **Network** tab.
4. Refresh the page.
5. Filter for "batchExecute" or find any request to `notebooklm.google.com`.
6. Click the request and look at the **Request Headers** section.
7. Right-click the `cookie` value and copy it.

**Step 2b: Set Environment Variable**
On your server, set the `NOTEBOOKLM_COOKIES` environment variable.

**Option A: Export in shell (Temporary)**
```bash
export NOTEBOOKLM_COOKIES="...paste your long cookie string here..."
```

**Option B: Systemd Service (Robust)**
If running via systemd, add the variable directly to your service file (`~/.config/systemd/user/openclaw.service`):

```ini
[Service]
Environment="NOTEBOOKLM_COOKIES=...paste your long cookie string here..."
...
```

Then reload:
```bash
systemctl --user daemon-reload
systemctl --user restart openclaw
```

### 4. Verification

Before running OpenClaw, you can manually verify that the skill is working correctly.

**Check Tool List:**
This confirms the server starts and `mcporter` can communicate with it.
```bash
npx -y mcporter --config ~/.mcporter_config.json list notebooklm
```
*Expected Output*: A list of tools starting with `notebooklm` and `29 tools ... STDIO`.

**Check Authentication:**
This confirms your cookies are valid and providing access.
```bash
npx -y mcporter --config ~/.mcporter_config.json call notebooklm.notebook_list
```
If it errors with "Login required" or "Authentication expired":
1.  **Refresh** the NotebookLM page in your browser.
2.  Copy the **Request Header** `cookie` value again (it must be the full string).
3.  Update the file: `nano ~/.nlm/cookies.txt` (paste new value).
4.  Try again.
