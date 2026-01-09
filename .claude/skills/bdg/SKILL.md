---
name: bdg
description: Use bdg CLI for browser automation via Chrome DevTools Protocol. Provides direct CDP access (60+ domains, 300+ methods) for DOM queries, navigation, screenshots, network control, and JavaScript execution. Use this skill when you need to automate browsers, scrape dynamic content, or interact with web pages programmatically.
---

# bdg - Browser Automation CLI

## Quick Start

```bash
bdg https://example.com          # Start session (launches Chrome)
bdg dom screenshot /tmp/page.png # Take screenshot
bdg stop                         # End session
```

## Session Management

```bash
bdg <url>                  # Start session (1920x1080, headless if no display)
bdg <url> --headless       # Force headless mode
bdg <url> --no-headless    # Force visible browser window
bdg status                 # Check session status
bdg peek                   # Preview collected telemetry
bdg stop                   # End session (use sparingly)
bdg cleanup --force        # Kill stale session
bdg cleanup --aggressive   # Kill all Chrome processes
```

**Sessions run indefinitely by default** (no timeout). With HMR/hot-reload dev servers, keep the session running:

```bash
bdg http://localhost:5173      # Start once
# ... make code changes, HMR updates the page ...
bdg dom screenshot /tmp/s.png  # Check anytime
bdg peek                       # Preview collected data
# No need to stop/restart - Chrome stays on the page
```

**Don't stop sessions prematurely** - use `bdg peek` to inspect data. Only call `bdg stop` when completely done with browser automation.

## Screenshots

Always use `bdg dom screenshot` (raw CDP is blocked):

```bash
bdg dom screenshot /tmp/page.png                    # Full page
bdg dom screenshot /tmp/viewport.png --no-full-page # Viewport only
bdg dom screenshot /tmp/el.png --selector "#main"   # Element only
bdg dom screenshot /tmp/scroll.png --scroll "#target" # Scroll to element first
```

## Playwright Selectors

bdg supports Playwright-style selectors for precise element targeting:

```bash
# Text-based selectors (most useful for AI agents)
bdg dom click 'button:has-text("Submit")'     # Contains text (case-insensitive)
bdg dom click ':text("Login")'                 # Smallest element with exact text
bdg dom fill 'input:text-is("Email")'          # Exact text match (case-sensitive)
bdg dom click 'button:visible'                 # Only visible elements

# Combined selectors
bdg dom click 'button.primary:has-text("Save")'  # CSS + text
bdg dom query 'div.modal:visible'                # CSS + visibility
```

**Why Playwright selectors?** Instead of a 2-step workflow:
```bash
# OLD: Query then use index (requires cache, can go stale)
bdg dom query "button"     # → [0] Save, [1] Cancel
bdg dom click --index 0    # Hope it's still the Save button

# NEW: One precise selector (no cache, always accurate)
bdg dom click 'button:text("Save")'
```

## Form Interaction

```bash
# Discover forms
bdg dom form --brief              # Quick scan: field names, types, required

# Fill and interact using selectors
bdg dom fill "input[name='user']" "myuser"           # By attribute
bdg dom fill 'input:has-text("Username")' "myuser"   # By label text
bdg dom click "button.submit"                         # Click by class
bdg dom click 'button:text("Submit")'                 # Click by text
bdg dom submit "form" --wait-navigation               # Submit and wait

# When multiple elements match, use --index
bdg dom query "button"                    # Shows [0], [1], [2]...
bdg dom click "button" --index 0          # Click first match
bdg dom click "button" --index 1          # Click second match

# Key press
bdg dom pressKey "input" Enter            # Press Enter key
bdg dom pressKey "input" Tab --times 3    # Press Tab 3 times

# Options
--no-wait          # Skip network stability wait
--wait-navigation  # Wait for page navigation (traditional forms)
--wait-network <ms> # Wait for network idle (SPA forms)
--index <n>        # Select nth element when multiple match (0-based)
```

## DOM Inspection

```bash
bdg dom query "selector"     # Find elements matching selector
bdg dom get "selector"       # Get semantic a11y info (token-efficient)
bdg dom get "selector" --raw # Get full HTML
bdg dom eval "js expression" # Run JavaScript (works with DOM elements)
```

**DOM eval now handles DOM elements:**
```bash
# Returns element description instead of failing
bdg dom eval "document.querySelector('button')"
# → "button.btn.btn-primary"

# Still works for primitives and objects
bdg dom eval "document.title"
# → "My Page Title"
```

## CDP Access

Direct access to Chrome DevTools Protocol:

```bash
# Execute any CDP method
bdg cdp Runtime.evaluate --params '{"expression": "document.title", "returnByValue": true}'
bdg cdp Page.navigate --params '{"url": "https://example.com"}'
bdg cdp Page.reload --params '{"ignoreCache": true}'

# Discovery
bdg cdp --list                    # List all 53 domains
bdg cdp Network --list            # List methods in domain
bdg cdp Network.getCookies --describe  # Show method schema
bdg cdp --search cookie           # Search methods
```

**Important**: Always use `returnByValue: true` for Runtime.evaluate to get serialized values.

## Common Patterns

### Login Flow
```bash
bdg https://example.com/login
bdg dom form --brief
bdg dom fill "input[name='username']" "$USER"
bdg dom fill "input[name='password']" "$PASS"
bdg dom click 'button:text("Log in")' --wait-navigation
bdg dom screenshot /tmp/result.png
bdg stop
```

### Click Button by Text
```bash
# Preferred: Use Playwright selector
bdg dom click 'button:text("Submit")'

# Alternative: Use --index if needed
bdg dom query "button"            # Find all buttons
bdg dom click "button" --index 2  # Click third button
```

### Wait for Element
```bash
for i in {1..20}; do
  EXISTS=$(bdg cdp Runtime.evaluate --params '{
    "expression": "document.querySelector(\"#target\") !== null",
    "returnByValue": true
  }' | jq -r '.result.value')
  [ "$EXISTS" = "true" ] && break
  sleep 0.5
done
```

### Extract Data
```bash
bdg cdp Runtime.evaluate --params '{
  "expression": "Array.from(document.querySelectorAll(\"a\")).map(a => ({text: a.textContent, href: a.href}))",
  "returnByValue": true
}' | jq '.result.value'
```

## Exit Codes

| Code | Meaning | Action |
|------|---------|--------|
| 0 | Success | - |
| 1 | Blocked command | Read error message, use suggested alternative |
| 81 | Invalid arguments | Check command syntax |
| 83 | Resource not found | Element/session doesn't exist |
| 101 | CDP connection failure | Run `bdg cleanup --aggressive` and retry |
| 102 | CDP timeout | Increase timeout or check page load |

## Troubleshooting

```bash
bdg status --verbose      # Full diagnostics
bdg cleanup --force       # Kill stale session
bdg cleanup --aggressive  # Kill all Chrome processes
```

**Chrome won't launch?** Run `bdg cleanup --aggressive` then retry.

**Session stuck?** Run `bdg cleanup --force` to reset.

### Custom Chrome Flags

Use `--chrome-flags` or `BDG_CHROME_FLAGS` for self-signed certificates, CORS, etc.:

```bash
# CLI option
bdg https://localhost:5173 --chrome-flags="--ignore-certificate-errors"

# Environment variable
BDG_CHROME_FLAGS="--ignore-certificate-errors" bdg https://localhost:5173

# Multiple flags
bdg https://example.com --chrome-flags="--ignore-certificate-errors --disable-web-security"
```

**Common flags for development:**
- `--ignore-certificate-errors` - Self-signed SSL certs
- `--disable-web-security` - CORS issues in development
- `--allow-insecure-localhost` - Insecure localhost
- `--disable-features=IsolateOrigins,site-per-process` - Cross-origin iframes

## Verification Best Practices

**Prefer DOM queries over screenshots** for verification:

```bash
# GOOD: Fast, precise, scriptable
bdg cdp Runtime.evaluate --params '{
  "expression": "document.querySelector(\".error-message\")?.textContent",
  "returnByValue": true
}'

# GOOD: Check element exists with text
bdg dom query 'div:has-text("Success")'

# GOOD: Check text content
bdg cdp Runtime.evaluate --params '{
  "expression": "document.body.innerText.includes(\"Success\")",
  "returnByValue": true
}'

# AVOID: Screenshots for simple verification (slow, requires visual inspection)
bdg dom screenshot /tmp/check.png  # Only use when you need visual proof
```

**When to use screenshots:**
- Visual regression testing
- Capturing proof for user review
- Debugging layout issues
- When DOM structure is unknown

**When to use DOM queries:**
- Verifying text content appeared
- Checking element exists/visible
- Validating form state
- Counting elements
- Any programmatic assertion

## When NOT to Use bdg

- **Static HTML** - Use `curl` + `htmlq`/`pq`
- **API calls** - Use `curl` + `jq`
- **Simple HTTP** - Use `wget`/`curl`

Use bdg when you need: JavaScript execution, dynamic content, browser APIs, screenshots, or network manipulation.
