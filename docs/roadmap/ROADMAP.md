# bdg Feature Roadmap

**Last Updated:** 2025-11-25  
**Status:** Living Document

This document consolidates planned features, enhancements, and strategic directions for bdg. Features are organized by priority tier and estimated effort.

---

## Current State (v0.6.9)

bdg provides comprehensive CDP access with these core capabilities:

- **100% CDP Protocol Coverage** - 53 domains, 300+ methods with type-safe wrappers
- **Daemon Architecture** - Persistent CDP connection via Unix sockets for fast CLI commands
- **Telemetry Collection** - Network requests, console messages, DOM snapshots, accessibility tree
- **Form Automation** - React/Vue/Angular compatible fills, clicks, key presses
- **Agent-Friendly Design** - Semantic exit codes, JSON output, token-efficient formats
- **DevTools-Compatible Filtering** - 10 filter types, 8 presets for network data
- **HAR 1.2 Export** - Full HAR export with WebSocket extensions
- **Console Inspection** - Rich object expansion, level filtering, deduplication

---

## Tier 1: Quick Wins (1-2 weeks each)

High-value features with low implementation risk.

### 1.1 Element Staleness Detection

**Problem:** nodeIds become invalid after navigation with no detection mechanism.

**Solution:**
```bash
bdg dom validate <index>
# Returns: { valid: true } or { valid: false, reason: "removed|navigated" }
```

**Implementation:**
- Add `NodeRegistry` class tracking nodeId validity via CDP events
- Subscribe to `DOM.childNodeRemoved`, `DOM.documentUpdated`
- Expose validation command for agents to check before operations

**Files:** `src/cache/NodeRegistry.ts`, `src/commands/dom/validate.ts`

**References:** [AGENT_FORM_AUTOMATION.md](./AGENT_FORM_AUTOMATION.md)

---

### 1.2 DOM Snapshot Diffing

**Problem:** Hard to detect what changed after an action.

**Solution:**
```bash
bdg dom snapshot save before
# ... perform action ...
bdg dom snapshot compare before
# Output: "+5 .product-card nodes, -1 .loading-spinner"
```

**Implementation:**
- Store DOM snapshots as JSON with node paths
- Diff algorithm comparing node trees
- Report additions, removals, attribute changes

**Files:** `src/commands/dom/snapshot.ts`, `src/utils/domDiff.ts`

---

### 1.3 Style Inspection

**Problem:** No CLI access to computed or matched CSS styles.

**Solution:**
```bash
bdg dom styles <selector>              # Computed styles as JSON object
bdg dom matched-rules <selector>       # CSS cascade with specificity
bdg dom matched-rules <selector> --raw # Raw CDP response
```

**Implementation:**
- Wrap `CSS.getComputedStyleForNode` and `CSS.getMatchedStylesForNode`
- Optional parsing via @devtoolcss/parser for friendly output
- Support index-based access like other DOM commands

**Files:** `src/commands/dom/styles.ts`, `src/telemetry/styles.ts`

**References:** [CHROME_INSPECTOR_EVALUATION.md](./CHROME_INSPECTOR_EVALUATION.md)

---

### 1.4 Pseudo-State Forcing

**Problem:** Can't capture hover/focus states for screenshots.

**Solution:**
```bash
bdg dom pseudo <selector> --states hover,focus
bdg dom pseudo <selector> --clear
```

**Implementation:**
- Wrap `CSS.forcePseudoState` CDP method
- Support: hover, active, focus, focus-within, focus-visible, visited
- State persists until cleared

**Files:** `src/commands/dom/pseudo.ts`

---

### 1.5 Event Listener Inspection

**Problem:** No visibility into what events elements handle.

**Solution:**
```bash
bdg dom listeners <selector>
# Output: { "click": 2, "submit": 1, "input": 5 }

bdg dom listeners <selector> --verbose
# Output: Full listener details with handler info
```

**Implementation:**
- Use `DOMDebugger.getEventListeners` CDP method
- Resolve node via `DOM.resolveNode` to get objectId
- Group and count by event type

**Files:** `src/commands/dom/listeners.ts`

---

## Tier 2: Medium Effort (3-4 weeks each)

Significant features requiring more architecture work.

### 2.1 Network Action Correlation

**Problem:** Can't tell which action triggered which network request.

**Solution:**
```bash
bdg trace-action start
bdg dom click "button.submit"
bdg trace-action stop
# Output:
#   Action: click on button.submit
#   Triggered:
#     - POST /api/submit (200 OK, 234ms)
#     - GET /success (302 → /dashboard)
```

**Implementation:**
- Track action context with timestamps
- Correlate network requests started within action window
- Store correlation in telemetry data

**Files:** `src/telemetry/actionCorrelation.ts`, `src/commands/trace.ts`

---

### 2.2 Form Autofill Analysis

**Problem:** Agents must manually identify form fields.

**Solution:**
```bash
bdg dom analyze-form <selector>
# Output:
# {
#   "contact": [
#     { "field": "name", "autocomplete": "name", "nodeId": 42 },
#     { "field": "email", "autocomplete": "email", "nodeId": 43 }
#   ],
#   "address": [...],
#   "payment": [...]
# }

bdg dom autofill contact --data contact.json
```

**Implementation:**
- Detect form groups by `autocomplete` attribute
- Identify common field patterns (name, email, phone, address, card)
- Provide one-step form population

**Files:** `src/commands/dom/analyzeForm.ts`, `src/commands/dom/autofill.ts`

**References:** [AGENT_FORM_AUTOMATION.md](./AGENT_FORM_AUTOMATION.md)

---

### 2.3 Optional DOM Cache Mode

**Problem:** Every query goes to CDP (chatty, slow for heavy automation).

**Solution:**
```bash
bdg localhost:3000 --cache
# Enables in-memory DOM mirror for fast local queries
```

**Implementation:**
- JSDOM-based DOM mirror in worker process
- Event-driven sync via `DOM.*` mutation events
- Fallback to live CDP on sync errors
- `tracked` validation before operations

**Trade-offs:**
- Pro: Drastically reduces CDP chatter, instant selector queries
- Con: Memory usage on large documents, sync complexity

**Files:** `src/cache/DOMCache.ts`, `src/daemon/worker.ts` modifications

**References:** [CHROME_INSPECTOR_EVALUATION.md](./CHROME_INSPECTOR_EVALUATION.md)

---

### 2.4 Issue Detection (DevTools Audits)

**Problem:** No automatic detection of common web problems.

**Solution:**
```bash
bdg diagnose
# Output:
# Security Issues (2)
#   - Mixed content on https://example.com/page
#   - Cookie without Secure flag: session_id
#
# Compatibility Warnings (1)
#   - Deprecated API: document.write()
#
# Performance Issues (1)
#   - Render-blocking resource: /styles/main.css

bdg diagnose --json         # Machine-readable for agents
bdg diagnose --category security,performance
```

**Implementation:**
- Enable `Audits.enable` CDP domain
- Subscribe to `Audits.issueAdded` events
- Categorize: security, compatibility, performance, accessibility, network
- Format actionable recommendations

**Files:** `src/telemetry/issues.ts`, `src/commands/diagnose.ts`

**References:** [DEVTOOLS_FRONTEND_EVALUATION.md](./DEVTOOLS_FRONTEND_EVALUATION.md)

---

### 2.5 Persistent Element Handles

**Problem:** Must re-query elements after any operation.

**Solution:**
```bash
bdg dom query "button.submit" --json
# Returns: { "handle": "h_abc123", "nodeId": 42, "valid": true }

bdg dom click --handle h_abc123
# Uses handle directly, validates before operation
```

**Implementation:**
- Return structured handles from query commands
- Store handle → nodeId mapping with validity tracking
- Detect staleness via DOM events
- Clear handles on navigation

**Files:** `src/cache/HandleRegistry.ts`, command modifications

---

## Tier 3: Strategic Features (2-3 months)

Major features requiring significant investment.

### 3.1 Performance Trace Analysis

**Problem:** No performance profiling capability.

**Solution:**
```bash
bdg perf trace --duration 10s trace.json
bdg perf vitals trace.json
# Output:
#   LCP: 2.3s (good)
#   CLS: 0.15 (needs improvement)
#   INP: 450ms (poor)
#   FCP: 1.2s (good)
#   TTFB: 380ms (good)

bdg perf bottlenecks trace.json
# Output:
#   Long Tasks:
#     - script.js:142 (245ms)
#     - vendor.js:8901 (180ms)
#   Layout Shifts:
#     - img.banner (0.08)
```

**Implementation:**
- Enable `Tracing.start/end` CDP methods
- Parse trace events for key metrics
- Extract Core Web Vitals (LCP, CLS, INP, FCP, TTFB)
- Identify bottlenecks (long tasks, layout shifts)

**Files:** `src/telemetry/performance.ts`, `src/commands/perf.ts`

**References:** [DEVTOOLS_FRONTEND_EVALUATION.md](./DEVTOOLS_FRONTEND_EVALUATION.md)

---

### 3.2 Source Map Integration

**Problem:** Minified stack traces are unreadable.

**Solution:**
```bash
bdg trace-error "TypeError at bundle.js:1:23456"
# Output:
#   Original location: src/utils/api.ts:42:15
#   Function: fetchUserData
#   Context:
#     40|   const response = await fetch(url);
#     41|   if (!response.ok) {
#   > 42|     throw new TypeError(`Request failed: ${response.status}`);
#     43|   }
```

**Implementation:**
- Fetch source maps from `//# sourceMappingURL=...`
- Map compiled → original locations
- Resolve console errors to source code
- Cache source maps per session

**Files:** `src/utils/sourceMap.ts`, `src/commands/traceError.ts`

---

### 3.3 Shadow DOM & iFrame Support

**Problem:** Limited to main document only.

**Solution:**
```bash
bdg dom query "my-component::shadow button"
# Queries inside shadow root

bdg frames list
# Output: [{ id: "main", url: "..." }, { id: "frame_1", url: "..." }]

bdg frames switch frame_1
bdg dom query "input"  # Now queries within iframe
```

**Implementation:**
- Per-frame DOM mirrors (if cache enabled)
- Shadow root traversal via `DOM.describeNode` with pierce option
- Frame context switching for commands

**Trade-offs:**
- Increases complexity significantly
- Shadow DOM support requires different CDP approach

**Files:** `src/session/frames.ts`, extensive command modifications

---

### 3.4 Multi-Tab Management

**Problem:** Single tab per session.

**Solution:**
```bash
bdg tabs list
# Output:
#   [0] https://example.com (active)
#   [1] https://docs.example.com

bdg tabs new "https://api.example.com/docs"
bdg tabs switch 1
bdg tabs close 0
```

**Implementation:**
- Track multiple targets via `Target.getTargets`
- Switch active target context
- Maintain separate telemetry per tab

**Files:** `src/session/tabs.ts`, `src/commands/tabs.ts`

---

### 3.5 Screenshot & Recording

**Problem:** No visual capture capability.

**Solution:**
```bash
bdg screenshot output.png
bdg screenshot --selector ".hero" hero.png
bdg screenshot --full-page full.png

bdg record start recording.webm
# ... interactions ...
bdg record stop
```

**Implementation:**
- `Page.captureScreenshot` for screenshots
- Screencast API or Tracing for video
- Clip regions for element screenshots

**Files:** `src/commands/screenshot.ts`, `src/commands/record.ts`

---

## Tier 4: Future Vision

Long-term possibilities beyond current scope.

### 4.1 Browser Extension

**Concept:** Chrome extension for bidirectional DevTools sync.

**Features:**
- Sync DevTools-selected element ($0) to bdg
- Visual overlay showing bdg query results
- Real-time console bridge

**Status:** Requires significant investigation.

---

### 4.2 Web Dashboard

**Concept:** Web UI for remote session management.

**Features:**
- Session status and telemetry visualization
- Interactive DOM exploration
- Network waterfall charts

**Status:** Out of scope for CLI tool, but interesting for enterprise use.

---

### 4.3 Programmatic API / SDK

**Concept:** Node.js library for programmatic use.

```typescript
import { BdgClient } from 'bdg';

const client = await BdgClient.connect('localhost:3000');
await client.dom.fill('input[name="email"]', 'test@example.com');
await client.dom.click('button[type="submit"]');
const har = await client.network.exportHar();
await client.stop();
```

**Status:** Architecture supports this; needs API design.

---

## Missing Automation Capabilities

Lower-priority but useful gaps to fill.

### File Input Support

```bash
bdg dom upload "input[type=file]" "/path/to/file.pdf"
```

Uses `DOM.setFileInputFiles` CDP method. Currently not supported.

---

### Drag-and-Drop Simulation

```bash
bdg dom drag ".draggable" ".dropzone"
```

Uses `Input.dispatchDragEvent` CDP methods. Complex interaction simulation.

---

### Dropdown/Select Interaction

```bash
bdg dom select "select#country" "US"
bdg dom select "select#country" --index 3
```

Currently requires workaround via `bdg dom eval`.

---

## Implementation Principles

All new features should follow these principles:

1. **CommandRunner Pattern** - Use `runCommand()` for consistent error handling
2. **Centralized Messages** - All user-facing strings in `src/ui/messages/`
3. **Semantic Exit Codes** - Use appropriate codes from `src/utils/exitCodes.ts`
4. **TSDoc Coverage** - Document all public APIs
5. **JSON Output** - Support `--json` flag for machine-readable output
6. **Agent-Friendly** - Consider token efficiency and semantic meaning
7. **No Dead Code** - Remove unused code immediately

---

## Related Documents

- [AGENT_FORM_AUTOMATION.md](./AGENT_FORM_AUTOMATION.md) - Form automation strategies and patterns
- [CHROME_INSPECTOR_EVALUATION.md](./CHROME_INSPECTOR_EVALUATION.md) - DOM mirroring integration analysis
- [DEVTOOLS_FRONTEND_EVALUATION.md](./DEVTOOLS_FRONTEND_EVALUATION.md) - Feature extraction from DevTools

---

## Contributing

Feature requests and implementation proposals are welcome. When proposing new features:

1. Open an issue describing the use case
2. Reference this roadmap for context
3. Consider agent workflows (how would an LLM use this?)
4. Propose CLI interface before implementation details

---

## Changelog

- **2025-11-25** - Initial roadmap consolidation
