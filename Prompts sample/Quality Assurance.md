### 6. Quality Assurance Prompts

When code changes introduce **unintended breakage** rather than planned enhancements, systematic review prompts help identify truncated files, missing functionality, and structural breaks before they reach production.

**Example Prompt for Quality Assurance Review:**

"You are a Quality Assurance code reviewer. Your task is to systematically review commits after a specific baseline to identify BROKEN functionality (not intended enhancements).

**COMPARISON BASELINE:**
- Production commit: [INSERT COMMIT HASH/BRANCH NAME]  
- Review commits: [INSERT COMMIT RANGE or "last N commits"]

**Check for these critical issues:**

1. **TRUNCATION DETECTION** - Flag any file with deletions >50 lines in source files (src/, components/, app/). Flag files that lost >30% of their size. Ignore documentation, generated files, locale translations.

2. **DELETION IMPACT ANALYSIS** - For each file with substantial deletions: Is it configuration/critical setup? → HIGH PRIORITY. Is it a core component or service? → MEDIUM PRIORITY.

3. **STRUCTURAL BREAKS** - Check if imports reference non-existent files. Check if removed exports are still imported elsewhere.

**Run these git commands:**
```
git diff --stat [baseline] HEAD | findstr /R "[0-9][0-9][0-9]* -"
git diff --numstat [baseline] HEAD  
git diff [baseline] HEAD <filename>
```

**For each flagged file, provide a "Verify and Fix" prompt:**

Format: "Verify this issue exists and fix it: [Clear description of what was deleted/broken and why it's problematic]. [Specific impact on functionality]. [What should be restored/fixed]. @[filename] ([line numbers if known])"

Example: "Verify this issue exists and fix it: The authentication middleware configuration was completely removed from next.config.js, which will break all protected routes. This causes users to bypass login requirements and access restricted pages. The middleware configuration should be restored to enforce authentication on /dashboard/* and /admin/* routes. @next.config.js (lines 15-28)"

**Save all findings to `code_review_findings.md` with:**
- Commit hashes for traceability
- Each issue as a separate "Verify and fix" prompt
- Priority order (CRITICAL issues first)

Priority order: Configuration files → Core services → UI components → New features."

**Example Prompt 2 for Quick QA Review:**

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

**Example Output Format:**

```
## Code Review Findings - Commit abc123f

### CRITICAL Issues:
Verify this issue exists and fix it: The entire CSS reset and global styles were removed from globals.css, causing complete UI breakdown across the application. This makes all components lose their styling and layout structure. The global styles, CSS variables, and Tailwind imports should be restored. @globals.css (lines 1-45)

Verify this issue exists and fix it: Database connection configuration was deleted from next.config.js, breaking all API routes that depend on database access. This causes 500 errors on /api/* endpoints. The database URL and connection pool settings should be restored. @next.config.js (lines 8-15)

### MEDIUM Issues:
Verify this issue exists and fix it: The UserProfile component lost its validation logic and error handling, potentially allowing invalid data submission. This could cause form submission failures and poor UX. The validation schema and error boundary should be restored. @components/UserProfile.tsx (lines 45-78)
```

**Strategic Purpose:**
- Generate immediately actionable "Verify and fix" prompts for the next agent
- Catch accidental file truncations and deletions before production
- Distinguish between intentional refactoring and unintended breakage
- Enable seamless handoff with context-rich problem descriptions

**Usage Notes:**
- Run after major refactoring sessions or bulk file operations
- Particularly useful when multiple developers work on the same codebase
- Essential for brownfield projects where context about "working state" may be unclear
- Can be automated as part of CI/CD pipeline for high-risk changes

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