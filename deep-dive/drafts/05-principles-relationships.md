# Deep Dive: Relationships Between Core Principles

The playbook introduces several core principles that work together to enable effective AI-assisted development. This deep-dive explores how these principles relate to each other, how they complement one another, and how they create a synergistic system.

## The Core Principles

The playbook's core principles include:

1. **Spec-Driven Development:** The specification drives implementation—improve the spec first, then the code follows
2. **Skeleton-First Approach:** Create the frame/structure first, then develop inner parts sequentially
3. **Structured Decomposition:** Break complex tasks into smaller, verifiable steps
4. **Architectural Slicing:** Organize codebase into self-contained subsystems
5. **General Rules for AI-Agent:** Project-specific guidelines and constraints for AI tasks
6. **The Golden Mean:** Strategic tool selection (manual labor, scripts, AI generation)

## How Principles Work Together

### Spec-Driven Development and Skeleton-First

**The Relationship:** Spec-driven development defines *what* needs to be built; skeleton-first defines *how* it's structured.

**The Workflow:**
1. **Spec defines what:** The specification (Objective, Rationale, Verification Criteria) defines what needs to be built
2. **Skeleton defines structure:** The skeleton defines the structure and interfaces
3. **Implementation fills in:** Implementation makes the skeleton functional

**Why They Work Together:**
- The spec provides the contract and requirements
- The skeleton provides the structural design
- Implementation follows both the spec and the skeleton
- Together, they ensure both correctness (spec) and good structure (skeleton)

**Example:** Building a payment service:
1. **Spec:** "Implement JWT-based authentication with password hashing" (Objective, Rationale, Verification Criteria)
2. **Skeleton:** `PaymentService` class with method signatures (`processPayment`, `refundPayment`, `getPaymentStatus`)
3. **Implementation:** Fill in each method's logic according to the spec

The spec ensures the service does what's needed; the skeleton ensures it's well-structured.

### Skeleton-First and Structured Decomposition

**The Relationship:** Skeleton-first is one approach to structured decomposition. It separates design (skeleton creation) from implementation (filling in details).

**How They Relate:**
- **Structured Decomposition** is the general principle: break complex tasks into smaller steps
- **Skeleton-First** is a specific approach: create structure first, then implement details sequentially
- Other decomposition approaches include: decomposition by architectural slices, by functional steps, by data flow, by dependencies

**When Skeleton-First is Most Effective:**
- The structure itself is complex and needs validation
- Multiple components need to work together
- The relationships between components are critical
- The structure provides high-signal context for implementation

**Example:** Implementing a complete feature:
- **Structured Decomposition:** Break into steps (entities, DTOs, services, controllers)
- **Skeleton-First Approach:** For each step, create skeleton first, then implement
- **Result:** Each step has clear structure before implementation begins

### Spec-Driven Development and Structured Decomposition

**The Relationship:** Spec-driven development provides the contract for each decomposed step.

**How They Work Together:**
- **Structured Decomposition** breaks the task into steps
- **Spec-Driven Development** provides a spec for each step (or the overall task)
- Each step can be validated against its spec
- The overall task is validated against the overall spec

**The Workflow:**
1. Create spec for the overall task
2. Decompose the task into steps
3. For each step, ensure it aligns with the spec
4. Implement each step to match the spec
5. Validate the overall result against the spec

**Example:** Building an authentication system:
- **Overall Spec:** "Implement JWT-based authentication" (with Objective, Rationale, Verification Criteria)
- **Decomposition:** Entities → DTOs → Services → Controllers
- **Each Step:** Aligned with the overall spec, implemented sequentially
- **Validation:** Final system validated against the spec

### Architectural Slicing and Structured Decomposition

**The Relationship:** Architectural slicing organizes the codebase; structured decomposition breaks down tasks. They work together when tasks span multiple slices.

**How They Work Together:**
- **Architectural Slicing** organizes the codebase into slices (authentication, shopping-cart, payment)
- **Structured Decomposition** can decompose tasks by slice
- When a task touches multiple slices, decompose it slice by slice
- Each slice provides focused context for its part of the task

**Example:** Adding a feature that touches multiple slices:
- **Task:** "Add guest checkout" (touches cart, payment, order slices)
- **Decomposition by Slices:**
  1. Cart slice: Allow guest cart creation
  2. Payment slice: Handle guest payment processing
  3. Order slice: Create orders for guests
- **Each Step:** Focused on one slice, with that slice's context

### General Rules for AI-Agent and All Principles

**The Relationship:** General rules for AI-agent provide the constraints and guidelines that all other principles follow.

**How They Work Together:**
- **General Rules** define coding standards, architectural patterns, domain rules
- **All Other Principles** operate within these constraints
- Specs must align with general rules
- Skeletons must follow architectural patterns from general rules
- Decomposition must respect architectural boundaries from general rules

**Example:** General rules define:
- "All React components must be functional components using hooks"
- Specs for new components must align with this rule
- Skeletons for components must follow this pattern
- Decomposition must respect component boundaries

### The Golden Mean and All Principles

**The Relationship:** The Golden Mean guides tool selection for each step of any principle.

**How They Work Together:**
- **Any Principle** creates steps or phases
- **The Golden Mean** guides which tool to use for each step
- Manual labor for strategic decisions (specs, architecture)
- AI generation for pattern recognition (skeletons, implementation)
- Scripts for repetitive tasks (scaffolding, automation)

**Example:** Skeleton-first workflow:
- **Create Spec:** Manual labor (strategic decision)
- **Generate Skeleton:** AI generation (pattern recognition)
- **Verify Skeleton:** Manual labor (review and validation)
- **Implement Methods:** AI generation (code generation)
- **Run Tests:** Scripts (automated testing)

The Golden Mean guides tool selection at each step.

## The Synergistic System

These principles don't work in isolation—they create a synergistic system:

**Foundation Layer:**
- **General Rules for AI-Agent** provide the constraints
- **Architectural Slicing** provides the organizational structure

**Planning Layer:**
- **Spec-Driven Development** provides the contracts
- **Structured Decomposition** breaks tasks into steps
- **Skeleton-First** provides structural design for each step

**Execution Layer:**
- **The Golden Mean** guides tool selection for each step
- Principles work together to enable focused, efficient execution

**The Result:** A development process that:
- Has clear constraints and structure (general rules, architectural slicing)
- Has clear contracts and plans (specs, decomposition, skeletons)
- Uses optimal tools for each step (golden mean)
- Reduces cognitive load at every stage
- Produces high-quality, maintainable code

## Practical Workflow: Principles in Action

**Example: Adding a New Feature**

1. **Spec-Driven Development:** Create spec (Objective, Rationale, Verification Criteria)
   - Tool: Manual labor (strategic decision)
   - Output: Clear contract for the feature

2. **Architectural Slicing:** Identify which slices the feature touches
   - Tool: Manual labor (architectural analysis)
   - Output: List of affected slices

3. **Structured Decomposition:** Break feature into steps
   - Tool: Manual labor (planning)
   - Output: Step-by-step plan

4. **Skeleton-First:** For each step, create skeleton
   - Tool: AI generation (pattern recognition)
   - Output: Structural skeletons

5. **General Rules:** Ensure skeletons follow patterns
   - Tool: Manual review (validation)
   - Output: Validated skeletons

6. **Implementation:** Fill in each skeleton
   - Tool: AI generation (code generation)
   - Context: Skeleton + spec + general rules
   - Output: Implemented code

7. **Validation:** Verify against spec
   - Tool: Scripts (automated testing)
   - Output: Validated feature

**The Workflow:**
```
Spec → Slicing → Decomposition → Skeletons → Rules Validation → Implementation → Validation
```

Each principle contributes to a different stage, and the Golden Mean guides tool selection at each stage.

## Conclusion: Principles as a System

These principles are not isolated techniques—they form a coherent system. Understanding how they relate helps you:
- Apply them effectively together
- Choose the right principle for each situation
- Create workflows that leverage their synergy
- Build development processes that are more than the sum of their parts

The power comes not from any single principle, but from how they work together to create a systematic, efficient, and effective development process.

