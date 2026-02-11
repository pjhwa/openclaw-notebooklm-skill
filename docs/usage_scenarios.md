# OpenClaw NotebookLM Usage Scenarios

This document outlines practical workflows for using the NotebookLM Skill within OpenClaw.

## 1. Automated Podcast Generation

**Goal**: Convert a technical PDF or URL into an engaging "Audio Overview" (podcast).

### Agent Workflow
1.  **Add Source**: The Agent adds the URL or file to a new notebook.
2.  **Generate Audio**: The Agent calls `studio_create` with `artifact_type="audio"`.
3.  **Wait**: The generation takes time. The Agent can poll `studio_status`.
4.  **Download**: Once complete, the Agent downloads the MP3 file.

### Example Prompt
> "Read this article about Quantum Computing [URL] and create a 5-minute podcast explaining it to a general audience. Save the MP3 file here."

### Underlying Commands
```bash
# 1. Create Notebook
uuid=$(mcporter call notebooklm.notebook_create --args '{"title": "Quantum Podcast"}')

# 2. Add Source (Important: Wait for completion)
mcporter call notebooklm.source_add --args '{"notebook_id": "$uuid", "source_type": "url", "url": "https://...", "wait": true, "wait_timeout": 600}'

# 3. Generate Audio
mcporter call notebooklm.studio_create --args '{"notebook_id": "$uuid", "artifact_type": "audio", "confirm": true}'

# 4. Download (after completion)
mcporter call notebooklm.download_artifact --args '{"notebook_id": "$uuid", "artifact_type": "audio", "output_path": "./podcast.mp3"}'
```

## 2. Deep Research Assistant

**Goal**: Research a complex topic using NotebookLM's "Deep Research" capabilities (which browse the web for sources).

### Agent Workflow
1.  **Start Research**: The Agent calls `research_start` with a query.
2.  **Monitor**: The Agent polls `research_status` to see progress (e.g., "Found 12 sources...").
3.  **Review**: Once complete, the Agent gets a summary report.
4.  **Import**: The Agent imports the best sources into the notebook for Q&A.

### Example Prompt
> "Research the current state of Solid State Batteries. Find at least 20 sources and summarize the key challenges."

### Underlying Commands
```bash
# 1. Start Deep Research
mcporter call notebooklm.research_start --args '{"query": "Solid State Battery challenges", "mode": "deep"}'

# 2. Check Status (Loop until completed)
mcporter call notebooklm.research_status --args '{"notebook_id": "$uuid"}'

# 3. Import Results
mcporter call notebooklm.research_import --args '{"notebook_id": "$uuid", "task_id": "$task_id"}'
```

## 3. Knowledge Base Q&A

**Goal**: Use NotebookLM as a RAG (Retrieval-Augmented Generation) engine for a set of documents.

### Agent Workflow
1.  **Upload Documents**: The Agent uploads multiple PDFs from a local folder.
2.  **Query**: The Agent asks specific questions. NotebookLM answers *only* using the provided sources (minimizing hallucinations).

### Example Prompt
> "I've uploaded the Q1, Q2, and Q3 financial reports. What is the trend in operating margin?"

### Underlying Commands
```bash
# 1. Add Local File
mcporter call notebooklm.source_add --args '{"notebook_id": "$uuid", "source_type": "file", "file_path": "/path/to/report_q1.pdf"}'

# 2. Query
mcporter call notebooklm.notebook_query --args '{"notebook_id": "$uuid", "query": "What is the trend in operating margin?"}'
```
