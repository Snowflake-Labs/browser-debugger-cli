# Reddit Post Template

**Target subreddits:** r/webdev, r/javascript, r/node, r/LocalLLaMA, r/ClaudeAI

---

## Title

CLI tool for AI agents to control Chrome - benchmarked 33% more token-efficient than MCP

## Body

I built a CLI tool that connects directly to Chrome DevTools Protocol, designed specifically for AI agents. Just hit alpha.

**The problem:** Getting browser context into CLI agents means screenshots, copy-paste from DevTools, Puppeteer scripts, or MCP servers. I wanted something simpler—a Unix-style CLI that agents can just call.

**What it does:** Opens a persistent WebSocket to CDP. Run `bdg example.com`, interact with your page, query live data with `bdg peek`, stop when done. All 644 CDP methods available via `bdg cdp <method>`.

**I benchmarked it against Chrome DevTools MCP Server** on real debugging tasks:

| | bdg (CLI) | MCP |
|--|-----------|-----|
| Score | 77/100 | 60/100 |
| Token Efficiency | 202 | 152 |

**Why CLI won:**
- **Selective queries** — ask for what you need vs full accessibility tree dumps
- **43x less tokens** on complex pages (1,200 vs 52,000 for Amazon product page)
- **Capabilities MCP lacks** — memory profiling, HAR export, batch JS execution

Full benchmark: https://github.com/szymdzum/browser-debugger-cli/blob/main/docs/benchmarks/ARTICLE_MCP_VS_CLI_FOR_AGENTS.md

**Agent-friendly by design:**
- Self-discovery (`bdg cdp --search cookie` finds 14 methods)
- Semantic exit codes for error handling
- JSON output pipes to jq naturally

Repo: https://github.com/szymdzum/browser-debugger-cli

Tested on macOS/Linux. Windows via WSL works, native Windows not yet.

Early alpha—validating the approach. Feedback welcome!

---

## Version History

- **2025-11-26**: Added benchmark data, structured benefits, confident tone
- **2025-11-XX**: Initial version (generic intro without data)
