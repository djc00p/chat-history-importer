# 📥 Chat History Importer

[![ClawHub Skill](https://img.shields.io/badge/ClawHub-Skill-blue)](#) [![Python 3.x](https://img.shields.io/badge/python-3.x-blue)](#)

**Chat History Importer** is a specialized utility designed to bridge the gap between your past AI interactions and your agent's future intelligence. It parses chat export files from **OpenCA (ChatGPT)** and **Anthropic (Claude)** and transforms raw JSON data into structured, searchable **episodic memory** for AI agents.

Instead of dumping raw logs, this tool creates dated, human-readable Markdown summaries (`memory/episodic/YYYY-MM-DD.md`) that allow your agent to "remember" historical context, instructions, and important milestones as if they were recent events.

---

## ✨ Key Features

* **🧠 Episodic Memory Generation:** Automatically organizes conversations into daily Markdown files, making it easy for agents to browse history chronologically.
* **🔍 Intelligent Auto-Detection:** No need to specify formats. The importer detects whether your source is OpenAI or Anthropic automatically.
* **🆔 Identity Mapping:** Transforms generic `user:` and `assistant:` labels into personalized names (e.g., `Kael:` and `Deonte:`) by reading from your workspace's `IDENTITY.md` or `USER.md`.
* **🚫 Smart Deduplication:** Uses unique Chat IDs to track imports. You can run the same export multiple times without creating duplicate entries in your memory.
* **🛠️ Integrated Workflow:** Designed to work seamlessly with the [chat-learnings-extractor](https://clawhub.ai/djc00p/chat-learnings-extractor) to turn imported history into actionable insights.

---

## 🚀 Quick Start

### 1. Prepare your exports

* **OpenAI:** Go to *Settings* $\rightarrow$ *Data Controls* $\rightarrow$ *Export data*. Look for `conversations.json`.
* **Anthropic:** Go to *Settings* $\rightarrow$ *Privacy* $\rightarrow$ *Export data*. Unzip the provided archive to find `conversations.json`.

### 2. Setup Environment

Ensure you have `python3` installed and set your workspace variable so the importer knows where to write the memory:

```bash
export OPENCLAW_WORKSPACE="/path/to/your/agent_workspace"
```

### 3. Run a Dry Run

Always run a dry run first to see what will happen without altering your memory:

```bash
python3 scripts/batch.py --dir ~/Downloads/chat_exports --dry-run --verbose
```

### 4. Execute Import

Once you are satisfied with the preview, run the actual import:

```bash
python3 scripts/batch.py --dir ~/Downloads/chat_exports
```

## 🛠 Usage Reference

| Argument | Type | Description | Example |
| :--- | :--- | :--- | :--- |
| `--dir` | `path` | The directory containing your `conversations.json` files. | `--dir ~/Downloads/exports` |
| `--dry-run` | `flag` | Preview the import process without writing to disk. | `--dry-run` |
| `--since` | `date` | Only import conversations from this date onwards (`YYYY-MM-DD`). | `--since 2025-01-01` |
| `--format` | `string` | Forces a specific format (`openai` or `anthropic`). | `--format anthropic` |
| `--agent-name`| `string` | Overrides the agent's name in the generated logs. | `--agent-name "Kael"` |
| `--user-name` | `string` | Overrides the user's name in the generated logs. | `--user-name "Deonte"` |
| `--verbose` | `flag` | Show detailed message counts and message previews. | `--verbose` |

---

#### Examples

Importing only recent history (since Jan 1st, 2025):

```bash
python3 scripts/batch.py --dir ~/Downloads/exports --since 2025-01-01
```

Running a high-visibility preview of a large folder:

```bash
python3 scripts/batch.py --dir ~/Downloads/exports --dry-run --verbose
```

## 🏗 Architecture & Logic

### The Memory Pipeline

1. Ingestion: The script scans the target directory for valid JSON exports.
2. Parsing: It identifies the schema (OpenAI vs. Anthropic) and extracts timestamps, roles, and message content.
3. Identity Resolution: The script looks for IDENTITY.md and USER.md in your OPENCLAW_WORKSPACE. If found, it replaces user with your name and assistant with the agent's name.
4. Aggregation: Messages are grouped by their original timestamp.
5. Persistence: Data is written to memory/episodic/Y-MM-DD.md.

### Deduplication Strategy

To prevent "memory bloat," the importer maintains a record of processed Chat IDs. If a Chat ID already exists in your episodic memory, the script skips it entirely.

---

## 🔗 Related Projects

[ClawHub Ecosystem](https://clawhub.ai/): The platform where these skills come to life.
[chat-learnings-extractor](https://github.com/djc00p/chat-learnings-extractor): The companion tool to analyze the memories you just imported.

Original implementation by @djc00p
