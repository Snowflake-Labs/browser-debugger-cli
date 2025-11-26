# Reddit Post Template

**Target subreddits:** r/webdev, r/javascript, r/node, r/LocalLLaMA, r/ClaudeAI

---

## Title

CLI tool for AI agents to control Chrome - benchmarked 33% more token-efficient than MCP

## Body

Hey 🖖, I built a CLI tool that connects directly to Chrome DevTools Protocol, explicitly designed for CLI agents that can use `bash_tool`. Just hit alpha.

**The problem:** Getting browser context into CLI agents means screenshots, copy-paste from DevTools, Puppeteer scripts, or MCP servers. I wanted something simpler—a Unix-style CLI that agents can call.

**What it does:** Opens a persistent WebSocket to CDP. Run `bdg example.com`, interact with your page, query live data with `bdg peek`, stop when done.

**Raw access to all** [**644 CDP methods**](https://chromedevtools.github.io/devtools-protocol/) — not constrained by what a protocol wrapper decides to expose. Memory profiling, network interception, DOM manipulation, performance tracing—if Chrome DevTools can do it, `bdg cdp <method>` can do it.

**Plus high-level helpers** for everyday tasks: `bdg dom click`, `bdg dom fill`, `bdg dom query` for automation. `bdg console` streams errors in real-time. `bdg peek` shows live network/console activity. Smart page-load detection built in. Raw power when you need it, convenience when you don't.

**I benchmarked it against Chrome DevTools MCP Server** on real debugging tasks:

[Full benchmark](https://github.com/szymdzum/browser-debugger-cli/blob/main/docs/benchmarks/ARTICLE_MCP_VS_CLI_FOR_AGENTS.md)

**Why CLI wins for agents:**

* **Unix philosophy** — composable by design. Output pipes to `jq`, chains with other tools. No protocol overhead.
* **Self-correcting** — errors are clearly exposed with semantic exit codes. The agent sees what failed and why, and adjusts automatically.
* **43x cheaper** on complex pages (1,200 vs 52,000 tokens for the Amazon product page). Selective queries vs full accessibility tree dumps.
* **Trainable via skills** — define project-specific workflows using [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills). Agent learns your patterns once and reuses them everywhere.

**Agent-friendly by design:**

* Self-discovery (`bdg cdp --search cookie` finds 14 methods)
* Semantic exit codes for error handling
* JSON output, structured errors

Repo: [https://github.com/szymdzum/browser-debugger-cli](https://github.com/szymdzum/browser-debugger-cli)

Tested on macOS/Linux. Windows via WSL works, native Windows not yet.

Early alpha—validating the approach. Feedback welcome!

---

## Short Version

**Title:** Built a CLI for AI agents to talk to Chrome - 33% more efficient than MCP

**Body:**

Made a thing. CLI tool that connects to Chrome DevTools Protocol so AI agents can control browsers without the MCP overhead.

```bash
bdg example.com          # start session
bdg dom click "button"   # interact
bdg console              # see errors
bdg stop                 # done
```

Benchmarked it against Chrome DevTools MCP: scored 77 vs 60, uses 43x fewer tokens on complex pages.

Why? Direct access to all 644 CDP methods. Unix-style output (pipes to jq). Errors are exposed so agents self-correct. You can train it on your workflow with [skills](https://docs.anthropic.com/en/docs/claude-code/skills).

Repo: https://github.com/szymdzum/browser-debugger-cli

Alpha stage, works on mac/linux. Would love feedback.

---

## Version History

- **2025-11-26 v3**: Added high-level helpers (click, fill, query, console, peek, page-load detection)
- **2025-11-26 v2**: Added raw CDP access (644 methods), Unix philosophy, self-correction, skills training
- **2025-11-26 v1**: Added benchmark data, structured benefits, confident tone
- **2025-11-XX**: Initial version (generic intro without data)
