### 5. Structured Workflow Prompts (Frequent Intentional Compaction)

This is an advanced three-phase workflow: Research, Plan, and Implement, where the entire development process is built around context management.

#### 5.1. Research Prompt

This prompt guides the agent to **understand the system, identify relevant files, and locate problems**. The output should include **file names and line numbers** to provide precise context for subsequent steps.

**Example Research Prompt:**

"Your task is to thoroughly **research the existing codebase** to understand how the current analytics event tracking system is implemented. I need to integrate a new `UserActivity` event.

**Perform a comprehensive analysis focusing on:**
1.  **Identifying all files and modules** responsible for dispatching, logging, and processing analytics events.
2.  **Understanding the data structure** of existing analytics events.
3.  **Pinpointing the exact locations (file paths and line numbers)** where new event types should be registered or where existing event dispatch logic would need modification to accommodate `UserActivity`.
4.  **Tracing the data flow** of an existing event from its trigger point to its final storage or reporting mechanism.

**Generate a detailed `research_summary.md` file** that includes:
*   A high-level overview of the analytics system architecture.
*   **A list of all relevant files with their full paths.**
*   **Specific code snippets and line numbers** indicating key integration points or areas of interest for the `UserActivity` event.
*   A clear explanation of how the data flows.

Ensure this summary is **precise and actionable**, providing all necessary context for a planning agent to formulate changes without further searching."

#### 5.2. Planning Prompt

After research, this prompt guides the agent to create a **detailed plan of every single change** it intends to make, including affected files, code snippets, and explicit testing/verification steps. The plan should be significantly shorter than the actual code changes.

**Example Planning Prompt:**

"Based on the provided `research_summary.md` regarding the analytics event tracking system and the integration of `UserActivity` events, your task is to **create a detailed implementation plan.**

**Your `implementation_plan.md` must include:**
*   **A clear, step-by-step breakdown of every single change** required to integrate the `UserActivity` event.
*   For each change, **specify the exact file path(s)** that will be modified.
*   **Provide snippets of the code** that will be added, modified, or removed, clearly indicating where these changes will occur.
*   **Outline specific test cases and verification steps** for each part of the implementation, ensuring the `UserActivity` event is correctly triggered, structured, and processed.
*   **Describe how existing functionality will be preserved** and any potential side effects mitigated.

The plan should be **comprehensive yet concise**, focusing on the 'what' and 'how' of the changes, preparing for the implementation phase. It should be easy to review and ensure mental alignment for the team."

#### 5.3. Implement Prompt

This prompt instructs the agent to write the code based on the detailed plan. During this phase, context is continuously managed by **updating the plan as phases are completed**, effectively providing a new context window for subsequent tasks and keeping context utilization under 40%.

**Example Implement Prompt:**

"You have been provided with a thoroughly reviewed `implementation_plan.md` for integrating the `UserActivity` event into the analytics system.

Your task is to **execute this plan by writing the necessary code changes**.

**Follow the plan precisely, making changes to the specified files and implementing the provided code snippets.**
*   As you complete each major step outlined in the `implementation_plan.md`, **update the plan itself** to mark that phase as `[DONE]` and provide a brief confirmation of its completion.
*   **Continuously monitor your context utilization.** If you approach 40% of the context window, compact your progress by updating the `implementation_plan.md` with your current status and focus on the next distinct phase described in the plan.
*   **Generate all specified unit tests** to verify the functionality of your changes.

**Do not deviate from the plan.** If you encounter any unexpected issues or require clarifications, halt the implementation, report the problem, and await further instructions. Otherwise, proceed with the implementation, providing periodic updates to the plan."