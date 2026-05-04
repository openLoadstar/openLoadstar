> 🌐 **English** | **[한국어](README.ko.md)**

# openLoadstar

> **A guidance system for code and metadata, built for developers working with AI agents**

openLoadstar provides a metadata system based on a work unit called **WayPoint**, so that AI agents can precisely understand a project's intent and progress while working on it. Comprising four components — SPEC documents, a CLI tool, a web UI, and an MCP server — it lets you restore the same working context from any AI client environment (Claude Code, Cursor, Claude Desktop, and so on).

---

## 🤔 Why openLoadstar

If you collaborate with AI agents, these problems should look familiar:

- **Context vanishes when a session ends** — you end up re-explaining the same things in the next conversation.
- **Code and docs drift apart** — there's no metadata that says "what's being worked on now, where you stopped."
- **AI burns tokens scanning the whole codebase** — there's no scope to constrain it.
- **"What and why" lives nowhere in the code** — intent is scattered across PR descriptions and Slack threads.

openLoadstar solves this with **file-based metadata (WayPoint · Map · Decision)**. At session start, the AI reads a single entry-point file to restore context, scans only within the directories defined in `CODE_MAP`, and tracks progress through `TECH_SPEC` checkboxes.

### Design Principle: Tolerable Consistency

> openLoadstar **does not aim for perfect consistency** between code and metadata.
> The cost of updating every metadata file on every code change exceeds the benefit. Instead, the goal is "knowing where the drift is" — `SYNCED_AT` and change logs let you monitor accumulated drift and reconcile it periodically.

> 💡 **Drift** — a state of mismatch between code and metadata (`.loadstar/`). For example, when code has changed but a WayPoint's `TECH_SPEC` or `CODE_MAP` has not been updated. The goal is **awareness and management**, not elimination.

---

## 🧭 Core Concepts & Terminology

### Five Elements

| Element | Prefix | Role |
|:---|:---|:---|
| **Map** | `M://` | An index for grouping WayPoints (hierarchical paths) |
| **WayPoint** | `W://` | **The execution unit for all work** (intent, TECH_SPEC, CODE_MAP, progress) |
| **Link** | `L://` | Logical relations between elements (reference, sequence, test) |
| **SavePoint** | `S://` | Physical coordinates (Git commits, files, line ranges) |
| **Decision** | — | Decision records for OPEN_QUESTIONS (ADR pattern) |

### Glossary

| Term | Meaning |
|:---|:---|
| **Drift** | A mismatch between code and metadata (`.loadstar/`). The goal is **awareness and management**, not elimination. |
| **TECH_SPEC** | Work-item checkboxes inside a WayPoint. `[ ]` pending / `[x] YYYY-MM-DD` done. |
| **CODE_MAP** | A directory-level scope that constrains the AI's search range when a WayPoint involves code changes — keeps lookup costs down. |
| **STATUS** | `S_IDL` (idle) / `S_PRG` (in progress) / `S_STB` (stable) / `S_ERR` (error) / `S_REV` (review needed) |
| **TODO state** | `PENDING` / `ACTIVE` / `BLOCKED` / `COMPLETED` / `FAILED` |
| **SYNCED_AT** | The time the element was last reconciled with actual code. If it's older than 30 days, recheck the CODE_MAP scope before working. |
| **OPEN_QUESTIONS** | Unresolved questions that need a human decision before work can begin. The AI must ask the user when any are unresolved. |
| **`.loadstar/`** | The metadata root directory (Maps, WayPoints, Decisions, etc.). |
| **`.clionly/`** | `.loadstar/.clionly/` — CLI-only territory. **Do not access directly** from either AI or human (logs, TODO_LIST, snapshots). |
| **LOADSTAR_INIT.md** | The AI session entry-point file. Read at the start of every session to restore project context. |

### Address Convention

```
M://root/cli                 →  .loadstar/MAP/root.cli.md
W://root/cli/cmd_show        →  .loadstar/WAYPOINT/root.cli.cmd_show.md
```

For the full SPEC, see **[openLoadstar/spec](https://github.com/openLoadstar/spec)**.

---

## 📦 Four Components

| Component | Role | Stack | Repository |
|:---|:---|:---|:---|
| **spec** | The LOADSTAR methodology specification (SPEC documents) | Markdown | [openLoadstar/spec](https://github.com/openLoadstar/spec) |
| **cli** | A CLI tool for managing WayPoints, Maps, and TODOs | Go + cobra | [openLoadstar/cli](https://github.com/openLoadstar/cli) |
| **ui** | An Explorer web UI (visualization and editing) | Spring Boot 3 + React 19 | [openLoadstar/ui](https://github.com/openLoadstar/ui) |
| **mcp** | An MCP server for external AI clients (Claude Desktop, Cursor, etc.) | Python 3.10+ | [openLoadstar/mcp](https://github.com/openLoadstar/mcp) |

### Which Combination Should You Use

| Use case | Components |
|:---|:---|
| You only use it from an IDE-integrated environment like Claude Code | **cli** (required) |
| You want to visualize, search, and edit on the web | cli + **ui** |
| You use external AI clients (Claude Desktop, Cursor, etc.) | cli + **mcp** |
| All of the above, integrated | cli + ui + mcp |

> **spec** is used as a reference document in every scenario — you don't install it directly; you visit the SPEC repo when needed.

---

## 🛠️ Installation Guide

> For detailed build and installation instructions for each component, follow the README in the respective repo. Here we only cover the **end-to-end flow**.

### Step 1. Install the CLI (required)

```bash
git clone https://github.com/openLoadstar/cli.git
cd cli
go build -o bin/loadstar.exe .
```

Add `bin/loadstar.exe` to your PATH or invoke it by absolute path.

### Step 2. Install the UI (recommended)

The CLI alone covers all features, but pairing it with the web UI makes WayPoint, Map, and TODO visualization, search, graph navigation, and editing far more efficient. For projects with many WayPoints, it's effectively required.

```bash
git clone https://github.com/openLoadstar/ui.git
cd ui
# Backend (Spring Boot)
cd backend && mvn spring-boot:run
# Frontend (Vite)
cd frontend && npm install && npm run build
```

Default URL: `http://localhost:8080`

### Step 3. Install the MCP server (optional)

To call LOADSTAR tools from external AI clients like Claude Desktop or Cursor:

```bash
git clone https://github.com/openLoadstar/mcp.git
cd mcp
pip install -e .
# or: uv sync
```

For Claude Desktop, register it in `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "loadstar": {
      "command": "python",
      "args": ["-m", "loadstar_mcp"],
      "env": {
        "LOADSTAR_CLI_PATH": "C:/path/to/loadstar.exe",
        "LOADSTAR_SPEC_PATH": "C:/path/to/loadstar_SPEC"
      }
    }
  }
}
```

For details, see the [openLoadstar/mcp](https://github.com/openLoadstar/mcp) README.

---

## 🤖 AI Session Entry Prompt (required)

> ⚠️ **Always load the SPEC first when starting a new working session with an AI.** If the AI touches metadata without understanding LOADSTAR's element formats, address conventions, and operational rules, you'll break consistency. The SPEC is a set of markdown files in the `openLoadstar/spec` repo — load it once per session and it stays in context.

Below is an example of an actual **session entry prompt (`/loadstar-enter`)**. You can use it as a slash command in Claude Code, a rule in Cursor, or simply as the first message of a chat.

````markdown
Review the current state of this project against the LOADSTAR SPEC and identify what needs to be done today.

## Entry Procedure (strict)

1. **Load the SPEC** — From `<SPEC_PATH>/`, read 01, 02, 03, 05, 07, 08 to understand
   element formats, the address convention, and the AI operational protocol
   (lazy-load only what you need).
2. **Project entry point** — Read `.loadstar/LOADSTAR_INIT.md` to pick up
   AI notes and progress context.
3. **WayPoint state snapshot** — Run:
   - `loadstar show`             # all WPs with STATUS
   - `loadstar todo list`        # PENDING / ACTIVE / BLOCKED
   - `loadstar validate`         # detect broken references
   - `loadstar question`         # unresolved OPEN_QUESTIONS
4. **Inspect ACTIVE / S_PRG WPs in depth** — For in-progress WPs only,
   read `.loadstar/WAYPOINT/*.md` and check the TECH_SPEC checkbox state.

## Rules (strict)

- **Do not read source code** — Judge from metadata first. Only read source after
  deciding to implement.
- **Do not modify code without an item** — If TECH_SPEC has no entry,
  add `- [ ] task description` to the WP first.
- **STATUS transitions** — On start: `S_IDL → S_PRG`. On full completion: `[x]` then `S_STB`.
- **WPs with SYNCED_AT older than 30 days** — Warn and recommend rechecking the
  CODE_MAP scope before working.
- **Unresolved OPEN_QUESTIONS** — Ask the user before proceeding.
- **Do not access `.clionly/` directly** — Read TODO and LOG only via the CLI.

## Output Format

### ACTIVE WayPoints
- W://address — SUMMARY (TECH_SPEC: N done / M total)

### ⚠️ Warnings
- SYNCED_AT > 30 days / broken references / unresolved OPEN_QUESTIONS

### 💡 Recommended for today (P1 → P3)
1. [P1] W://address — reason
````

> 📌 Replace `<SPEC_PATH>` with your local spec repo path (e.g. `C:/work/openLoadstar/spec` or `~/oss/spec`). **If you're using the MCP server**, the `loadstar_get_spec` tool returns the SPEC automatically and no path is needed.

### Telling the AI your project path

After the entry prompt loads the SPEC, the next step is to tell the AI **your project path**. The AI will read `.loadstar/` metadata in that directory to restore the working context.

```
Project path: C:/work/my-project
```

Or combine both into a single message:

```
/loadstar-enter

Project path: ~/work/my-project
```

### Tip for Claude Code users

In Claude Code, **user memory (auto memory)** persists across sessions.

- Tell the AI your project path, SPEC path, or preferred entry steps once, and it gets saved to memory files automatically — **the next session restores them without you typing anything**.
- Pin the entry prompt (`/loadstar-enter`) itself as a slash command or in `CLAUDE.md`, and you only need a one-line invocation each session.
- Add LOADSTAR working rules to a per-project `CLAUDE.md`, and together with user memory the AI will load a consistent flow at every session start.

> Other environments behave similarly — pinning the entry procedure in `.cursorrules` or a system prompt in Cursor achieves the same effect.

---

Once the entry prompt has run, the AI keeps the restored context throughout the session and can handle ordinary task requests like "add feature X" or "diagnose this bug."

---

## 🚀 Quick Start

### A. Starting from a new project

In a new project directory:

```bash
loadstar init
```

This creates the `.loadstar/` directory and the entry-point file (`LOADSTAR_INIT.md`). Now create your first WayPoint and define the work — ask the AI like this:

#### 💬 AI prompt example — new project

```
Build the initial LOADSTAR structure for this project.

- Project overview: [content]
- Main features: [list]
- First Map: M://root
- First WayPoint: W://root/initial_setup

Register these initial setup items into the TECH_SPEC of W://root/initial_setup:
- [ ] Define directory structure
- [ ] Write dependency installation script
- [ ] Add the first unit test case
```

### B. Applying it to an existing project

To introduce LOADSTAR metadata into a codebase you already have:

```bash
cd <existing-project>
loadstar init
```

Once the entry-point file is created, ask the AI to analyze the codebase and extract the initial Map/WayPoint structure.

#### 💬 AI prompt example — existing project

```
I want to organize this codebase with LOADSTAR metadata.

1. Add a LOADSTAR rules section to CLAUDE.md (refer to spec/01.MASTER_GUIDE.md).
2. Analyze the main modules and create a Map hierarchy — e.g., M://root/backend, M://root/frontend.
3. Define each module's primary responsibility as a WayPoint. Specify the
   target directory in CODE_MAP.scope.
4. If there's any work in progress, register it under the corresponding
   WayPoint's TECH_SPEC as a [ ] item.

Afterwards, run `loadstar show` and `loadstar validate` to confirm there
are no broken references.
```

---

## ⚠️ Things to Watch Out For

### Hard rules
- **Do not access `.loadstar/.clionly/` directly** — It's CLI-only territory. Even AI agents must not read or write here. Direct access permanently breaks consistency between LOG and the actual metadata state.

### Recommended rules
- **Do not modify code without an item** — Before changing code, register `- [ ] description` in the target WayPoint's TECH_SPEC. For quick bug fixes, registering after the fact is acceptable.
- **STATUS transition timing**:
  - On start: `S_IDL → S_PRG`
  - When all TECH_SPEC items become `[x]`: `S_PRG → S_STB`
- **WPs with SYNCED_AT older than 30 days** — Verify that the CODE_MAP scope is still valid before working.
- **Unresolved OPEN_QUESTIONS** — The AI must confirm a decision with the user before proceeding.

### Practical operating principles
- **Don't try to keep metadata 100% in sync** — Tolerable Consistency. Drift is natural and is managed via `loadstar validate` and periodic reconciliation sessions.
- **Auto-load `LOADSTAR_INIT.md` at AI session start** — Pin the entry procedure in CLAUDE.md, `.cursorrules`, or a system prompt so context is restored automatically every session.

---

## 📚 Learn More

- 📖 **SPEC documents**: [openLoadstar/spec](https://github.com/openLoadstar/spec) — element formats, address convention, AI operational protocol
- 🛠️ **CLI command reference**: [openLoadstar/cli](https://github.com/openLoadstar/cli)
- 🖥️ **UI usage guide**: [openLoadstar/ui](https://github.com/openLoadstar/ui)
- 🔌 **MCP tool spec**: [openLoadstar/mcp](https://github.com/openLoadstar/mcp)

---

## 📮 Contact & Support

- 🐛 **Bug reports**: [GitHub Issues](https://github.com/openLoadstar/openLoadstar/issues)
- 💬 **Questions & ideas**: [GitHub Discussions](https://github.com/openLoadstar/openLoadstar/discussions)
- 🤝 **Contributing**: [CONTRIBUTING.md](./CONTRIBUTING.md)
- 🔒 **Security vulnerabilities**: [SECURITY.md](./SECURITY.md) — private reporting procedure
- 👤 **Maintainer**: [@aeolusk](https://github.com/aeolusk)

---

## 📄 License

[Apache License 2.0](./LICENSE)
