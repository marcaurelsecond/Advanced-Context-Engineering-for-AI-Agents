
### 1. Corrective or Revised Prompts

When an agent "screws up" or goes "off track," the most straightforward corrective action is to **stop and restart with a fresh context**, providing a revised prompt that includes specific steering instructions. This tells the agent **what not to do or how to approach the task differently**.

**Example Prompt for Correction/Revision:**

"The previous attempt to implement the user authentication module resulted in an infinite redirect loop and did not correctly handle session management. **Discard all previous outputs and restart the task.**

This time, **do not attempt to use `library_X` for session handling**, as it has known incompatibilities with our current framework. Instead, **focus on integrating `library_Y`**, following its recommended best practices for secure session token generation and validation. Ensure that **all redirect logic explicitly checks for successful authentication** before proceeding. Pay close attention to error handling for invalid credentials.

Proceed with the task of implementing the user authentication module, focusing on robustness and correct session management using `library_Y`."

**Example Prompt 2 for Correction/Revision:**
I need to create a handoff prompt for a new AI session to continue working on this problem. 

Please structure the handoff prompt to:
- Clearly state what failed in previous attempts
- Specify what approaches to avoid based on our experience  
- Provide the new AI with enough context to understand the problem immediately
- Include specific technical constraints and requirements
- Give clear direction on the preferred approach to take

The goal is to create a prompt that allows a fresh AI session to pick up exactly where we left off without repeating failed solutions.

Current Context to Include:
- Specific error messages and symptoms we're experiencing
- Technical stack details (e.g., Next.js 15.5.3, Turbopack, next-intl)
- What approaches we've already tried that didn't work
- Current file structure and configuration state
- The exact goal we're trying to achieve

**Template Structure:**
'Previous session failed because [specific issue]. Do NOT attempt [failed approach]. Instead, focus on [preferred approach]. Current state: [file/config status]. Goal: [clear objective].'