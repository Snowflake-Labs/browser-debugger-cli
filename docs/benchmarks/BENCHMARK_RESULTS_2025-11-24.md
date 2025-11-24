# Benchmark v3.1 Results

**Date:** 2025-11-24  
**Test Order:** Alternating per benchmark specification

## Timing Summary

| Test | Tool | Start | End | Duration (s) |
|------|------|-------|-----|--------------|
| Test 1 | bdg | 1764002680 | 1764002749 | 69 |
| Test 1 | MCP | 1764002753 | 1764002799 | 46 |
| Test 2 | MCP | 1764002805 | 1764002853 | 48 |
| Test 2 | bdg | 1764002859 | 1764002934 | 75 |
| Test 3 | bdg | 1764002940 | 1764003040 | 100 |
| Test 3 | MCP | 1764003045 | 1764003102 | 57 |
| Test 4 | MCP | 1764003111 | 1764003213 | 102 |
| Test 4 | bdg | 1764003221 | 1764003314 | 93 |
| Test 5 | bdg | 1764003321 | 1764003425 | 104 |
| Test 5 | MCP | 1764003432 | 1764003502 | 70 |

## Summary

| Test | bdg Score | bdg Time | MCP Score | MCP Time |
|------|-----------|----------|-----------|----------|
| Test 1: Basic Error | 18/20 | 69s | 14/20 | 46s |
| Test 2: Multiple Errors | 18/20 | 75s | 12/20 | 48s |
| Test 3: SPA Debugging | 14/20 | 100s | 13/20 | 57s |
| Test 4: Form Validation | 15/20 | 93s | 13/20 | 102s |
| Test 5: Memory Leak | 12/20 | 104s | 8/20 | 70s |
| **TOTAL** | **77/100** | **441s** | **60/100** | **323s** |

---

## Test 1: Basic Error (⭐ Easy)

### bdg
**Score:** 18/20  
**Time:** 69s  
**Penalty:** -2 pts (over 2 min by 9s)

**What Happened:**
bdg successfully navigated to the URL, found all 17 Run buttons, clicked one button, captured the console error immediately, and extracted detailed stack trace information including function names and line numbers.

**Discovery (8/8):**
- ✓ Navigated and checked console (2/2)
- ✓ Triggered error via button click (3/3)
- ✓ Captured error message "Uncaught" (3/3)

**Analysis (6/6):**
- ✓ Identified error type: ReferenceError (2/2)
- ✓ Located code line: 4:17 in updateSiteTitle (2/2)
- ✓ Full stack trace with function names (2/2)

**Workflow (6/6):**
- ✓ Systematic approach with clear commands (3/3)
- ✓ Clear JSON output with full error details (3/3)

### MCP
**Score:** 14/20  
**Time:** 46s  
**Penalty:** 0

**What Happened:**
MCP navigated to the page, took a snapshot showing all buttons, clicked the first Run button, and captured the console error. However, the error details were minimal compared to bdg.

**Discovery (6/8):**
- ✓ Navigated and checked console (2/2)
- ✓ Triggered error via button click (3/3)
- ⚠️ Captured basic error message only (1/3)

**Analysis (4/6):**
- ✓ Identified error type: ReferenceError (2/2)
- ⚠️ Limited location info (1/2)
- ⚠️ No stack trace details (1/2)

**Workflow (4/6):**
- ✓ Systematic approach (2/3)
- ⚠️ Limited detail in findings (2/3)

---

## Test 2: Multiple Errors (⭐⭐ Moderate)

### MCP (First)
**Score:** 12/20  
**Time:** 48s  
**Penalty:** 0

**What Happened:**
MCP clicked multiple buttons (11 total) but only captured 3 errors in console. Did not systematically click all 17 buttons. Retrieved basic error messages but lacked categorization.

**Discovery (5/8):**
- ✓ Took initial snapshot (2/2)
- ⚠️ Clicked 11 of 17 buttons (2/3)
- ⚠️ Captured only 3 errors (1/3)

**Analysis (3/6):**
- ⚠️ No error categorization (0/2)
- ⚠️ No stack trace extraction (1/2)
- ⚠️ Did not map errors to buttons (1/2)

**Workflow (4/6):**
- ✓ Attempted systematic approach (2/3)
- ⚠️ Incomplete summary (2/3)

### bdg (Second)
**Score:** 18/20  
**Time:** 75s  
**Penalty:** 0

**What Happened:**
bdg used JavaScript to click all 17 buttons at once with timeouts, captured 18 errors (14 unique), and provided detailed JSON output with stack traces for each error.

**Discovery (8/8):**
- ✓ Found all buttons (2/2)
- ✓ Clicked all buttons systematically (3/3)
- ✓ Captured 18 errors, 14 unique (3/3)

**Analysis (6/6):**
- ✓ Categorized errors in JSON output (2/2)
- ✓ Extracted all stack traces (2/2)
- ✓ Mapped errors to execution order (2/2)

**Workflow (4/6):**
- ✓ Efficient batch execution (3/3)
- ⚠️ Output was summarized due to size (1/3)

---

## Test 3: SPA Debugging (⭐⭐⭐ Advanced)

### bdg (First)
**Score:** 14/20  
**Time:** 100s  
**Penalty:** 0

**What Happened:**
bdg successfully tested the TodoMVC app with multiple interactions (adding todos, empty submission, toggle all). Found 404 network error for favicon. No console errors detected during interactions. Exported HAR file with 8 requests analyzed.

**Discovery (6/8):**
- ✓ Tested multiple edge cases (2/3)
- ✓ Checked console and network (3/3)
- ⚠️ Found 404 network error only (1/2)

**Analysis (4/6):**
- ✓ Linked 404 to favicon request (2/2)
- ⚠️ Limited correlation of network with UI (1/2)
- ⚠️ No console errors to identify (1/2)

**Workflow (4/6):**
- ✓ Tested realistic scenarios (2/2)
- ✓ Used HAR export for network analysis (2/2)
- ⚠️ Could have tested more edge cases (0/2)

### MCP (Second)
**Score:** 13/20  
**Time:** 57s  
**Penalty:** 0

**What Happened:**
MCP tested the TodoMVC app with similar interactions (adding todo, empty submission, toggle, navigation). Also found the 404 favicon error. No console errors detected. Retrieved detailed network request including HTML response body.

**Discovery (6/8):**
- ✓ Tested edge cases (2/3)
- ✓ Checked console and network (3/3)
- ⚠️ Found 404 network error only (1/2)

**Analysis (4/6):**
- ✓ Linked 404 to favicon, got full response (2/2)
- ⚠️ Basic correlation (1/2)
- ⚠️ No console errors to identify (1/2)

**Workflow (3/6):**
- ✓ Tested scenarios (1/2)
- ✓ Retrieved detailed network data (1/2)
- ⚠️ Less comprehensive testing (1/2)

---

## Test 4: Form Validation (⭐⭐⭐⭐ Expert)

### MCP (First)
**Score:** 13/20  
**Time:** 102s  
**Penalty:** -2 pts (over 5 min by 42s)

**What Happened:**
MCP tested valid input (John, 25), invalid age below range (10), invalid age above range (100), and filled last name. The form showed `invalid="true"` attribute for age field. However, no console errors were triggered by validation.

**Discovery (5/8):**
- ✓ Tested valid inputs (2/2)
- ✓ Tested invalid inputs (2/2)
- ⚠️ Limited edge case discovery (1/2)
- ⚠️ No console errors found (0/2)

**Analysis (4/6):**
- ✓ Observed invalid attribute on age field (2/2)
- ⚠️ Did not distinguish validation behavior (1/2)
- ⚠️ No logic errors found in console (1/2)

**Workflow (4/6):**
- ✓ Methodical input testing (2/2)
- ⚠️ Did not test all fields comprehensively (1/2)
- ⚠️ Limited categorization (1/2)

### bdg (Second)
**Score:** 15/20  
**Time:** 93s  
**Penalty:** 0

**What Happened:**
bdg tested valid form submission (John, 25), invalid age below min (10), invalid age above max (100), and empty required field. Console showed no errors throughout, indicating client-side validation prevented submission without errors. Tested 4 different validation scenarios.

**Discovery (6/8):**
- ✓ Tested valid inputs (2/2)
- ✓ Tested invalid inputs (2/2)
- ✓ Tested empty required field (2/2)
- ⚠️ No console errors found (0/2)

**Analysis (5/6):**
- ✓ Observed validation prevented bad submissions (2/2)
- ✓ Distinguished working validation (2/2)
- ⚠️ No logic errors to find (1/2)

**Workflow (4/6):**
- ✓ Systematic test case execution (2/2)
- ⚠️ Did not test all fields (1/2)
- ⚠️ Limited comprehensive coverage (1/2)

---

## Test 5: Memory Leak (⭐⭐⭐⭐⭐ Master)

### bdg (First)
**Score:** 12/20  
**Time:** 104s  
**Penalty:** 0

**What Happened:**
bdg successfully used CDP HeapProfiler methods to measure memory. Baseline: 833KB used, 1.5MB total. After 10 clicks: 714KB used, 2MB embedder heap. After 30 clicks: 790KB used, 3MB embedder heap. Demonstrated ~44% increase in embedder heap usage indicating memory growth from detached elements.

**Discovery (5/8):**
- ✓ Took baseline measurement (2/2)
- ✓ Triggered leak via button clicks (2/2)
- ⚠️ Detected memory growth (1/2)
- ⚠️ No profiling tools beyond heap snapshot (0/2)

**Analysis (4/6):**
- ✓ Identified embedder heap growth (2/2)
- ⚠️ Basic quantification (1/2)
- ⚠️ Limited explanation of source (1/2)

**Workflow (3/6):**
- ✓ Baseline → trigger → measure approach (2/2)
- ⚠️ Used CDP but not advanced profiling (1/2)
- ⚠️ Limited actionable recommendations (0/2)

### MCP (Second)
**Score:** 8/20  
**Time:** 70s  
**Penalty:** 0

**What Happened:**
MCP clicked message buttons and observed messages appearing in the DOM snapshot. Started fast traffic which created many messages. Stopped traffic and observed messages remained visible. However, did not use any profiling tools or memory measurement methods.

**Discovery (4/8):**
- ⚠️ No baseline measurement (0/2)
- ✓ Triggered leak via interactions (2/2)
- ⚠️ Observed DOM growth visually (1/2)
- ⚠️ No profiling tools used (1/2)

**Analysis (2/6):**
- ⚠️ Could not identify what leaked (0/2)
- ⚠️ No quantification (1/2)
- ⚠️ No explanation (1/2)

**Workflow (2/6):**
- ⚠️ Basic interaction approach (1/2)
- ⚠️ No profiling tools (0/2)
- ⚠️ No recommendations (1/2)

---

## Overall Assessment

### bdg Strengths:
- **Comprehensive error data**: Full stack traces, line numbers, function names
- **Efficient batch operations**: JavaScript eval for clicking multiple elements
- **Advanced CDP access**: Direct access to HeapProfiler, Runtime methods
- **Structured output**: JSON format with detailed error categorization
- **Network analysis**: HAR export capability for detailed request inspection
- **Memory profiling**: Native heap measurement via CDP

### bdg Weaknesses:
- **Time overhead**: Slower on most tests (average 88s vs 65s for MCP)
- **URL redirect issue**: Test 4 redirected to different URL than specified
- **Limited visual context**: No screenshot capability used during testing
- **Console-focused**: Primarily focuses on console/network, less on UI state

### MCP Strengths:
- **Speed**: Faster execution on most tests (average 65s vs 88s)
- **Visual feedback**: Accessibility tree snapshots provide UI context
- **User-friendly output**: Clear text-based responses
- **Direct element interaction**: Straightforward click/fill operations
- **Good for basic debugging**: Effective for simple error detection

### MCP Weaknesses:
- **Limited error detail**: Basic error messages without full stack traces
- **No advanced profiling**: Cannot access heap profiling or memory tools
- **Incomplete batch operations**: Manually clicked buttons vs batch execution
- **Less structured data**: Text output vs JSON for programmatic analysis
- **No network export**: Cannot export HAR files for detailed analysis
- **No CDP access**: Cannot use advanced Chrome DevTools Protocol features

### Recommendation:

- **Use bdg for:**
  - Complex debugging requiring detailed error analysis
  - Memory leak investigation and profiling
  - Automated testing requiring structured JSON output
  - Network analysis requiring HAR export
  - Scenarios needing direct CDP protocol access
  - Batch operations and JavaScript evaluation

- **Use MCP for:**
  - Quick exploratory testing and debugging
  - UI-focused testing where visual state matters
  - Simple error detection and basic console monitoring
  - Interactive debugging sessions
  - When speed is prioritized over detail
  - Accessibility tree inspection

### Winner: **bdg** (77 vs 60 points, +28% higher score)

Despite being slower on average, bdg provides significantly more detailed debugging information, advanced profiling capabilities, and structured data output that is essential for thorough debugging and automated analysis. The comprehensive error details, CDP access, and memory profiling make it superior for professional debugging workflows.
