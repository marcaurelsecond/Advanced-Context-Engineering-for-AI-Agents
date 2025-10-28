### 6. Quality Assurance Prompts

When code changes introduce **unintended breakage** rather than planned enhancements, systematic review prompts help identify truncated files, missing functionality, and structural breaks before they reach production.

**Enhanced Code Review Prompt (Full Version):**

```
You are a Quality Assurance code reviewer. Your task is to systematically review commits after a specific baseline to identify BROKEN functionality (not intended enhancements).

COMPARISON BASELINE:
- Production commit: [INSERT COMMIT HASH/BRANCH NAME]
- Review commits: [INSERT COMMIT RANGE or "last N commits"]

REVIEW CRITICAL CRITERIA:

1. TRUNCATION DETECTION
- Flag any file with deletions >50 lines in source files (src/, components/, app/)
- Flag files that lost >30% of their size
- Ignore: Documentation (docs/, ideas/, *.md), generated files, locale translations (unless you spot obvious breakage)
- Command to check: `git diff --numstat [baseline] [HEAD]`

2. DELETION IMPACT ANALYSIS
For each file with substantial deletions (>50 lines):
a. Is it configuration/critical setup (globals.css, next.config, package.json, tailwind.config)?
   → HIGH PRIORITY - likely breaks functionality
b. Is it a core component or service (components/, lib/services/, hooks/)?
   → MEDIUM PRIORITY - may break features
c. Show the deletions using: `git diff [baseline] [HEAD] <filename>`

3. STRUCTURAL BREAKS
- Check if imports reference non-existent files
- Check if removed exports are still imported elsewhere
- Check if critical paths were deleted (route guards, authentication logic, etc.)

4. SIZE ANOMALIES
- Files that went from 1000+ lines to <100 lines → Likely truncation
- Files that decreased by >80% → High risk
- New files with 500+ lines → Check if they properly imported all dependencies

5. PRODUCTION SYNC VERIFICATION
- Compare the changed files with production version
- If production has working CSS/config/variables, ensure dev still has them
- Check: `git show [prod-commit]:<filename>` vs current version

CONTEXT MANAGEMENT:
- Save findings to `code_review_findings.md` for handoff
- Include commit hashes and timestamps for traceability
- Log false positives to improve future reviews

SPECIFIC CHECKS TO RUN:
```
# Check file size changes
git diff --stat [baseline] HEAD | findstr /R "[0-9][0-9][0-9]* -"

# Find files with large deletions
git diff --numstat [baseline] HEAD | Select-String -Pattern "\d+\s+\d{2,}\s+\d+"

# Review each flagged file
git diff [baseline] HEAD <filename>

# Compare with production
git show [baseline]:<filename>
```

OUTPUT FORMAT:
For each flagged file, provide:
1. File path
2. Lines deleted (actual number)
3. Risk level (CRITICAL/MEDIUM/LOW)
4. What was removed (summary)
5. Recommendation (restore/keep/detailed review needed)

IMMEDIATE ACTIONS:
- For CRITICAL issues: Provide exact restoration commands
- For MEDIUM issues: Suggest specific files to examine
- Create a priority-ordered checklist for the developer

PRIORITY ORDER:
1. Configuration files (globals.css, config files) → CRITICAL
2. Core services and utilities → CRITICAL
3. UI components used in production → MEDIUM
4. New components/features → LOW (unless they import non-existent dependencies)

IF REVIEW FINDS NO ISSUES:
- Confirm baseline is correct
- Check if changes are in ignored directories
- Verify git diff commands ran successfully
```

**Quick Session Starter (Compact Version):**

```
Review commits after [baseline] for breakage, not enhancements.

Check for:
- Files with >50 deletions (likely truncation)
- Files with >30% size reduction
- CSS/config files with deletions → CRITICAL
- Core components/services with deletions → MEDIUM

Priority: config files, CSS, services over features.

Show: file name, deletion count, what was removed, risk level, recommendation (restore/review).

Ignore: docs, ideas, locale files, generated files.

Save findings to `code_review_findings.md` with commit hashes for traceability.
```

**Strategic Purpose:**
- Catch accidental file truncations and deletions before production
- Distinguish between intentional refactoring and unintended breakage
- Provide systematic approach to post-commit quality assurance
- Enable quick handoff of review findings to development team

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