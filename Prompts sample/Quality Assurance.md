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

**For each flagged file, provide:**
1. File path
2. Lines deleted (actual number)  
3. Risk level (CRITICAL/MEDIUM/LOW)
4. What was removed (summary)
5. Recommendation (restore/keep/detailed review needed)

**Save findings to `code_review_findings.md` with commit hashes for traceability.**

Priority order: Configuration files → Core services → UI components → New features."

**Example Prompt 2 for Quick QA Review:**

"Review commits after [baseline] for breakage, not enhancements.

Check for:
- Files with >50 deletions (likely truncation)
- Files with >30% size reduction  
- CSS/config files with deletions → CRITICAL
- Core components/services with deletions → MEDIUM

Priority: config files, CSS, services over features.

Show: file name, deletion count, what was removed, risk level, recommendation (restore/review).

Ignore: docs, ideas, locale files, generated files.

Save findings to `code_review_findings.md` with commit hashes for traceability."

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