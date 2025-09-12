### `Advanced Context Engineering for AI Agents/Working with Gemini/Gemini 2.5 Pro X Structured Workflow.md`

This is a modular flow that blends **Gemini's conversational nature** with the **Structured Workflow Prompt** technique. It creates a layered development pipeline where each phase builds context for the next, while keeping cognitive load and the context window usage efficient, especially when working with features like the 'Import from GitHub' on Gemini.

-----

## ✅ GEMINI 2.5 PRO X STRUCTURED WORKFLOW PROMPT

### 🔹 **Step 1: The Research Prompt**

  - You provide a clear requirement in plain English.
  - Gemini scans the repo, matches your request to the code, and outputs a `research_summary.md` with:
      - Relevant files and line numbers
      - Gaps, ambiguities, or potential conflicts

> *This phase triangulates your intent against the actual codebase.*

-----

### 🔹 **Step 2: The Planning Prompt**

  - You run a Planning Prompt using the `research_summary.md`.
  - Gemini generates a concise `implementation_plan.md` with:
      - Affected files and line ranges
      - Code snippets or pseudocode (not full code)
      - Test/verification steps

> *This becomes your tactical blueprint for implementation, allowing for review before code is written.*

-----

### 🔹 **Step 3: The Iterative Implementation Prompt**

  - You instruct Gemini to execute one step of the plan at a time.
  - As each step is completed, Gemini:
      - Provides the updated code.
      - Updates the `implementation_plan.md` to mark the step as `[DONE]`.
  - This iterative process keeps the context window small and focused.

> *This is your execution layer—clean, versionable, and context-aware, preventing context overflow.*

-----

## 🧠 Why This Works So Well with Gemini

  - **Context Compression**: Each file isolates a phase, keeping context utilization lean and preventing errors from a limited context window.
  - **Traceability**: You can audit every decision from the initial requirement to the final implementation.
  - **Modularity**: You can reuse the `research_summary.md` and `implementation_plan.md` across different sessions or for different parts of a project.
  - **Clarity and Control**: You maintain strategic clarity while Gemini handles the mechanical aspects of coding, giving you a chance to course-correct at each step.

-----
