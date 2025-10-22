# 1. Foundation: Prompt Engineering

Prompt Engineering is the practice of designing and refining precise, context-rich instructions to guide a Generative AI agent toward producing a specific and accurate output, such as code, documentation, or analysis. It is the essential skill of communicating a developer's intent to the AI in a way that maximizes the quality and relevance of its response.

### Example: From a Weak Prompt to a Strong Prompt

Here is an example that contrasts a weak prompt with a strong, illustrative one for a common development task.

***

#### Weak Prompt (Vague and Ineffective)

> "Refactor this function."

This prompt fails because it lacks a clear goal, provides no context, and doesn't specify the desired outcome.

---

#### Strong Prompt (Concise, Clear, Descriptive, Illustrative)

> **Role:** You are an expert TypeScript developer specializing in data transformation and error handling.
>
> **Task:** Refactor the following `processUserData` function.
>
> **Context:**
> Here is the current function that processes a raw user object. It is inefficient and lacks proper type safety.
> ```typescript
> function processUserData(user) {
>   if (user && user.id && user.profile) {
>     const new_user = {
>       userId: user.id,
>       displayName: user.profile.name,
>       isActive: user.status === 'active' ? true : false,
>     };
>     return new_user;
>   }
>   return null;
> }
> ```
>
> **Requirements:**
> 1.  Convert the function to use modern TypeScript, including defining `interface` types for the input (`RawUser`) and the output (`FormattedUser`).
> 2.  Use optional chaining (`?.`) for safer property access.
> 3.  The `isActive` property should be a strict boolean comparison.
> 4.  If the input `user` object is invalid, the function should throw a `TypeError` with the message "Invalid user data provided."
>
> **Output:** Provide only the complete, refactored TypeScript code, including the new interface definitions. Do not add any surrounding explanations.

***

#### Why This is a Strong Example of Prompt Engineering

The example above is a highly effective use of **prompt engineering** because all the information the AI needs is self-contained within the detailed structure of the prompt itself.

The key elements that make it successful are:
*   **Clear Role-Setting:** It tells the AI *what persona to adopt* ("expert TypeScript developer").
*   **Specific Task Definition:** The goal is unambiguous ("Refactor the following... function").
*   **Sufficient Context:** It provides the exact code snippet to be worked on.
*   **Precise Requirements:** The rules for the output are given as a clear, numbered list.

This method of crafting a detailed, self-contained set of instructions represents the core discipline of prompt engineering. It is about removing ambiguity and providing a complete "work ticket" within the prompt to ensure a high-quality, predictable result.
