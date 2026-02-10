# 📓 OpenClaw NotebookLM Skill

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![MCP](https://img.shields.io/badge/MCP-Compatible-green)
![Platform](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-lightgrey)

> **Supercharge your AI Agent with Google's NotebookLM.**
> Generate podcasts, perform deep research, and query your knowledge base—all from within OpenClaw.

## 🌟 Why This Skill?

This skill integrates the power of **Google NotebookLM** directly into your OpenClaw agent, enabling capabilities that standard LLMs cannot match:

| Feature | Description |
| :--- | :--- |
| 🎙️ **Audio Overviews** | Generate "Deep Dive" podcasts from any document or URL. |
| 🧠 **Deep Research** | Autonomous web research agent that finds and summarizes 50+ sources. |
| 📚 **RAG Engine** | Query your specific documents with grounded answers (zero hallucinations). |
| ⚡ **MCP Native** | Built on the Model Context Protocol for seamless agent integration. |

---

## 🚀 capabilities

- **Manage Notebooks**: Create, list, delete, and rename.
- **Source Management**: Add URLs, PDFs (local), Drive files, and pasted text.
- **Studio Production**: Create Audio Overviews, FAQs, Study Guides, and Briefing Docs.
- **Note Taking**: Create and manage notes within notebooks.

## 📦 Installation

### 1. Auto-Install (OpenClaw)

Clone this repo into your agent's skills directory. OpenClaw handles the rest.

```bash
cd ~/.openclaw/workspace/skills
git clone https://github.com/pjhwa/openclaw-notebooklm-skill.git notebooklm
```

### 2. Dependencies

Ensure `uv` is installed for efficient Python package management.

**macOS / Linux:**
```bash
# macOS
brew install uv

# Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

The agent will automatically install `notebooklm-mcp-cli` when it first runs.

---

## 🔑 Authentication

**Robust & Secure**: We use a file-based cookie method to ensure stability in headless environments (Linux servers, Docker containers).

### Step 1: Extract Cookies
1.  Open [NotebookLM](https://notebooklm.google.com) in your desktop browser.
2.  Open **DevTools (F12)** → **Network** tab.
3.  Refresh the page.
4.  Find a request to `notebooklm.google.com` (e.g., `batchExecute`).
5.  Copy the **entire** `cookie` value from the **Request Headers**.

### Step 2: Save to File
Save this string to a file on the machine running OpenClaw.

```bash
mkdir -p ~/.nlm
nano ~/.nlm/cookies.txt
# Paste the cookie string and save
```

*(If you have `notebooklm-mcp-cli` installed locally, you can also run `nlm login --manual`)*

---

## 🛠️ Usage & Configuration

To enable the skill, register it with `mcporter` using the standard configuration command.

**Universal Config Command (Run Once):**

```bash
# Clear old configs
npx -y mcporter --config ~/.mcporter_config.json config remove notebooklm

# Register with Auth Injection
npx -y mcporter --config ~/.mcporter_config.json config add notebooklm --stdio \
  "bash -c 'export NOTEBOOKLM_COOKIES=\$(cat ~/.nlm/cookies.txt); notebooklm-mcp --transport stdio'"
```

> **Note**: We use `--config ~/.mcporter_config.json` to ensure a consistent configuration file location for both the User and the Agent.

---

## 🧪 Verification

Test your setup before unleashing the agent.

**1. Check Connection**
```bash
npx -y mcporter --config ~/.mcporter_config.json list notebooklm
```
*Expected: List of tools (`notebook_list`, `studio_create`, etc.)*

**2. Check Auth**
```bash
npx -y mcporter --config ~/.mcporter_config.json call notebooklm.notebook_list --args "{}"
```
*Expected: JSON list of your notebooks.*

---

## 📚 Documentation

Detailed guides for developers and power users:

- [**Architecture & Internals**](docs/architecture.md): Data flow, authentication logic, and SKILL.md structure.
- [**Advanced Scenarios**](docs/usage_scenarios.md): Workflows for "Podcast Generation", "Deep Research", and "RAG".

## 🤝 Contributing

Contributions are welcome! Please open an issue or pull request.

## 📄 License

MIT License
