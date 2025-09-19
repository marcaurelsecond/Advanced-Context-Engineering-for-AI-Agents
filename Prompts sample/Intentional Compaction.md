### 2. Intentional Compaction Prompts

This strategy focuses on **deliberately managing the agent's memory and the file system to control context size**. The core idea is to have the agent generate a **"progress file"** that encapsulates the current state and understanding, which then serves to onboard the next agent iteration. This helps optimize for correctness, completeness, and size, preventing "too much noise" or "bad info" from flooding the context window.

**Enhanced Compaction Trigger:**

"Monitor your context utilization continuously. When you reach 35-40% of your context window, or after completing 3-4 major tasks, initiate compaction by generating `progress_summary.md`.

**Example Prompt for Intentional Compaction:**

"Your current task is nearing a logical checkpoint, or your context window is approaching its limit. To ensure the continuity and efficiency of our work, please **generate or update a file named `progress_summary.md`**.

Include these specific sections:
*   **Completed Tasks**: What's been finished with verification status
*   **Current State**: Exact file modifications and system status  
*   **Key Decisions**: Why certain approaches were chosen with rationale
*   **Blockers Resolved**: What obstacles were overcome and how
*   **Next 2-3 Steps**: Immediate actionable items with specific file paths
*   **Context for Handoff**: Critical information the next agent needs
*   **Risk Assessment**: Potential issues or dependencies to watch
*   **Testing Status**: What's been validated and what still needs testing

This `progress_summary.md` will be used to **onboard the next agent instance**, providing it with all necessary context to continue seamlessly without repeating prior work or requiring extensive re-analysis. Focus on **precision and brevity**, ensuring all essential information is present without unnecessary verbosity, to minimize context burden for future steps."


---

**Example Prompt for Onboarding the Next Agent Instance:**

"You are a new agent instance, taking over a complex development task. To ensure a seamless continuation of work, you have been provided with a file named `progress_summary.md`.

**Your primary task is to thoroughly review and internalize the `progress_summary.md` file.** This document contains the complete context, current state, key decisions, remaining challenges, and the immediate next steps from the previous agent's work.

*   **Do not re-evaluate or re-read previous raw outputs or an extensive history of interactions.** Your understanding of the project's current status and trajectory should be **derived exclusively from the information presented in `progress_summary.md`**.
*   **Identify the 'Next Logical Steps'** clearly outlined in the summary.
*   **Based on the summary, execute the very next action required** to advance the project.
*   **If the context window allows and it's beneficial, propose an updated `progress_summary.md`** after completing a significant step or if new critical information emerges, to ensure continuous, efficient context management.

Begin by confirming your understanding of the `progress_summary.md` and then proceed with the first identified 'Next Logical Step' described within it."

**Success Metrics for Compaction:**
- Progress summary is under 500 words but contains all critical information
- Next agent can continue without asking clarifying questions
- No important context or decisions are lost
- File paths and technical details are specific and actionable

**Failure Recovery:**
If the compacted summary is insufficient:
- Include specific line numbers and code snippets for context
- Add environment setup instructions if configuration changed
- Document any temporary workarounds or patches applied
- List exact versions of dependencies or tools used

**Context Window Management:**
- Aim to keep context utilization under 40% after compaction
- Archive detailed logs and outputs in separate files if needed
- Reference external documentation or specs rather than copying them
- Use bullet points and structured formatting for quick scanning