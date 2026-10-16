# agents

The rules file I give AI coding agents when I build apps.

- [`AGENTS.md`](AGENTS.md) is the full set of rules. It works with Claude Code, Codex, Cursor and Copilot.
- [`CLAUDE.md`](CLAUDE.md) imports `AGENTS.md`, so Claude Code reads the same rules.

It covers my workflow (research → references → frame-by-frame breakdown of the vibe → prototype → handoff → phased build → my audit → production), how agents hand work to each other through the repo when one hits a usage limit, a script-based audit in every phase, and design rules that keep the output from looking AI-generated.

To use it, copy both files into the root of a project and add that project's own `handoff.md`.
