# Superpowers for Cline

A port of [obra/superpowers](https://github.com/obra/superpowers) — the popular Claude Code skills library — to **[Cline](https://cline.bot)**.

Same skills, same behavior-shaping content, repackaged using Cline's native primitives:

| Superpowers (Claude Code) | This repo (Cline) |
|---|---|
| `using-superpowers` skill, injected by a SessionStart hook | `.clinerules/superpowers.md` — an always-on [Cline rule](https://docs.cline.bot/customization/cline-rules), loaded into every task |
| Each other skill (`brainstorming`, `test-driven-development`, …), loaded on demand via the `Skill` tool | A [Cline workflow](https://docs.cline.bot/features/slash-commands/workflows) at `.clinerules/workflows/<name>.md`, invoked on demand via `/<name>.md` |
| `skills/<name>/SKILL.md` + supporting files | `.clinerules/workflows/<name>.md` + `.clinerules/workflows/<name>/…` |
| Claude Code tool names (`Skill`, `Task`, `TodoWrite`, `EnterPlanMode`) | Mapped in [`.clinerules/cline-tools.md`](.clinerules/cline-tools.md) (workflows → `/name.md`, subagents → `/newtask`, todos → Cline's todo list, plan → Plan/Act mode) |

The skill *content* is the upstream content, edited only where a Claude-Code mechanic had to become a Cline one. Two skills that lean on Claude Code's *parallel* subagents — `dispatching-parallel-agents` and `subagent-driven-development` — carry a short "⚠ Cline adaptation" note: Cline's `/newtask` gives you context-isolated subtasks but runs them sequentially, so you keep the discipline (isolate each unit, review between them) and drop the concurrency.

## What's included

Bootstrap rule:

- **`.clinerules/superpowers.md`** — "you have superpowers", how to find/run workflows, the red-flag table, skill priority. Always loaded.
- **`.clinerules/cline-tools.md`** — Claude-Code-tool → Cline-equivalent mapping.

Workflows (`.clinerules/workflows/`), each invoked as `/<name>.md`:

- `brainstorming` — turn an idea into a design before any creative work
- `writing-plans` — turn a spec into a step-by-step implementation plan
- `executing-plans` — execute a written plan with review checkpoints
- `subagent-driven-development` — execute plan tasks via isolated Cline subtasks (⚠ sequential)
- `dispatching-parallel-agents` — split independent work across isolated subtasks (⚠ sequential)
- `test-driven-development` — RED-GREEN-REFACTOR discipline for any feature or fix
- `systematic-debugging` — root-cause a bug before proposing fixes
- `requesting-code-review` — get a fresh-context review of completed work
- `receiving-code-review` — process review feedback with technical rigor
- `verification-before-completion` — prove work is done before claiming it
- `using-git-worktrees` — isolated workspace before feature work
- `finishing-a-development-branch` — merge / PR / cleanup decision after work is done
- `writing-skills` — author or edit a workflow (skill) itself

## Install

**Per-project (recommended):** copy the `.clinerules/` directory into the root of your project.

```bash
git clone https://github.com/salt-n-ice/superpowers-cline.git
cp -r superpowers-cline/.clinerules /path/to/your/project/
```

Cline auto-loads everything under `.clinerules/` (including subfolders), so `superpowers.md` and `cline-tools.md` become always-on rules and every file under `.clinerules/workflows/` becomes a slash command.

**Global (all projects):** copy each workflow file into `~/Documents/Cline/Workflows/`, and paste the contents of `superpowers.md` + `cline-tools.md` into your global Cline rules. (Cline's global location for workflows is `~/Documents/Cline/Workflows`; global rules go in `~/Documents/Cline/Rules`.)

## How to use

You usually don't have to do anything — `superpowers.md` instructs Cline to reach for the right workflow on its own (e.g. it runs `/brainstorming.md` before writing code for "let's build X", `/systematic-debugging.md` before proposing a bug fix).

To invoke one explicitly, type `/` in the Cline chat input and pick the workflow, or type its name directly: `/test-driven-development.md`, `/writing-plans.md`, etc.

## Acceptance check

Open a fresh Cline task in a project that has this `.clinerules/` and send:

> Let's make a react todo list

A working install responds by running the `brainstorming` workflow (asking clarifying questions, proposing approaches) **before** writing any code.

## Notes & limitations

- **Cline hooks aren't used.** Superpowers' Claude Code build uses a SessionStart hook; Cline hooks are macOS/Linux-only, so this port uses an always-on `.clinerules` rule instead — works on every OS, no scripts.
- **Subtasks are sequential.** See the `⚠ Cline adaptation` notes in `subagent-driven-development.md` and `dispatching-parallel-agents.md`.
- **`brainstorming`'s visual companion** is a small Node HTTP server (`.clinerules/workflows/brainstorming/scripts/`). It needs Node on `PATH`; in Cline run it with `--foreground` as a background command.
- **`writing-skills`** talks about authoring "skills" — in this repo that means authoring Cline workflows. The principles are unchanged.

## Credit

All skill content is from [obra/superpowers](https://github.com/obra/superpowers) by Jesse Vincent, MIT-licensed. This repo only re-homes it onto Cline's primitives and adjusts tool-specific mechanics. If you use Claude Code, use the original.
