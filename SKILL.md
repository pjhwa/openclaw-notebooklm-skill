---
name: notebooklm
description: Interact with NotebookLM via MCP (notebooklm-mcp-cli). Manage notebooks, query sources, and download artifacts.
homepage: https://github.com/jacob-bd/notebooklm-mcp-cli
metadata:
  {
    "openclaw":
      {
        "emoji": "📓",
        "requires": { "bins": ["notebooklm-mcp"] },
        "install":
          [
            {
              "id": "uv",
              "kind": "uv",
              "package": "notebooklm-mcp-cli",
              "bins": ["notebooklm-mcp"],
              "label": "Install NotebookLM MCP (uv)",
            },
            {
              "id": "pip",
              "kind": "uv",
              "package": "notebooklm-mcp-cli",
              "bins": ["notebooklm-mcp"],
              "label": "Install NotebookLM MCP (uv/pip)",
            }
          ],
      },
  }
---

# NotebookLM MCP

This skill provides access to [NotebookLM](https://notebooklm.google.com) features via the Model Context Protocol (MCP).

## Installation

Recommended installation via `uv` (handled automatically by the install button above):

```bash
uv tool install notebooklm-mcp-cli
```

Or via `pip`:

```bash
pip install notebooklm-mcp-cli
```

## Authentication

### Desktop (Interactive)

Run the interactive authentication tool to log in and save credentials:

```bash
nlm login
```

### Server / Headless (Linux)

For headless servers, you must manually exact cookies and set an environment variable.

1.  **Extract Cookies**:
    *   Open [NotebookLM](https://notebooklm.google.com) in your local browser.
    *   Open Developer Tools (F12) -> **Network** tab.
    *   Refresh the page.
    *   Click on a request (e.g., `batchExecute`).
    *   Copy the value of the `cookie` request header.

2.  **Set Environment Variable**:
    Export the cookies in your shell (or `.env` file):

    ```bash
    export NOTEBOOKLM_COOKIES="Values from step 1..."
    ```

    **Or from a file:**
    If you saved cookies to `~/.nlm/cookies.txt`:
    ```bash
    export NOTEBOOKLM_COOKIES=$(cat ~/.nlm/cookies.txt)
    ```

    *Tip: Ensure you quote the string to handle special characters.*

### Usage with mcporter

**Robust Configuration (Recommended for Linux):**
This method reads cookies directly from a file at runtime, avoiding special character issues in environment variables.

1.  Save your cookies to `~/.nlm/cookies.txt`.
2.  Configure mcporter:

```bash
# Remove old config
npx -y mcporter config remove notebooklm

# Add new config with explicit stdio transport
npx -y mcporter config add notebooklm --stdio "bash -c 'export NOTEBOOKLM_COOKIES=\$(cat ~/.nlm/cookies.txt); notebooklm-mcp --transport stdio'"
```

> **IMPORTANT**: Do NOT run the `notebooklm-mcp` server manually in a separate terminal. OpenClaw (`mcporter`) will start and stop it automatically as needed.

Then verify:

```bash
npx -y mcporter list notebooklm
```

Then you can call tools:

```bash
npx -y mcporter call notebooklm.notebook_query notebook_id="<UUID>" query="Summarize this"
```
