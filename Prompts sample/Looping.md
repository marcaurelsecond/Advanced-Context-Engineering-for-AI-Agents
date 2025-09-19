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
**Enhanced Autonomous Loop Management:**

**Context Window Monitoring:**
- Track context usage after each iteration
- When approaching 35% context usage, create a checkpoint summary
- Archive detailed logs to external files to maintain focus
- Reset context with essential information only

**Progress Tracking Requirements:**
- Maintain `iteration_log.md` with timestamp, changes made, and performance metrics
- Include rollback instructions for each change
- Document any patterns or insights discovered during the process
- Track cumulative improvements and diminishing returns

**Safety Mechanisms:**
- Set maximum iteration limits (e.g., 50 iterations before human review)
- Define failure thresholds (e.g., 3 consecutive failed tests = stop)
- Include health checks to verify system stability
- Implement automatic backup creation before major changes

**Success Metrics for Looping:**
- Each iteration completes within expected time bounds
- Performance metrics show measurable improvement
- No regression in existing functionality
- Context window remains manageable throughout the process

**Failure Recovery:**
If the loop encounters issues:
- Document the exact state when the problem occurred
- Preserve all logs and intermediate files
- Create a detailed handoff summary for human intervention
- Include specific error messages and system state information

**Example Enhanced Loop Structure:**
```
Iteration N:
1. Check context usage (target: <35%)
2. Analyze current bottleneck
3. Plan micro-refactoring (single function/method)
4. Implement with tests
5. Verify performance improvement
6. Update logs and checkpoint if needed
7. Continue or escalate if issues detected
```