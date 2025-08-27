### 3. Looping Prompts for Autonomous Tasks

This approach involves running the **"same prompt in a loop overnight"** for extended periods, which can be highly effective with a deep understanding of LLMs and context windows. The prompt needs to be robust enough to guide the agent through continuous work without constant human intervention.

**Example Prompt for Looping/Autonomous Task:**

"Your ongoing objective is to **continuously refactor the `payment_processing_service.py` module** to improve its performance and reduce latency. You have been provided with the current codebase and performance metrics.

**Your process should be as follows:**
1.  **Analyze the current performance bottlenecks** based on the provided logs and profiling data.
2.  **Identify a small, isolated section of code** within `payment_processing_service.py` that contributes significantly to these bottlenecks.
3.  **Propose a specific refactoring change** to address the identified bottleneck, detailing the expected performance improvement.
4.  **Implement the proposed refactoring** in the codebase.
5.  **Generate unit tests** to verify the correctness and measure the performance impact of your change.
6.  **Update the `refactoring_log.md` file** with details of the change, the performance impact, and the next area you plan to address.
7.  **Loop back to step 1** and continue the process.

**Prioritize incremental changes** and ensure each refactoring maintains full functionality. **Do not introduce breaking changes.** Continue this process autonomously until explicitly stopped, updating the `refactoring_log.md` after each successful iteration."
