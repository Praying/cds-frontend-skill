# cds-frontend-skill

An agent skill for designing, implementing and refactoring frontend UI in the Claude Design System (CDS) style. Works with Claude Code, Cursor and Codex.

## Attribution

This skill is distilled from [Caho1/open-claude-ui](https://github.com/Caho1/open-claude-ui) (MIT, Copyright (c) 2026 Caho1), in particular its `AGENTS.md` and the v2 analysis manual (`docs/v2`, Claude Desktop 2.0 v2.37337.1). The design tokens, spring presets and findings come from that project; this repo only repackages them as a workflow skill. It is not affiliated with or endorsed by Anthropic.

## Install

| Tool | How |
|---|---|
| Claude Code | copy or symlink `skills/cds-frontend` to `.claude/skills/` (project) or `~/.claude/skills/` (global) |
| Codex | copy or symlink to `.agents/skills/` |
| Cursor | copy `.cursor/rules/cds-frontend.mdc` and `skills/` into your project |

The skill refers to `AGENTS.md`, `code-examples/` and `docs/v2/` of the upstream repo; keep a clone nearby for full detail.

## Layout
- `skills/cds-frontend/SKILL.md`: workflow, decision rules, guards, audit checklist
- `skills/cds-frontend/references/v2-delta.md`: CDS 1.x to 2.0 token and motion changes

## License
MIT, see [LICENSE](LICENSE).
