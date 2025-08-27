
### 4. Sub-agent Orchestration Prompts

Sub-agents are primarily used for **context control** by offloading specific information-gathering tasks, such as "finding where something happens" or "understanding how information flows across multiple components in a codebase". The crucial aspect is to **prompt the parent model on "how it should return its information"** from the sub-agent to keep the context concise and relevant.

**Example Parent Prompt to Initiate Sub-agent for Research:**

"To understand the data flow for the `UserRegistration` process, which spans multiple services, I need specific information about how user data is validated and persisted.

**Launch a sub-agent** and instruct it to **analyze the `auth_service`, `profile_service`, and `database_interface` repositories** to identify:
1.  All functions or methods involved in user data validation during registration.
2.  The sequence of calls between these services when a new user registers.
3.  The specific database tables and fields where user data is ultimately stored.

**Crucially, instruct the sub-agent to return its findings in a highly concise format.** I need a summary that includes **only the relevant file paths, function names, and database schema details (table.field)**, without including the full code or extensive textual descriptions. This information will be directly integrated into my current context to inform the next steps."
