### 6. Quality Assurance Prompts

When code changes introduce **unintended breakage** rather than planned enhancements, systematic review prompts help identify truncated files, missing functionality, and structural breaks before they reach production.

---

**WHICH PROMPT SHOULD I USE? Decision Tree:**

```
START: Do you need to review code changes?
│
├─→ YES: Who made the changes?
│   │
│   ├─→ I made them (I'm the developer)
│   │   └─→ Use PROMPT 1 - PART 1 (Session Documentation)
│   │       Save to code_review_findings.md and hand off
│   │
│   └─→ Someone else made them (I'm the reviewer)
│       │
│       ├─→ Single session/commit?
│       │   └─→ Use PROMPT 1 - PART 2 (Critical Breakage Detection)
│       │       Read Part 1 first, then review
│       │
│       └─→ Multiple sessions/commits?
│           └─→ Use PROMPT 2B (Multi-Session Review)
│               Review entire commit range as one unit
│
├─→ Need a quick self-check? (< 5 files, no formal review)
│   └─→ Use PROMPT 2 (Quick Single-Agent Review)
│       Fast pass before committing
│
└─→ Verifying fixes after review?
    └─→ Use PROMPT 3 (Post-Fix Verification)
        Confirm issues resolved, no new problems
```

**Quick Reference:**
- **PROMPT 1A**: Developer documents their changes
- **PROMPT 1B**: Reviewer audits changes for breakage
- **PROMPT 2**: Quick self-review (small changes)
- **PROMPT 2B**: Multi-session cumulative review
- **PROMPT 3**: Post-fix verification

---

**REVIEW WORKFLOW:**

This prompt produces two parts for different agents:

**PART 1: Session Documentation (Original Developer)**
- Use this when you're the agent who made the changes
- Document context, patterns, and reasoning
- Focus on "what we did and why"

**PART 2: Critical Breakage Detection (Fresh Reviewer)**
- Use this when you're a different agent reviewing the changes
- Review Part 1 for completeness, then focus on breakage detection
- Use git commands to independently verify
- Approach with fresh context window (don't assume Part 1 is complete)

**HANDOFF:**
Original developer completes Part 1 → Save to `code_review_findings.md`
Fresh reviewer reads Part 1 → Completes Part 2 → Validates both parts

**QUICK START:**
-Developer: Jump to PART 1 → Document your changes → Save and exit
-Reviewer: 
1. Check if `code_review_findings.md` exists
2. If yes: Read Part 1 → Jump to PART 2
3. If no: Jump to PART 2 (document missing Part 1 as issue #1)

---

**Example Prompt 1A - PART 1: Session Documentation (For Original Developer)**

Use this when: You made the changes and need to document them before handing off

"You are documenting your code changes for quality assurance review.

**CONTEXT:**
- Review changes made after commit: [INSERT COMMIT HASH/BRANCH NAME]
- Review staged changes (if any)
- Focus on source code files (src/, components/, app/)
- Ignore: docs, generated files, locale translations

**YOUR TASK: SESSION DOCUMENTATION**

For each change made during this session, document:

1. **File path** and line count changes (+X/-Y)
2. **Why the change was made** (problem solved)
3. **Risk assessment** (Low/Medium/High)
4. **Pattern or approach used** (if applicable)

**Output format:**

```
## Session Summary - [Date/Time]
Baseline commit: [commit hash]

### Changes Made:

| File | Lines Changed | Type of Change | Risk | Reasoning |
|------|---------------|----------------|------|-----------|
| src/auth.ts | +45/-12 | Refactor | Medium | Replaced deprecated auth library with new implementation |
| components/Header.tsx | +8/-3 | Enhancement | Low | Added user avatar display |

### Patterns Used:
- Serialization pattern: Database → DTO → API Response
- Error handling: Try-catch with custom error types

### Testing Recommendations:
- Test authentication flow end-to-end
- Verify user avatar displays correctly on all screen sizes

### Verification Steps for Reviewer:
1. Check that all auth imports are updated
2. Verify no deprecated library references remain
3. Test login/logout functionality
```

**Save to `code_review_findings.md` and hand off to reviewer."






**Example Prompt 1B - PART 2: Critical Breakage Detection (For Reviewer)**

Use this when: You're reviewing someone else's changes for unintended breakage

"You are a Quality Assurance reviewer checking for unintended breakage in code changes.

**CONTEXT:**
- Review changes made after commit: [INSERT COMMIT HASH/BRANCH NAME]
- First, check if `code_review_findings.md` exists and read Part 1 (Session Documentation)
- Focus on source code files (src/, components/, app/)
- Ignore: docs, generated files, locale translations

**YOUR TASK: CRITICAL BREAKAGE DETECTION**

**FIRST TIME REVIEWING? Start here:**
1. Read the entire Part 1 from `code_review_findings.md`
2. Run the git commands to see actual changes
3. Compare what Part 1 says vs what git shows
4. Flag any discrepancies
5. Then proceed with systematic breakage detection

**Instructions for Reviewer:**
1. Read Part 1 summary first (understand intent)
2. Then independently verify using git commands
3. Question Part 1 if you find discrepancies
4. Flag any issues Part 1 missed
5. Validate that "intended changes" don't break dependencies

**Part 1 Validation Checklist:**
Before proceeding with breakage detection, verify Part 1 includes:
- [ ] All modified files listed
- [ ] Line counts for each change
- [ ] Risk assessment for each change
- [ ] Patterns documented (if applicable)
- [ ] Testing recommendations provided
- [ ] Verification steps listed

If Part 1 is incomplete, document what's missing before proceeding.

**Run these git commands:**
```
git diff --stat [baseline] HEAD | findstr /R "[0-9][0-9][0-9]* -"
git diff --numstat [baseline] HEAD  
git diff [baseline] HEAD <filename>
```

**Check for these critical issues:**

1. **TRUNCATION DETECTION**
   - Flag files with deletions >50 lines in source files
   - Flag files that lost >30% of their size
   - Provide line count before/after for deleted sections

2. **DELETION IMPACT ANALYSIS**
   - Configuration/critical setup → HIGH PRIORITY
   - Core component or service → MEDIUM PRIORITY
   - UI/non-critical → LOW PRIORITY
   - Distinguish: Intentional refactoring vs. Unintended breakage

3. **STRUCTURAL BREAKS**
   - Verify all imports reference existing files
   - Check if removed exports are still imported elsewhere
   - Validate all TypeScript types/interfaces are still valid

**For each flagged issue, provide a "Verify and Fix" prompt:**

Format: "Verify this issue exists and fix it: [Clear description]. [Specific impact]. [What should be restored/fixed]. @[filename] ([line numbers])"

Example: "Verify this issue exists and fix it: The authentication middleware configuration was completely removed from next.config.js, which will break all protected routes. This causes users to bypass login requirements and access restricted pages. The middleware configuration should be restored to enforce authentication on /dashboard/* and /admin/* routes. @next.config.js (lines 15-28)"

**Output format for Part 2:**

```
## Critical Review Findings

### Part 1 Summary (from original developer):
[Brief 2-3 sentence summary of what Part 1 documented]

### Part 1 Validation:
✓ Part 1 summary is complete and accurate
OR
✗ Part 1 missing/incomplete: [describe what's missing]

### Files Reviewed:
- src/auth.ts (45 additions, 12 deletions)
- components/Header.tsx (8 additions, 3 deletions)
- [List all files checked with git commands]

### CRITICAL Issues:
Verify this issue exists and fix it: [issue description]

### MEDIUM Issues:
Verify this issue exists and fix it: [issue description]

### LOW Issues:
Verify this issue exists and fix it: [issue description]

### Discrepancies with Part 1:
- Part 1 claims "Low risk" for auth.ts changes, but removed error handling makes this Medium risk
- Part 1 didn't mention deletion of migration file
```

**CONFLICT RESOLUTION:**
- If Part 2 finds issues Part 1 didn't mention → Flag as "Missed by original developer"
- If Part 1 says "intentional" but Part 2 sees breakage → Flag as "Needs verification"
- If unclear whether deletion is intentional → Mark as "REVIEW REQUIRED" and ask original developer

**Append findings to `code_review_findings.md` with:**
- Commit hashes for traceability
- Session summary (Part 1)
- Critical issues (Part 2) in priority order
- Timestamp and baseline commit reference

Priority order: Configuration files → Core services → UI components → New features."






**Example Prompt 2 for Quick Single-Agent QA Review - Use this when: You're doing a quick check yourself (no separate reviewer), reviewing a small change (<5 files), Need a fast pass before committing**

"Review commits after [baseline] for breakage, not enhancements.

Check for:
- Files with >50 deletions (likely truncation)
- Files with >30% size reduction  
- CSS/config files with deletions → CRITICAL
- Core components/services with deletions → MEDIUM

Priority: config files, CSS, services over features.

Output each issue as: "Verify this issue exists and fix it: [problem description]. [impact]. [solution]. @[filename] ([lines])"

Ignore: docs, ideas, locale files, generated files.

Save findings to `code_review_findings.md` with commit hashes for traceability."






**Example Prompt 2B for Multi-Session/Multi-Agent Review -Use this when: Changes span multiple sessions/commits, Multiple agents contributed to these changes, Solution evolved iteratively (not a single session, Need to review cumulative impact across sessions)**

"Review commits after [baseline] through [current commit] for breakage across multiple development sessions.

**Context:**
- Changes span multiple sessions/commits
- Multiple agents may have contributed
- Solution evolved iteratively
- Focus on detecting breakage, not documenting intent

**Approach:**
1. Review the entire commit range as one unit
2. Use git commands to see cumulative changes
3. Flag any breakage regardless of when it was introduced
4. Treat the entire range as 'one change set'

**Run these git commands:**
```
git diff --stat [baseline] [current] | findstr /R "[0-9][0-9][0-9]* -"
git diff --numstat [baseline] [current]
git log --oneline [baseline]..[current]
```

**Session History Summary:**
List each commit with brief description:
- Session 1 (commit abc123): [what was attempted]
- Session 2 (commit def456): [what was modified]
- Total: X sessions, Y agents, Z files changed

**Cross-Session Pattern Detection:**
- Files modified in multiple sessions → Flag as high risk
- Same file fixed repeatedly → Investigate root cause
- Config changes then reverted → Document why
- Logic touched by multiple agents → Verify consistency

**Evolution Analysis:**
✓ Feature completed successfully across sessions
⚠ Multiple attempts suggest unclear requirements
✗ Repeated fixes to same file indicate deeper issue

**Output format:**
- Group issues by file, not by commit
- Don't attribute issues to specific agents/sessions
- Focus on 'what's broken' not 'who broke it'
- Provide fix recommendations without blame

For each issue: 'Verify this issue exists and fix it: [problem]. [impact]. [solution]. @[filename] ([lines])'

Priority: Configuration files → Core services → UI components → New features

Save findings to `code_review_findings.md` with full commit range for traceability."






**Example Prompt 3 for Post-Fix Verification:**

"You are verifying that issues found in code review have been properly resolved.

**Review the fixes for these issues:**
[Paste the "Verify and fix" prompts that were addressed]

**For each issue, confirm:**
1. The fix was applied correctly
2. No new issues were introduced
3. Related functionality still works
4. Tests pass (if applicable)

**Run verification commands:**
```
git diff [before-fix] [after-fix] <filename>
git log --oneline -n 5
```

**Output format:**
```
## Fix Verification Results

✓ Issue #1: RESOLVED - Auth middleware restored, all routes protected
✗ Issue #2: PARTIALLY RESOLVED - CSS restored but missing dark mode variables
⚠ Issue #3: NEW ISSUE FOUND - Fix introduced TypeScript error in Header.tsx

### New Issues Introduced by Fixes:
Verify this issue exists and fix it: [new issue description]
```

**If all issues resolved:** Confirm with "All critical issues have been resolved. Code is ready for production."

**If new issues found:** Generate new "Verify and fix" prompts for the next iteration."

---

**Complete QA Workflow:**

```
Session 1 (Developer Agent):
  ↓ Makes code changes
  ↓ Runs Part 1: Session Documentation
  ↓ Saves to code_review_findings.md
  
Session 2 (Reviewer Agent):
  ↓ Reads Part 1 from code_review_findings.md
  ↓ Runs Part 2: Critical Breakage Detection
  ↓ Appends findings to code_review_findings.md
  ↓ Generates "Verify and fix" prompts
  
Session 3 (Fix Agent):
  ↓ Reads "Verify and fix" prompts
  ↓ Implements fixes
  ↓ Runs Part 3: Post-Fix Verification
  ↓ Updates code_review_findings.md
  
Session 4 (Final Verification):
  ↓ Confirms all issues resolved
  ↓ Marks as production-ready
```

**Strategic Purpose:**
- **Two-phase review**: Self-documentation + Independent audit catches both intent and reality
- **Context preservation**: Part 1 captures reasoning that would otherwise be lost
- **Trust but verify**: Part 2 questions Part 1, preventing confirmation bias
- **Iterative fixing**: Part 3 ensures fixes don't introduce new issues
- **Pattern documentation**: Captures reusable approaches for future sessions
- **Seamless handoff**: Each phase produces actionable input for the next

**Usage Notes:**
- **Part 1**: Run at end of coding session before context window fills up
- **Part 2**: Run in fresh session with new context window for objectivity
- **Part 3**: Run after implementing fixes to verify no regressions
- Particularly useful when multiple agents work on the same codebase
- Essential for brownfield projects where context about "working state" may be unclear
- Can be automated as part of CI/CD pipeline for high-risk changes
- The two-phase approach prevents "I know what I meant to do" bias

**Success Metrics:**
- Zero false positives on intentional deletions
- 100% detection rate for files with >50 line deletions
- Clear actionable recommendations for each flagged file
- Complete traceability with commit hashes and timestamps

**Failure Recovery:**
- If too many false positives: Refine ignore patterns and size thresholds
- If missing real issues: Lower deletion thresholds or expand file type coverage
- If unclear recommendations: Add more context about file purpose and dependencies
- If handoff fails: Include screenshots of git diff output and specific restoration commands

---

**Review Complete When:**

**Part 1 Complete:**
- [ ] All changes documented
- [ ] Risk levels assigned
- [ ] Patterns captured
- [ ] Testing recommendations provided
- [ ] Saved to `code_review_findings.md`

**Part 2 Complete:**
- [ ] Part 1 validated
- [ ] All files checked with git
- [ ] Critical issues identified
- [ ] "Verify and fix" prompts generated
- [ ] Findings appended to `code_review_findings.md`

**Ready for Production:**
- [ ] All critical issues resolved
- [ ] Part 3 verification passed
- [ ] No new issues introduced
- [ ] Final approval given