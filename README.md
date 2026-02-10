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

This skill requires the `notebooklm-mcp-cli` Python package.

**macOS / Linux:**
```bash
# Install uv (if not present)
brew install uv  # macOS
curl -LsSf https://astral.sh/uv/install.sh | sh  # Linux

# Install the tool
uv tool install notebooklm-mcp-cli
```

### 2. Authentication

#### Desktop (Interactive)
Run the login command and follow the browser prompts (Works on macOS & Windows):
```bash
nlm login
```

#### Headless / Service (Linux & macOS)
If you are running OpenClaw as a background service (systemd or launchd) or cannot use the interactive login, use the **manual cookie file method**.

**Step 2a: Extract Cookies**
1. Open [NotebookLM](https://notebooklm.google.com) in your **local computer's browser** (Chrome recommended).
2. Open Developer Tools (`F12` or right-click -> Inspect).
3. Go to the **Network** tab.
4. Refresh the page.
5. Filter for "batchExecute" or find any request to `notebooklm.google.com`.
6. Click the request and look at the **Request Headers** section.
7. Right-click the `cookie` value and copy it.

**Step 2b: Save Cookies to File**
1.  Create a directory for the cookies:
    ```bash
    mkdir -p ~/.nlm
    ```
2.  Create/Edit the cookie file:
    ```bash
    nano ~/.nlm/cookies.txt
    ```
3.  Paste the **entire** cookie string into the file and save (Ctrl+O, Enter, Ctrl+X).

### 3. Usage & Configuration

To ensure OpenClaw can use the skill with proper authentication, we configure `mcporter` to read the cookie file at runtime.

Run this command once to register the tool:

```bash
# Remove any old config
npx -y mcporter --config ~/.mcporter_config.json config remove notebooklm

# Register with file-based auth and standard IO
npx -y mcporter --config ~/.mcporter_config.json config add notebooklm --stdio "bash -c 'export NOTEBOOKLM_COOKIES=\$(cat ~/.nlm/cookies.txt); notebooklm-mcp --transport stdio'"
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
npx -y mcporter --config ~/.mcporter_config.json call notebooklm.notebook_list --args "{}"
```
If it errors with "Login required" or "Authentication expired":
1.  **Refresh** the NotebookLM page in your browser.
2.  Copy the **Request Header** `cookie` value again (it must be the full string).
3.  Update the file: `nano ~/.nlm/cookies.txt` (paste new value).
4.  Try again.

### 5. Usage Examples

**List Notebooks:**
```bash
npx -y mcporter --config ~/.mcporter_config.json call notebooklm.notebook_list --args "{}"
```

**Query Notebook:**
```bash
npx -y mcporter --config ~/.mcporter_config.json call notebooklm.notebook_query notebook_id="<UUID>" query="Summarize this"
```
