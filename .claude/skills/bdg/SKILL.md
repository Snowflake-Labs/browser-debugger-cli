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
bdg stop                   # Stop and save output
bdg cleanup --force        # Kill stale session
bdg cleanup --aggressive   # Kill all Chrome processes
```

## Screenshots

Always use `bdg dom screenshot` (raw CDP is blocked):

```bash
bdg dom screenshot /tmp/page.png                    # Full page
bdg dom screenshot /tmp/viewport.png --no-full-page # Viewport only
bdg dom screenshot /tmp/el.png --selector "#main"   # Element only
bdg dom screenshot /tmp/scroll.png --scroll "#target" # Scroll to element first
```

## Form Interaction

```bash
# Discover forms
bdg dom form --brief              # Quick scan: field names, types, required

# Fill and interact
bdg dom fill "input[name='user']" "myuser"    # Fill by selector
bdg dom fill 0 "value"                         # Fill by index (from query)
bdg dom click "button.submit"                  # Click element
bdg dom submit "form" --wait-navigation        # Submit and wait for page load
bdg dom pressKey "input" Enter                 # Press Enter key

# Options
--no-wait          # Skip network stability wait
--wait-navigation  # Wait for page navigation (traditional forms)
--wait-network <ms> # Wait for network idle (SPA forms)
--index <n>        # Select nth element when multiple match
```

## DOM Inspection

```bash
bdg dom query "selector"     # Find elements, returns [0], [1], [2]...
bdg dom get "selector"       # Get semantic a11y info (token-efficient)
bdg dom get "selector" --raw # Get full HTML
bdg dom eval "js expression" # Run JavaScript
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
bdg dom submit "button[type='submit']" --wait-navigation
bdg dom screenshot /tmp/result.png
bdg stop
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

## Verification Best Practices

**Prefer DOM queries over screenshots** for verification:

```bash
# GOOD: Fast, precise, scriptable
bdg cdp Runtime.evaluate --params '{
  "expression": "document.querySelector(\".error-message\")?.textContent",
  "returnByValue": true
}'

# GOOD: Check element exists
bdg dom query ".submit-btn"

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
