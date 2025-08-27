For advanced context engineering with LLMs, particularly for coding agents, several categories of prompts and prompting strategies are employed to manage the context window effectively and improve output quality. The overarching goal is to optimize for correctness, completeness, size, and trajectory of the agent's work.
Here are the different categories of prompts and related strategies:

• Corrective or Revised Prompts: When an agent veers off track or "screws up," a common approach is to stop the current process and start over with a fresh context, providing a revised prompt that explicitly tells the agent what not to do or how to approach the task differently. This is a basic form of steering.

• Intentional Compaction Prompts: This strategy involves being deliberate about what information is committed to the file system and the agent's memory to manage context size.
    ◦ A key element is having the agent write out a "progress file" which encapsulates the current state or understanding. This file then serves to onboard the next agent iteration, effectively compacting the relevant context for subsequent steps.

• Looping Prompts for Autonomous Tasks: For certain tasks, a single "same prompt" can be run in a loop for extended periods, such as overnight. This approach is considered effective if there's a deep understanding of LLMs and context windows, allowing the agent to continuously work on a problem.

• Structured Workflow Prompts (Frequent Intentional Compaction): This advanced approach involves building the entire development workflow around context management, typically structured into three phases, each with its specific prompt:
    ◦ Research Prompt: This is often a "really long" and open-source prompt designed to help the agent understand how the system works, identify all relevant files, and locate where a problem resides. The output of this prompt provides crucial context, including file names and line numbers, so the subsequent agent knows precisely where to focus its attention without extensive searching.
    ◦ Planning Prompt: After research, this prompt guides the agent to create a detailed plan of every single change it intends to make. This includes specifying affected files, code snippets, and explicit steps for testing and verification. The plan is significantly shorter than the actual code changes, making it easier to review and ensuring mental alignment within a team.
    ◦ Implement Prompt: This prompt is used to instruct the agent to write the code based on a good plan. During implementation, the context is continuously managed by keeping context utilization under 40% and constantly updating the plan as phases are completed, effectively providing a new context window for subsequent tasks.

• Sub-agent Orchestration Prompts: Sub-agents are primarily used for context control by offloading specific information-gathering tasks.
    ◦ A parent model can be prompted to utilize a sub-agent, for instance, to "find where something happens" or "understand how information flows across multiple components in a codebase".
    ◦ Crucially, the parent model must be prompted on "how it should return its information" from the sub-agent to ensure the output is concise and relevant, preventing the parent agent from being burdened with excessive context from the sub-agent's search.


The effectiveness of these prompts relies on recognizing that a misunderstanding at the research phase (a bad line of research) can lead to thousands of bad lines of code, while a bad part of a plan can lead to hundreds. Therefore, focusing on specifying the right problem and understanding the system through these structured prompts yields greater returns than solely focusing on the code itself