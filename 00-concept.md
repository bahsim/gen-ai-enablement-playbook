# The GenAI Enablement Playbook: Concept

This document defines the conceptual foundation for developing the GenAI Enablement Playbook. It explains the core ideas that guide the playbook's structure and content.

## The Problem

Simple prompt-driven development fails on large-scale projects because AI lacks context about architecture, coding standards, and business rules. Providing context manually is too slow and error-prone.

**The insight:** AI output quality depends on context quality, not prompt length. Context must be systematically prepared, not manually gathered.

**The shift:** Using AI effectively requires shifting from line-by-line implementation to directing AI-assisted work. This is a form of delegation—assigning tasks to a fast, knowledgeable, but context-blind assistant. The success of this delegation hinges on the quality of instructions provided.

**What's required:** Effective AI delegation needs a high-level view (Why, What, How) and proper developer onboarding. Onboarding is not optional but essential—only properly onboarded developers can utilize GenAI effectively.

**Another challenge:** Documentation preparation presents the problem of finding the right level of detail. Too shallow documentation is useless but easy to create. Too detailed documentation becomes too complex, mostly useless, and hard to maintain because it requires frequent updates. The right level is basic architectural documentation — comprehensive enough to be useful, abstract enough to remain stable, changing only when architecture changes.

## The Principle

Understanding this problem reveals a core principle: AI does not fix broken processes; it accelerates existing processes, for better or worse. On well-documented projects with clean architecture, AI multiplies effectiveness. On projects with technical debt and poor knowledge sharing, AI amplifies inefficiencies.

**The strategic question:** How do we prepare our engineering discipline for AI-driven acceleration? The answer: address foundational gaps in process and documentation first.

**The self-reinforcing cycle:** Use AI to improve engineering discipline (refactoring, documentation generation, pattern enforcement), which improves context quality, leading to better AI output.

**The golden mean:** Effective engineering requires choosing the right tool for each task—manual labor for strategic decisions, automation by scripts for repetitive rule-based tasks, and AI generation for pattern recognition and synthesis. The optimal solution often emerges from a strategic combination of these three approaches.

**Spec-driven development:** Follow a workflow where the specification drives implementation—improve the spec first, then the code follows.

**Skeleton-first approach:** When developing something, first create the frame/structure, then develop inner parts sequentially. This applies to both code (create interfaces/skeletons first) and documentation (create structure first, then fill in details).

**General rules for AI-agent:** Establish project-specific guidelines and constraints that define how AI should approach tasks—coding standards, architectural patterns, and domain-specific rules that must be followed.

## The Solution: Documentation preparation
The solution is documentation preparation in three concentric circles of constraints. This layered approach results in onboarded developers, provides a high-level view of the project, and reduces the time and effort required to prepare context for each separate task.

The three circles:

- **Basic architectural documentation** is the first circle of constraints—comprehensive and high-level, mostly constant. It cannot and should not cover every flow, edge case, and implementation detail—that would make it impractical and useless. It must be comprehensive enough to be useful, abstract enough to remain stable.

- **System configuration documentation** is the next circle of constraints, bridging universal architecture to actual deployment. It documents what's actually configured in a specific instance, eliminating unnecessary exploration by constraining to what is actually used.

- **Task-specific documentation** is the innermost circle, ephemeral and created for specific tasks, providing focused context that bridges general architecture to implementation.

When documentation is prepared:

- **Preparation in advance:** Basic architectural documentation and system configuration documentation are prepared in advance as mostly first steps. These provide the foundational context that remains stable.

- **Preparation for each task:** Task-specific documentation is prepared for each task separately, creating focused context that bridges general architecture to implementation.

### Techniques for creating and managing context:

- **Architectural Slicing:** Organize knowledge into vertical features and horizontal infrastructure. This becomes the foundation for documentation structure. By organizing documentation by slices, you can provide focused context for a single slice instead of the entire application, dramatically reducing AI context requirements while maintaining high signal density.

- **Structured Decomposition:** Break complex tasks into smaller steps that fit within AI's context window while maintaining high signal density. Approaches include: decomposing according to architectural slices (identify which slices the task touches and create a step-by-step plan slice by slice), the "skeletons first" method (separate design from implementation), and other strategies (by functional steps, data flow, dependencies, etc.).

## The Narrative Flow

The playbook follows this progression:

1. **The Problem:** Simple prompting fails because AI lacks context; manual context gathering is too slow; documentation preparation requires finding the right level of detail
2. **The Principle:** AI amplifies existing processes; golden mean for tool selection; skeleton-first approach; spec-driven development; general rules for AI-agent
3. **The Solution:** Documentation preparation in three concentric circles of constraints (basic architectural, system configuration, task-specific), with techniques for creating and managing context (architectural slicing, structured decomposition)
4. **The Result:** Effective development and evolved developers who direct AI-assisted work with architectural thinking and disciplined workflows

Each chapter builds on previous concepts, creating a coherent system for enabling GenAI on projects.

## The Result

This approach leads to effective development and evolved developers. Developers evolve from line-by-line implementers to directors of AI-assisted work. They possess architectural thinking, create systematic context, and use disciplined workflows that maximize AI effectiveness while maintaining code quality. The project becomes AI-ready not through better prompts, but through better engineering discipline.

---

## Documentation Files Structure

Based on the concept above, the following documentation files should be created:

**01-the-problem.md** - Simple prompting fails; context quality insight; delegation shift; what's required (high-level view, onboarding); documentation level challenge

**02-the-principle.md** - Core principle: AI amplifies existing processes; strategic question; self-reinforcing cycle; golden mean (tool selection); spec-driven development; skeleton-first approach; general rules for AI-agent

**03-the-solution.md** - Documentation preparation: three circles (basic architectural, system configuration, task-specific); when prepared; techniques (architectural slicing, structured decomposition)

**04-the-result.md** - Effective development and evolved developers
