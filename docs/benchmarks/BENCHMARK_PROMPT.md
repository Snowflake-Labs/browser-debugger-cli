# Browser Automation Tool Benchmark Prompt
**Version:** 1.0
**Purpose:** Standardized evaluation protocol for comparing browser automation tools from an AI agent perspective

---

## Overview

This benchmark evaluates browser automation tools across **5 real-world scenarios** on live websites. It measures:
1. **Command/tool efficiency** - How many operations to complete a task
2. **Token efficiency** - Output verbosity and context window impact
3. **Discovery patterns** - How agents find and interact with elements
4. **Error handling** - Recovery suggestions and actionable feedback
5. **Composability** - Integration with other tools and workflows

---

## Prerequisites

Before running this benchmark:
- [ ] Tool is installed and configured
- [ ] Chrome/Chromium browser is available
- [ ] Non-headless mode is used (some sites block headless)
- [ ] Network access to test websites
- [ ] Ability to measure token counts

---

## Test Suite

Execute all 5 tests in order. Record:
- Commands/tool calls required
- Token counts (input + output)
- Success/failure status
- Time to complete
- Blockers encountered

---

## Test 1: Hacker News Comment Thread
**Difficulty:** Easy
**URL:** https://news.ycombinator.com
**Goal:** Extract comment structure and analyze voting behavior

### Tasks
1. **Start session** - Navigate to Hacker News front page
2. **Count stories** - How many story items are on the page?
3. **Get accessibility info** - Describe the first story title for screen readers
4. **Find vote buttons** - Count all upvote/downvote buttons
5. **Extract points** - Get the score of the first story
6. **Navigate to comments** - Click the comments link for the first story
7. **Monitor network** - What requests were made during navigation?
8. **Count comments** - How many comments are on the page?
9. **Get comment structure** - Extract the raw HTML of the first comment
10. **Check console** - Are there any JavaScript errors?
11. **Export network data** - Save all requests as HAR file
12. **Stop session** - Clean up and terminate

### Expected Results
- 30 stories on front page
- 29-30 vote buttons
- 90-120 comments (varies by time)
- Network shows navigation to `/item?id=...`
- HAR file with 10-15 requests

### Scoring Criteria
- **Commands:** Fewer is better (target: <12)
- **Tokens:** Lower output is better (target: <5,000)
- **Discovery:** Can agent find elements without docs? (CSS selectors vs UIDs)
- **Errors:** Does tool suggest fixes when selectors fail?

---

## Test 2: CodePen Trending
**Difficulty:** Easy
**URL:** https://codepen.io/trending
**Goal:** Inspect embedded pens and extract code samples

### Tasks
1. **Navigate** - Load CodePen trending page
2. **Count pens** - How many pen preview cards are visible?
3. **Get pen metadata** - Extract title and author of first pen
4. **Count iframes** - How many embedded preview iframes?
5. **Click pen** - Open the first pen in detail view
6. **Find code editors** - Are CodeMirror editors present?
7. **Check scripts** - What external scripts are loaded? (CDN URLs)
8. **Screenshot** - Capture full page screenshot
9. **Stop session**

### Expected Results
- 5-10 pen cards
- Equal number of iframes
- CodeMirror script from `cpwebassets.codepen.io`
- Screenshot ~300-500KB

### Scoring Criteria
- **Commands:** Target <8
- **Tokens:** Target <3,000
- **Screenshot:** Can agent save to disk?
- **Iframe detection:** Can agent count embedded content?

---

## Test 3: Amazon Product Page (Anti-Bot Challenge)
**Difficulty:** Hard
**URL:** https://www.amazon.com/Amazon-Basics-Non-Stick-Temperature-Stainless/dp/B0F7988YRR/?th=1
**Goal:** Test resilience against anti-bot measures

### Tasks
1. **Navigate** - Load Amazon product page
2. **Check for bot detection** - Is content visible or blocked?
3. **Extract product title** - If accessible, get the main heading
4. **Get price** - Extract current price
5. **Find "Add to Cart" button** - Locate via accessibility tree
6. **Stop session**

### Expected Results
- Likely blocked by Cloudflare/bot detection
- Page returns challenge or empty content

### Scoring Criteria
- **Resilience:** Does tool provide useful error message?
- **Detection:** Can agent identify anti-bot page vs normal page?
- **Suggestions:** Does tool recommend headless-evasion techniques?

---

## Test 4: MDN Web Docs
**Difficulty:** Medium
**URL:** https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array
**Goal:** Extract documentation structure and code samples

### Tasks
1. **Navigate** - Load MDN Array reference page
2. **Get page title** - Extract via JavaScript evaluation
3. **Find code blocks** - Count all `<pre>` or code example elements
4. **Extract first example** - Get text content of first code sample
5. **Query headings** - List all section headings (Methods, Properties, etc.)
6. **Check navigation** - Get accessibility tree for sidebar navigation
7. **Click method link** - Navigate to a method (e.g., `filter()`)
8. **Detect navigation type** - Same page (hash) or new page load?
9. **Check for service workers** - Are service workers registered?
10. **Get stylesheets** - List all CSS file URLs
11. **Stop session**

### Expected Results
- 20-40 code examples
- Section headings: "Constructor", "Static methods", "Instance methods"
- Navigation is typically in-page (hash change)
- 5-10 stylesheet links

### Scoring Criteria
- **Commands:** Target <10
- **Tokens:** Target <4,000
- **Code extraction:** Can agent parse `<pre>` vs `<code>` differences?
- **Navigation detection:** Can agent distinguish same-page vs new-page?

---

## Test 5: Reddit Thread
**Difficulty:** Hard
**URL:** https://www.reddit.com/r/programming/top/?t=week
**Goal:** Analyze post structure and comment interactions

### Tasks
1. **Navigate** - Load r/programming top posts (week)
2. **Check for CAPTCHA** - Is "Prove your humanity" challenge shown?
3. **Count posts** - If accessible, how many post cards visible?
4. **Get post metadata** - Extract score (karma) of first post
5. **Find vote buttons** - Count upvote/downvote buttons (may be in Shadow DOM)
6. **Click first post** - Navigate to comments
7. **Count comments** - How many comment elements?
8. **Analyze nesting** - What's the maximum comment depth/nesting level?
9. **Get accessibility tree** - Query buttons by role (reply, share, etc.)
10. **Monitor network** - What API calls were made? (e.g., `/api/...`)
11. **Check cookies** - How many cookies set? Any tracking?
12. **Stop session**

### Expected Results
- May be blocked by CAPTCHA (depends on IP/session)
- 20-30 posts if accessible
- 50-200 comments (varies)
- Max nesting: 2-4 levels
- 10-20 cookies

### Scoring Criteria
- **Commands:** Target <10
- **Tokens:** Target <5,000
- **Shadow DOM:** Can agent access elements inside Shadow DOM?
- **Dynamic content:** Can agent handle lazy-loaded comments?
- **API detection:** Can agent correlate UI actions with network calls?

---

## Scoring Rubric

### 1. Command Efficiency (0-25 points)
- **20-25 pts:** Completes all tasks in <40 total commands
- **15-19 pts:** 40-60 commands
- **10-14 pts:** 60-80 commands
- **0-9 pts:** >80 commands

### 2. Token Efficiency (0-25 points)
- **20-25 pts:** Total output <15,000 tokens
- **15-19 pts:** 15,000-30,000 tokens
- **10-14 pts:** 30,000-60,000 tokens
- **0-9 pts:** >60,000 tokens

### 3. Discovery & Usability (0-25 points)
- **20-25 pts:** Intuitive element selection (CSS selectors or semantic queries)
- **15-19 pts:** Requires some non-standard patterns (UIDs from snapshots)
- **10-14 pts:** Verbose discovery process (multi-step lookups)
- **0-9 pts:** Brittle or unclear element selection

### 4. Error Handling (0-15 points)
- **12-15 pts:** Actionable error messages with recovery suggestions
- **8-11 pts:** Clear errors but minimal guidance
- **4-7 pts:** Generic errors
- **0-3 pts:** Cryptic or misleading errors

### 5. Composability (0-10 points)
- **8-10 pts:** Native Unix pipes, JSON output, easy integration
- **5-7 pts:** Structured output, requires parsing
- **2-4 pts:** Mixed output formats
- **0-1 pts:** Difficult to integrate with other tools

### Total Score: /100

---

## Benchmark Execution Template

Fill out this template after running all tests:

```markdown
## Tool: [Tool Name]
**Version:** [x.y.z]
**Date:** [YYYY-MM-DD]
**Tester:** [Your Name/Agent ID]

### Test Results

| Test | Commands | Tokens (Out) | Success | Time | Blockers |
|------|----------|--------------|---------|------|----------|
| HN Comments | | | ☐ ☑ | | |
| CodePen | | | ☐ ☑ | | |
| Amazon | | | ☐ ☑ | | |
| MDN Docs | | | ☐ ☑ | | |
| Reddit | | | ☐ ☑ | | |

### Scoring

- Command Efficiency: __/25
- Token Efficiency: __/25
- Discovery & Usability: __/25
- Error Handling: __/15
- Composability: __/10

**Total: __/100**

### Key Observations

#### Strengths
1.
2.
3.

#### Weaknesses
1.
2.
3.

#### Unique Capabilities
-

#### Recommended Use Cases
-
```

---

## Comparison Example

Based on actual testing (2025-11-23):

| Metric | bdg | Chrome MCP |
|--------|-----|------------|
| **Avg Commands** | 8 | 4 |
| **Avg Tokens** | 2,700 | 42,500 |
| **Discovery** | CSS selectors | Accessibility UIDs |
| **Error Handling** | Actionable suggestions | Basic confirmations |
| **Composability** | Unix pipes ✓ | Limited |
| **Network Monitoring** | Built-in | Separate tool call |
| **Score** | 86/100 | 79/100 |

---

## Agent-Specific Considerations

When an AI agent runs this benchmark:

### Discovery Pattern Recognition
- **CSS Selectors:** Does agent know `.class`, `#id`, `[attribute]` syntax?
- **Accessibility:** Can agent parse a11y tree with 1000+ nodes?
- **XPath:** Is XPath supported as fallback?

### Token Awareness
- Does agent measure token counts from tool outputs?
- Can agent optimize by requesting specific data (e.g., "first 10 results")?
- Does tool provide summary vs full output options?

### Error Recovery
- If selector fails, does agent:
  - Retry with adjusted selector?
  - Request page snapshot to discover elements?
  - Abandon task?
- Does tool guide agent toward successful patterns?

### Workflow Adaptation
- Can agent build multi-step workflows (pipes, loops)?
- Does tool support conditional logic (if/else based on results)?
- Can agent save intermediate state?

---

## Continuous Benchmarking

This benchmark should be re-run:
- When tool versions update
- When test websites undergo major redesigns
- Quarterly to track tool evolution
- When comparing new tools to established baselines

### Version History
- **v1.0** (2025-11-23): Initial benchmark based on bdg vs Chrome MCP comparison

---

## Appendix: Sample Command Sequences

### Example: bdg on Hacker News
```bash
bdg https://news.ycombinator.com --timeout 300
bdg dom query ".athing"
bdg dom query ".votelinks a"
bdg dom eval "document.querySelector('.score')?.textContent"
bdg dom click ".athing:first-child ~ tr .subtext a:last-child"
bdg peek --last 10
bdg dom query ".comment"
bdg console --list
bdg network har /tmp/hn.har
bdg stop
```

### Example: MCP on Hacker News
```javascript
new_page({ url: "https://news.ycombinator.com" })
take_snapshot({})
evaluate_script({ function: "() => document.querySelectorAll('.athing').length" })
click({ uid: "2_46" })  // From snapshot UID
evaluate_script({ function: "() => document.querySelectorAll('.comment').length" })
```

---

## License & Attribution

This benchmark prompt is open-source and can be adapted for any browser automation tool evaluation. If you publish results using this benchmark, please reference:

```
Browser Automation Tool Benchmark v1.0
https://github.com/szymdzum/browser-debugger-cli/docs/benchmarks/
Based on bdg vs Chrome MCP comparison (2025-11-23)
```
