# Browser Debugger CLI

[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](https://github.com/szymdzum/browser-debugger-cli/pulls)
[![CI](https://github.com/szymdzum/browser-debugger-cli/actions/workflows/ci.yml/badge.svg)](https://github.com/szymdzum/browser-debugger-cli/actions/workflows/ci.yml)
[![Security](https://github.com/szymdzum/browser-debugger-cli/actions/workflows/security.yml/badge.svg)](https://github.com/szymdzum/browser-debugger-cli/actions/workflows/security.yml)
[![npm downloads](https://img.shields.io/npm/dt/browser-debugger-cli?color=blue)](https://www.npmjs.com/package/browser-debugger-cli)

Chrome DevTools Protocol in your terminal. Self-documenting CDP access with discovery, search, and introspection. **Designed for AI agents** and developers who want direct browser control without framework overhead.

## Why bdg?

| | bdg | Puppeteer/Playwright | Chrome DevTools MCP |
|---|-----|---------------------|---------------------|
| **CDP coverage** | ✅ All 300+ methods | ⚠️ Via CDPSession API | ⚠️ 28 curated tools |
| **Self-documenting** | ✅ Built-in introspection | ❌ External docs | ❌ Fixed descriptions |
| **Discovery** | ✅ Search methods by keyword | ❌ Know what you need | ❌ Browse tool list |
| **Interface** | ✅ CLI, immediate | ❌ Write code first | ❌ MCP server + client |
| **Token efficient** | ✅ Semantic a11y (70-99% reduction) | ❌ Raw HTML | ❌ Verbose responses |
| **Unix philosophy** | ✅ Pipes, jq, composable | ❌ Programmatic only | ❌ Protocol-based |

**When to use alternatives:**
- **Puppeteer/Playwright**: Complex multi-step scripts, mature testing ecosystem
- **Chrome DevTools MCP**: Already invested in MCP infrastructure

**Built for agents:** Self-discovery (`--list`, `--search`), semantic exit codes, structured errors, case-insensitive commands, token-efficient output.

## Install

```bash
npm install -g browser-debugger-cli@alpha
```

**Platform Support:**
- ✅ macOS and Linux
- ✅ Windows via WSL
- ❌ PowerShell/Git Bash (not yet)

## Quick Start

```bash
bdg example.com                    # Start session
bdg cdp --search cookie            # Discover commands
bdg cdp Network.getCookies         # Run any CDP method
bdg dom query "button"             # High-level helpers
bdg stop                           # End session
```

## Current State

**Raw CDP access is complete.** All 300+ protocol methods work now. High-level wrappers (`bdg dom`, `bdg network`) are being added for common operations. See [Commands](https://github.com/szymdzum/browser-debugger-cli/wiki/Commands) for full reference.

## Agent Discovery Pattern

```bash
# Agent explores what's possible (no docs needed)
bdg cdp --list                              # 53 domains
bdg cdp Network --list                      # 39 methods
bdg cdp Network.getCookies --describe       # Full schema + examples
bdg cdp Network.getCookies                  # Execute

# Search across all domains
bdg cdp --search screenshot                 # Find relevant methods
bdg cdp --search cookie                     # 14 results
```

## Documentation

📖 **[Wiki](https://github.com/szymdzum/browser-debugger-cli/wiki)** - Guides, command reference, recipes

- [Getting Started](https://github.com/szymdzum/browser-debugger-cli/wiki/Getting-Started)
- [Commands](https://github.com/szymdzum/browser-debugger-cli/wiki/Commands)
- [For AI Agents](https://github.com/szymdzum/browser-debugger-cli/wiki/For-AI-Agents)
- [Recipes](https://github.com/szymdzum/browser-debugger-cli/wiki/Recipes)
- [Troubleshooting](https://github.com/szymdzum/browser-debugger-cli/wiki/Troubleshooting)

## Design Principles

This tool implements [Agent-Friendly Tools](docs/principles/AGENT_FRIENDLY_TOOLS.md):

- **Self-documenting** - Tools teach themselves via `--list`, `--describe`
- **Semantic exit codes** - Machine-parseable error handling
- **Structured output** - JSON by default, human-readable optional
- **Progressive disclosure** - Simple commands, deep capabilities

## Contributing

[Issues](https://github.com/szymdzum/browser-debugger-cli/issues) for bugs, [Discussions](https://github.com/szymdzum/browser-debugger-cli/discussions) for ideas. PRs welcome.

See `docs/` for architecture and contributor guides.

## License

MIT
