---
name: cline-tools
description: Tool-name mapping - workflow text uses Claude Code tool names; this maps them to Cline equivalents.
---

# Cline Tool Mapping

The workflows in this library were ported from a Claude Code skill set. Where a workflow refers to a Claude Code tool, use the Cline equivalent below.

| Workflow says (Claude Code) | In Cline, do this |
|---|---|
| "Use the `Skill` tool" / "invoke the X skill" / "REQUIRED SUB-SKILL: X" | Run the `/X.md` workflow (type `/X.md` in chat). |
| `TodoWrite` / "create a todo" / "track with todos" | Use Cline's built-in todo list / focus chain. |
| `Task` tool / "dispatch a subagent" / "dispatch an agent" | Start a Cline subtask with `/newtask` (or the `new_task` tool). Cline runs subtasks **sequentially**, not in parallel — keep the isolation discipline, drop the wall-clock-parallelism expectation. |
| `EnterPlanMode` / `ExitPlanMode` / "present a plan" | Toggle Cline's **Plan** mode to design, then switch to **Act** mode to execute. |
| `Read` / `Glob` / `Grep` | Cline's `read_file`, `list_files`, `search_files` (or `<read_file>` / `<search_files>` XML tool calls inside a workflow). |
| `Edit` / `Write` | Cline's `replace_in_file` / `write_to_file`. |
| `Bash` / "run a command" | Cline's `execute_command` (or `<execute_command>` inside a workflow). |
| `AskUserQuestion` / "ask your human partner" | Cline's `ask_followup_question` (or `<ask_followup_question>`). |
| MCP tool calls | Cline's `use_mcp_tool` / `<use_mcp_tool>`. |
| Plugin paths like `${CLAUDE_PLUGIN_ROOT}/...` | Paths are relative to the repo: `.clinerules/workflows/<skill>/...`. |
| "your human partner" | The user. Keep treating them as a collaborator, not a command source to obey blindly. |

## On subtasks vs. subagents

Several workflows (`/dispatching-parallel-agents.md`, `/subagent-driven-development.md`, `/requesting-code-review.md`) were written for a harness with parallel, context-isolated subagents. Cline's `/newtask` gives you the **context isolation** (a fresh subtask with exactly the brief you write, returning a summary) but **not parallelism** — subtasks run one at a time. So:

- Keep: one subtask per independent unit of work; hand it a self-contained brief; have it return a summary; review between units.
- Drop: "all run concurrently", "time saved by parallelism". Do them in sequence.
