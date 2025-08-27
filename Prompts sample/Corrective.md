
### 1. Corrective or Revised Prompts

When an agent "screws up" or goes "off track," the most straightforward corrective action is to **stop and restart with a fresh context**, providing a revised prompt that includes specific steering instructions. This tells the agent **what not to do or how to approach the task differently**.

**Example Prompt for Correction/Revision:**

"The previous attempt to implement the user authentication module resulted in an infinite redirect loop and did not correctly handle session management. **Discard all previous outputs and restart the task.**

This time, **do not attempt to use `library_X` for session handling**, as it has known incompatibilities with our current framework. Instead, **focus on integrating `library_Y`**, following its recommended best practices for secure session token generation and validation. Ensure that **all redirect logic explicitly checks for successful authentication** before proceeding. Pay close attention to error handling for invalid credentials.

Proceed with the task of implementing the user authentication module, focusing on robustness and correct session management using `library_Y`."