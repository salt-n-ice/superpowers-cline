# Superpowers for Cline

A port of [obra/superpowers](https://github.com/obra/superpowers) — the popular Claude Code skills library — to **[Cline](https://cline.bot)**.

Same skills, same behavior-shaping content, repackaged using Cline's native primitives:

| Superpowers (Claude Code) | This repo (Cline) |
|---|---|
| `using-superpowers` skill, injected by a SessionStart hook | `.clinerules/superpowers.md` — an always-on [Cline rule](https://docs.cline.bot/customization/cline-rules), loaded into every task |
| Each other skill (`brainstorming`, `test-driven-development`, …), loaded on demand via the `Skill` tool | A [Cline workflow](https://docs.cline.bot/features/slash-commands/workflows) at `.clinerules/workflows/<name>.md`, invoked on demand via `/<name>.md` |
| `skills/<name>/SKILL.md` + supporting files | `.clinerules/workflows/<name>.md` + `.clinerules/workflows/<name>/…` |
| Claude Code tool names (`Skill`, `Task`, `TodoWrite`, `EnterPlanMode`) | Mapped in [`.clinerules/cline-tools.md`](.clinerules/cline-tools.md) (workflows → `/name.md`, subagents → `/newtask`, todos → Cline's todo list, plan → Plan/Act mode) |

The skill *content* is the upstream content, edited only where a Claude-Code mechanic had to become a Cline one. Two upstream skills are built on Claude Code's *parallel* subagents, which Cline doesn't have — `subagent-driven-development` and `dispatching-parallel-agents` are therefore **redirect stubs** that point at `long-haul-development` (a Cline addition), which gets the parts that matter — one isolated context per unit of work, review gates, task-by-task execution — via a sequential `/newtask` relay plus disk-backed state.

## What's included

Bootstrap rule:

- **`.clinerules/superpowers.md`** — "you have superpowers", how to find/run workflows, the red-flag table, skill priority. Always loaded.
- **`.clinerules/cline-tools.md`** — Claude-Code-tool → Cline-equivalent mapping.

Workflows (`.clinerules/workflows/`), each invoked as `/<name>.md`:

- `brainstorming` — turn an idea into a design before any creative work
- `writing-plans` — turn a spec into a step-by-step implementation plan
- `long-haul-development` — **Cline addition; the plan-execution workflow for Cline.** Execute an implementation plan task-by-task with review gates: disk-backed plan + journal, relayed across `/newtask` tasks so a large job stays under the context budget. See [below](#long-haul-development).
- `executing-plans` — execute a written plan inline in one session (use only for a plan small enough to finish without filling the window)
- `subagent-driven-development` — redirect → `long-haul-development` (Cline has no subagents)
- `dispatching-parallel-agents` — redirect → `long-haul-development` (Cline has no parallel agents)
- `test-driven-development` — RED-GREEN-REFACTOR discipline for any feature or fix
- `systematic-debugging` — root-cause a bug before proposing fixes
- `requesting-code-review` — get a fresh-context review of completed work
- `receiving-code-review` — process review feedback with technical rigor
- `verification-before-completion` — prove work is done before claiming it
- `using-git-worktrees` — isolated workspace before feature work
- `finishing-a-development-branch` — merge / PR / cleanup decision after work is done
- `writing-skills` — author or edit a workflow (skill) itself
- `ultrathink` — **Cline addition (not from upstream).** Forces a deep, written, multi-phase reasoning pass on a hard task before acting, and guards against the model stopping its thinking early. See [below](#ultrathink).

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

## ultrathink

`ultrathink` is not part of upstream superpowers — it's a Cline-specific addition for models that tend to cut their reasoning short. Invoke it with `/ultrathink.md` on a complex, ambiguous, or high-stakes task and the model must work through (and **write out**) six reasoning phases before it acts: restate the problem → surface and verify assumptions → enumerate 2–3 approaches → adversarially attack the leading one → decide on the reasoning that survived → plan the execution. It also carries a "you're stopping too early" red-flags table and a hard completion gate (no editing/answering until the phases are visibly done).

It's orthogonal to the other workflows — use it *alongside* `brainstorming` / `systematic-debugging` / `writing-plans` when the task warrants it; it makes the front of those rigorous, it doesn't replace them. The file is self-contained, so you can also drop just `.clinerules/workflows/ultrathink.md` into a project on its own.

## long-haul-development

Also a Cline addition, for the case where the work is bigger than your context window — large codebase, multi-file refactor, or a small-context model that fills up fast. Instead of trying to hold the plan + the codebase + the progress in context (and hitting the wall mid-task), it treats **disk as memory**: a git-ignored `.cline/plan.md` (checkbox task list, source of truth) and `.cline/journal.md` (append-only log of what's done, which files matter and why, gotchas, what's next). The agent does one bounded task, updates both files, checks its context budget, and when it's getting full it writes a tiny handoff and relays to a fresh task via `/newtask` — which reads the two files and continues. Repeat until done. It also enforces surgical reading (grep to the lines, don't slurp whole files), which is the single biggest context saver in a big repo.

It's the no-subagent, context-bounded counterpart to upstream's `subagent-driven-development` — same isolation-per-task and review-gate benefits, achieved by relaying fresh tasks instead of dispatching subagents — which is why `subagent-driven-development` and `dispatching-parallel-agents` are just redirect stubs here. Invoke `/long-haul-development.md` (it'll bootstrap a plan via `/writing-plans.md` if you don't have one). Requires Cline's `/newtask` to be available.

## Notes & limitations

- **Cline hooks aren't used.** Superpowers' Claude Code build uses a SessionStart hook; Cline hooks are macOS/Linux-only, so this port uses an always-on `.clinerules` rule instead — works on every OS, no scripts.
- **No subagents.** Cline's `/newtask` is a sequential baton pass, not a parent-and-worker. `subagent-driven-development` and `dispatching-parallel-agents` are redirect stubs → use `long-haul-development` (or, for a few independent fixes, just do them as sequential tasks).
- **`brainstorming`'s visual companion** is a small Node HTTP server (`.clinerules/workflows/brainstorming/scripts/`). It needs Node on `PATH`; in Cline run it with `--foreground` as a background command.
- **`writing-skills`** talks about authoring "skills" — in this repo that means authoring Cline workflows. The principles are unchanged.

## Credit

All skill content is from [obra/superpowers](https://github.com/obra/superpowers) by Jesse Vincent, MIT-licensed. This repo only re-homes it onto Cline's primitives and adjusts tool-specific mechanics. If you use Claude Code, use the original.
