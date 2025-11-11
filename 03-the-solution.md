# 3. The Solution

The solution is **documentation preparation in three concentric circles of constraints.** This layered approach results in onboarded developers, provides a high-level view of the project, and reduces the time and effort required to prepare context for each separate task.

## The Three Circles of Constraints

### Circle 1: Basic Architectural Documentation

**Basic architectural documentation** is the first circle of constraints—comprehensive and high-level, mostly constant. It cannot and should not cover every possible flow, edge case, and implementation detail—that would make it impractical and useless. It must be comprehensive enough to be useful, abstract enough to remain stable.

This documentation creates a **comprehensive, high-level blueprint** of the system. It changes only when the architecture itself changes, not with every feature addition or bug fix. It serves all developers throughout the entire development lifecycle, providing the foundational context needed for both human understanding and AI-assisted development.

Basic architectural documentation is intentionally abstract and comprehensive at a high level, providing the strategic overview and structural map of the system, but not the tactical details needed for every specific implementation task.

**The Three-Level Hierarchy:**

- **Level 1: The High-Level Overview:** Multiple entry point documents that provide essential mental models, visualize the entire system, and link to the deeper levels. Different documents serve different entry points (narrative, flow, rules).

- **Level 2: The Deep-Dive Guides:** Each one is a dedicated, comprehensive document focused on a single architectural slice. Architectural slices are the primary organizational unit for this level.

- **Level 3: The Implementation Details:** The lowest level of detail, containing implementation patterns, code navigation guides, and reference materials.

### Circle 2: System Configuration Documentation

**System configuration documentation** is the next circle of constraints, bridging universal architecture to actual deployment. It documents what's actually configured in a specific instance, eliminating unnecessary exploration by constraining to what is actually used.

Basic architectural documentation describes the universal system: what the codebase can support, all possible configurations, integrations, and flows. However, a specific deployment may only use a subset of these capabilities.

**What this layer documents:**
- **Active configurations:** Which integrations, providers, or services are actually configured and enabled
- **Feature flags and settings:** Which features are enabled or disabled in this deployment
- **Environment-specific constraints:** What is available in different environments
- **Deployment-specific limitations:** What capabilities are not used, even though the system supports them

This documentation serves as a **constraint filter** that eliminates unnecessary exploration. It bridges the gap between the universal architectural knowledge and the actual working system, providing the constraints that task-specific documentation will further narrow.

System configuration documentation is **persistent but deployment-specific**—it should be updated when configurations change, but it remains stable across multiple tasks within the same deployment.

### Circle 3: Task-Specific Documentation

**Task-specific documentation** is the innermost circle, ephemeral and created for specific tasks, providing focused context that bridges general architecture to implementation.

This documentation is a **focused, implementation-ready context** that:
- **Narrows the scope** to only the relevant project areas
- **Identifies constraints** (external dependencies, workflow boundaries, architectural rules)
- **Provides detailed context** for the specific area being modified
- **Fits within the LLM's context window** while maintaining high signal density
- **Enables precise implementation** without architectural overhead

Task-specific documentation is created by first constraining the problem space, then preparing focused documentation for that constrained area, and finally using it as the primary context for implementation.

**The approach:**
1. **Constraining the problem space:** Identify which project areas are affected, what external constraints apply, what workflow context the task exists within, and what cases are explicitly eliminated.
2. **Preparing focused documentation:** Extract only the relevant architectural context from general documentation, gather implementation details specific to the task, include relevant code examples, and document what is not part of the task.
3. **Executing with focused context:** Use the task-specific documentation as the primary context for AI-assisted implementation, referencing general architectural documentation only when needed.

This documentation is **ephemeral**—it is created for a specific task and can be discarded or archived after completion. It is the **bridge** between the permanent architectural knowledge base and the temporary implementation context.

## When Documentation is Prepared

### Preparation in Advance

**Basic architectural documentation** and **system configuration documentation** are prepared in advance as mostly first steps. These provide the foundational context that remains stable.

These layers are not created for a specific task but serve as the permanent knowledge base that supports all development work. They are maintained and updated as the architecture evolves, but they remain relatively constant across individual tasks.

### Preparation for Each Task

**Task-specific documentation** is prepared for each task separately, creating focused context that bridges general architecture to implementation.

This documentation is created on-demand, tailored to the specific requirements and constraints of each individual task. It is assembled from the foundational layers, extracting only what is relevant and adding task-specific details as needed.

## Techniques for Creating and Managing Context

### Architectural Slicing

When a developer takes on a complex task, the first step is to slice it into a chain of smaller, manageable sub-tasks. This is a natural workflow that reduces cognitive load and increases productivity.

**Architectural Slicing** is the formal, system-wide application of this exact approach. We slice a large, complex application into smaller **subsystems**, each with its own focused context. This allows a developer to work on a specific task by focusing on the smaller context of a single subsystem, without needing to hold the implementation details of the entire application in memory at once.

There are two fundamental ways to slice an application:

**Vertical Slicing: Slicing Through the Stack**

A vertical slice cuts through the dependent layers of an application's technical stack (e.g., from the user interface down to the database). It represents a complete piece of the application's execution flow.

There are two common strategies:
- **Slicing by Feature:** This is the most common modern approach. Slice the application by user-facing functionality (e.g., `ShoppingCart`, `ProductCatalog`, `UserProfile`). Each feature slice is a self-contained subsystem that includes its user interface code, API endpoints, business logic, and data access code.
- **Slicing by Layer:** This is a more traditional approach where the application is sliced by its technical layers. Frontend layers include `UILayer` (UI components), `BusinessLogicLayer` (state management), `DataLayer` (API clients). Backend layers include `PresentationLayer` (API endpoints), `DomainLayer` (business logic), `PersistenceLayer` (database code).

In both strategies, the slices are **vertical** because they represent the dependent stack of execution.

**Horizontal Slicing: Independent Subsystems**

A horizontal slice is an **independent subsystem** that provides a specific, cross-cutting capability to any vertical slice that needs it. These subsystems are "horizontal" because they can be used by any feature or any layer, and they do not depend on them.

Examples of horizontal slices include:
- **Authentication & Authorization:** A subsystem that verifies a user's identity and permissions
- **Internationalization (I18n):** A subsystem that provides translated text
- **Logging & Telemetry:** A subsystem that provides a standardized way to record events and system metrics
- **Caching:** A subsystem for storing frequently accessed data to improve performance
- **Message Queuing:** A backend subsystem for managing asynchronous communication and background jobs
- **Configuration:** A subsystem for securely loading environment variables, feature flags, and secrets
- **Testing:** A subsystem that provides the tools and environment for verification

The key architectural principle is that a horizontal slice must be completely independent. The `Authentication` subsystem, for example, should have no knowledge of the `ShoppingCart` or the `UserProfile`. It simply provides an authentication service that any part of the application can use. This makes them reusable, replaceable, and easy to maintain in isolation.

**Why Slicing Matters for Documentation and AI Context**

Architectural slices are the primary organizational unit for Level 2 documentation. Each slice is a complete, self-contained document that provides an end-to-end view of a cross-cutting concern or feature area. 

This approach provides two critical benefits:
- **Reduces Developer Cognitive Load:** Allows developers to focus on one concern at a time, without needing to hold the entire application in memory
- **Dramatically Reduces AI Context Requirements:** Instead of providing context for the entire application, you can provide focused context for a single slice, making it fit within the AI's context window while maintaining high signal density

By organizing documentation by slices, you enable focused context preparation for AI-assisted tasks. When a task touches a specific slice, you can provide only that slice's documentation as context, dramatically reducing the amount of information needed while maintaining all relevant details.

### Structured Decomposition

Providing high-quality context is key to effective AI assistance. This exposes a hard physical constraint: the **LLM's context window**. This limited "short-term memory" creates a tension that developers must navigate.

- **Too little context** leads to the failures of "vibe coding."
- **Too much context** can be equally problematic, leading to truncation and a low signal-to-noise ratio that confuses the model.

The goal is not to provide the *most* context, but the most *optimal* context. This is a skill of maximizing **signal density**—packing the most relevant, high-impact information into the limited space of the context window.

**Structured Decomposition** is the practice of breaking down a complex software development task into a sequence of smaller, verifiable, and architecturally distinct steps. Instead of giving an AI a large, vague goal like "implement the feature," an architect can first analyze the request and create a step-by-step implementation plan that guides the AI through the task.

**Approaches to Decomposition:**

1. **Decomposition by Architectural Slices:** Identify which architectural slices the task touches and create a step-by-step plan slice by slice. For example, a UI transformation might touch the `UI & Components` slice, the `State & Data Flow` slice, and the `Integration & Extensibility` slice. Each slice becomes a distinct step with focused context.

2. **The "Skeletons First" Method:** Separate the design phase from the implementation phase. First, use the full context window to generate only the high-level "skeletons" of the required code (class definitions, method signatures, type definitions, docstrings). Once the skeletons are created and verified, proceed with a series of smaller, more focused requests to implement each method or function one by one. The skeleton itself serves as the primary, high-signal context for each subsequent task.

3. **Other Decomposition Strategies:** Tasks can also be decomposed by functional steps, by data flow, by dependencies, or by any other logical division that creates smaller, verifiable steps.

This approach provides several benefits:
- **Context Window Efficiency:** Each step provides focused, high-signal context
- **Incremental Verification:** Each step can be individually tested and verified
- **Isolated Changes:** Problems are immediately isolated to the last small change
- **Low-Risk Workflow:** Transforms development into a low-risk, low-stress, professional workflow

Beyond improving the quality of the AI's output, this decompositional approach enforces a pattern of small, incremental, and easily verifiable changes, aligning perfectly with modern best practices like CI/CD.

## The Complete Solution

Together, these three circles of constraints and the techniques for managing context create a systematic approach to documentation preparation that:

- **Provides foundational context** that remains stable (basic architectural and system configuration documentation)
- **Enables focused implementation** for specific tasks (task-specific documentation)
- **Maximizes context efficiency** through architectural slicing and structured decomposition
- **Reduces cognitive load** by organizing knowledge into architectural slices
- **Transforms context preparation** from a bottleneck into a strategic advantage

This solution addresses the core problem: moving beyond manual, ad-hoc context preparation to build a truly streamlined, scalable, and effective AI-assisted development workflow.
