
### 5. Sub-agent Orchestration Prompts

Sub-agents are primarily used for **context control** by offloading specific information-gathering tasks, such as "finding where something happens" or "understanding how information flows across multiple components in a codebase". The crucial aspect is to **prompt the parent model on "how it should return its information"** from the sub-agent to keep the context concise and relevant.

**Example Parent Prompt to Initiate Sub-agent for Research:**

"To understand the data flow for the `UserRegistration` process, which spans multiple services, I need specific information about how user data is validated and persisted.

**Launch a sub-agent** and instruct it to **analyze the `auth_service`, `profile_service`, and `database_interface` repositories** to identify:
1.  All functions or methods involved in user data validation during registration.
2.  The sequence of calls between these services when a new user registers.
3.  The specific database tables and fields where user data is ultimately stored.

**Crucially, instruct the sub-agent to return its findings in a highly concise format.** I need a summary that includes **only the relevant file paths, function names, and database schema details (table.field)**, without including the full code or extensive textual descriptions. This information will be directly integrated into my current context to inform the next steps."
**Enhanced Sub-agent Return Format Specifications:**

**Structured Output Requirements:**
Instruct the sub-agent to return information in this exact format:

```
## Sub-agent Research Results

### Key Files:
- `path/to/file.py:function_name()` - Brief purpose
- `path/to/other.py:class.method()` - Brief purpose

### Data Flow:
1. Entry Point → Validation → Processing → Storage
2. [Specific function calls in sequence]

### Database Schema:
- `table_name.field_name` (type, constraints)
- `related_table.foreign_key` → `primary_table.id`

### Integration Points:
- Line X in file Y: Where new code should be added
- Dependencies: [List of required imports/modules]

### Risks/Considerations:
- [Brief list of potential issues]
```

**Context Management for Sub-agents:**
- Limit sub-agent responses to 200 words maximum
- Focus on actionable information only
- Exclude implementation details unless specifically requested
- Use bullet points and structured formatting
- Reference external documentation rather than copying it

**Sub-agent Task Boundaries:**
- Define clear scope: "Analyze only X, ignore Y and Z"
- Set time limits: "Spend no more than 10 minutes on this research"
- Specify depth: "Surface-level overview" vs "Deep technical analysis"
- Include exclusions: "Do not analyze test files or documentation"

**Quality Control for Sub-agent Output:**
- Verify all file paths and function names are accurate
- Ensure database schema information is current
- Check that integration points are specific and actionable
- Confirm that risks are relevant to the current task

**Example Sub-agent Validation Prompt:**
"Before returning your research, verify that:
1. All file paths exist and are correct
2. Function names and signatures are accurate
3. Database schema matches current production
4. Integration points include specific line numbers
5. Response is under 200 words and well-structured"

**Failure Recovery for Sub-agents:**
If sub-agent returns insufficient information:
- Request specific clarification on missing elements
- Provide examples of the expected format
- Break down the task into smaller, more focused queries
- Consider using multiple specialized sub-agents for complex research