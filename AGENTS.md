# AGENTS.md

This repo is a Cline port of [obra/superpowers](https://github.com/obra/superpowers).

If you are an AI agent working **in this repo**:

- The skill content is upstream content. Don't rewrite it to "comply" with anyone's style guide. Change `.md` bodies only where a Claude-Code-specific mechanic must become a Cline one (the `Skill` tool → `/name.md` workflows; the `Task` tool / parallel subagents → `/newtask` sequential subtasks; `TodoWrite` → Cline's todo list; `EnterPlanMode`/`ExitPlanMode` → Plan/Act mode; plugin paths → `.clinerules/...`). See `.clinerules/cline-tools.md`.
- When you add a new workflow: drop `<name>.md` in `.clinerules/workflows/`, put supporting files in `.clinerules/workflows/<name>/`, and add it to the list in `.clinerules/superpowers.md` and `README.md`.
- Don't add third-party dependencies. The visual-companion server is intentionally zero-dependency Node.
- For substantive changes to behavior-shaping content, follow `/writing-skills.md` — write the failing pressure test first.

If you are an AI agent working **in another repo that has installed this**: see `.clinerules/superpowers.md` — that's your entry point.
