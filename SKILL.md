---
name: notebooklm
description: comprehensive Agent interface for Google NotebookLM. Create notebooks, add sources (PDF/Web/Drive), perform Deep Research, and generate Studios (Podcasts/Quizzes).
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
          ],
      },
  }
---

# NotebookLM Agent Skill

This skill empowers the Agent to use **Google NotebookLM** as a research, synthesis, and content generation engine.

## 🧠 Core Capabilities

The Agent can perform the following high-level tasks:
1.  **Deep Research**: Autonomously research a topic on the web and summarize findings.
2.  **RAG (Retrieval-Augmented Generation)**: Answer questions based strictly on provided documents (PDF, Drive, URL).
3.  **Content Studio**: Generate "Deep Dive" Audio Overviews (Podcasts), Briefing Docs, FAQs, and Study Quizzes.
4.  **Knowledge Management**: Create/Edit notes and organize sources within notebooks.

## 🛠️ Tool Reference & Usage Patterns

### 1. Notebook Operations
Manage the workspace. **Always list notebooks first** to get valid UUIDs.

```bash
# List all notebooks (Required before any operation to find UUIDs)
mcporter call notebooklm.notebook_list --args "{}"

# Create a new project
mcporter call notebooklm.notebook_create --args '{"title": "Project Alpha"}'

# Delete a notebook (Irreversible)
mcporter call notebooklm.notebook_delete --args '{"notebook_id": "UUID", "confirm": true}'
```

---

### 2. Source Management (CRITICAL: Smart Polling)
Adding sources takes time to index. **The Agent MUST verify source readiness before querying.**

**Workflow:**
1.  Call `source_add` with `wait: true`.
2.  **IMMEDIATELY** verify status using `notebook_get`.
3.  Loop until all sources show `"ready": true` (or `"status": "complete"`).
4.  Only then proceed to query.

```bash
# 1. Add Source (URL)
mcporter call notebooklm.source_add --args '{"notebook_id": "UUID", "source_type": "url", "url": "https://example.com", "wait": true}'

# 2. Add Source (Text/Paste)
mcporter call notebooklm.source_add --args '{"notebook_id": "UUID", "source_type": "text", "text": "Analysis content...", "title": "My Notes", "wait": true}'

# 3. VERIFY STATUS (Mandatory Step)
# Call this loop until sources are ready.
mcporter call notebooklm.notebook_get --args '{"notebook_id": "UUID"}'
```

**Supported Source Types**: `url` (Web/YouTube), `text` (Pasted), `file` (Local PDF/MP3/Txt), `drive` (Google Drive).

---

### 3. Deep Research Agent
Delegate complex web research to NotebookLM.

**Workflow:**
1.  `research_start` -> Returns `task_id`.
2.  `research_status` -> Loop every 30s until `status: "completed"`.
3.  `research_import` -> Import found sources into the notebook.

```bash
# Start Research
mcporter call notebooklm.research_start --args '{"query": "Latest advances in Solid State Batteries", "mode": "deep", "notebook_id": "UUID"}'

# Check Progress (Wait 30s between calls!)
mcporter call notebooklm.research_status --args '{"notebook_id": "UUID", "task_id": "TASK_UUID"}'

# Import Results
mcporter call notebooklm.research_import --args '{"notebook_id": "UUID", "task_id": "TASK_UUID"}'
```

---

### 4. Studio Generation (Podcasts, Quizzes, etc.)
Generate artifacts from sources.

**Supported Types**:
*   `audio`: Audio Overview (Podcast).
*   `quiz`: Learning Quiz (Multiple Choice).
*   `faq`: Frequently Asked Questions.
*   `study_guide`: Study Guide.
*   `briefing_doc`: Briefing Document.

```bash
# Generate Podcast (Deep Dive)
mcporter call notebooklm.studio_create --args '{"notebook_id": "UUID", "artifact_type": "audio", "audio_format": "deep_dive", "confirm": true}'

# Generate Quiz (Great for YouTube videos)
mcporter call notebooklm.studio_create --args '{"notebook_id": "UUID", "artifact_type": "quiz", "confirm": true}'

# Check Status (Wait 30s between calls!)
mcporter call notebooklm.studio_status --args '{"notebook_id": "UUID"}'

# Download Artifact
mcporter call notebooklm.download_artifact --args '{"notebook_id": "UUID", "artifact_type": "audio", "output_path": "./podcast.mp3"}'
```

---

### 5. Note Management
Interact with specific notes (atomic thoughts) within a notebook.

```bash
# List Notes
mcporter call notebooklm.note --args '{"notebook_id": "UUID", "action": "list"}'

# Create Note
mcporter call notebooklm.note --args '{"notebook_id": "UUID", "action": "create", "content": "My observation...", "title": "Observation 1"}'
```

---

## 🛡️ Best Practices & Failure Prevention

To ensure 100% success rate, the Agent MUST follow these rules:

1.  **Smart Polling (No Blind Waits)**:
    *   Never assume a task is done just because you waited X seconds.
    *   Always check `notebook_get` (for sources), `research_status` (for research), or `studio_status` (for artifacts).
    *   **Rule**: `while status != complete: wait(30s); check_status()`

2.  **Rate Limiting**:
    *   NotebookLM has rate limits. Do not poll faster than once every 10-20 seconds.
    *   If you receive a 429 error, sleep for 60 seconds.

3.  **Source Limits**:
    *   Keep inputs under **350,000 words** or **200MB** per source.
    *   If a source is too large, split it or summarize it first.

4.  **Google Drive Access**:
    *   Ensure the `cookies.txt` account has permissions to the Drive file.
    *   For shared files, make sure they are "Anyone with link can view" or shared directly.

---

## ⚙️ Configuration (User Setup)

For the Agent to work, `notebooklm-mcp` must be authenticated.

**Robust Auth Setup (Recommended)**:
1.  Save browser cookies to `~/.nlm/cookies.txt`.
2.  Register with auth injection:

**Linux / macOS:**
```bash
npx -y mcporter --config ~/.mcporter_config.json config add notebooklm --stdio \
  "bash -c 'export NOTEBOOKLM_COOKIES=\$(cat ~/.nlm/cookies.txt); notebooklm-mcp --transport stdio'"
```

**Windows (PowerShell):**
```powershell
npx -y mcporter --config "$HOME\.mcporter_config.json" config add notebooklm --stdio \
  "powershell -c \"`$env:NOTEBOOKLM_COOKIES = Get-Content '$HOME\.nlm\cookies.txt' -Raw; notebooklm-mcp --transport stdio\""
```
