### 2. Intentional Compaction Prompts

This strategy focuses on **deliberately managing the agent's memory and the file system to control context size**. The core idea is to have the agent generate a **"progress file"** that encapsulates the current state and understanding, which then serves to onboard the next agent iteration. This helps optimize for correctness, completeness, and size, preventing "too much noise" or "bad info" from flooding the context window.

**Example Prompt for Intentional Compaction:**

"Your current task is nearing a logical checkpoint, or your context window is approaching its limit. To ensure the continuity and efficiency of our work, please **generate or update a file named `progress_summary.md`**.

In this file, provide a **concise summary of your current progress**, including:
*   **Key findings or discoveries** made so far.
*   **The current state of the code or system** you are working on.
*   **Any significant decisions** or assumptions made.
*   **The most critical remaining challenges** or unresolved issues.
*   **A clear outline of the very next logical steps** you intend to take.

This `progress_summary.md` will be used to **onboard the next agent instance**, providing it with all necessary context to continue seamlessly without repeating prior work or requiring extensive re-analysis. Focus on **precision and brevity**, ensuring all essential information is present without unnecessary verbosity, to minimize context burden for future steps."
