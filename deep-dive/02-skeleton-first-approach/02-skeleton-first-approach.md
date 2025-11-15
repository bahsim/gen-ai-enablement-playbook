# Deep Dive: The Skeleton-First Approach

**What if you could validate your architecture before writing a single line of implementation?** The skeleton-first approach does exactly that: create the frame first, then fill in the details. This principle applies to both code (interfaces and method signatures) and documentation (outlines and structure), and it dramatically reduces cognitive load for developers and AI systems.

## The Core Principle

**Skeleton-First Approach:** Create the frame/structure first, then develop inner parts sequentially.

The fundamental insight: structure provides context. By establishing the framework first, you create high-signal context that guides all subsequent work. The skeleton serves as a map — showing what needs to be built and how pieces fit together — before investing effort in implementation details.

### Why It Works: Cognitive Load and Context Windows

**For Developers:**
- **Reduced Cognitive Load:** The skeleton provides a clear mental model of the entire structure without requiring understanding of implementation details
- **Focused Work:** Once the structure is established, developers can focus on one method or section at a time
- **Clear Progress:** The skeleton shows what's done and what remains, providing clear progress indicators
- **Early Validation:** Structural issues are caught early, before investing in implementation

**For AI Systems:**
- **Context Window Efficiency:** The skeleton uses the full context window (the limited amount of information an AI can process at once) for high-level design, then smaller windows for focused implementation
- **High Signal Density:** Skeletons contain high-signal information (structure, relationships, contracts) without low-signal implementation noise
- **Guided Generation:** The skeleton provides clear constraints and context for generating each implementation piece
- **Incremental Context:** Each implementation step can use the skeleton as primary context, with minimal additional information

## Applying Skeleton-First to Code

### The Process

**Step 1: Generate the Skeleton**
Use the full context window to generate only the high-level structure:
- Class definitions with clear responsibilities
- Method signatures with parameter types and return types
- Property names and types
- Type definitions and interfaces
- Detailed docstrings explaining the purpose of each element

**Step 2: Verify the Skeleton**
Review and validate the structure:
- Does the structure align with the requirements?
- Are the interfaces clear and well-defined?
- Do the relationships between components make sense?
- Are there any structural issues that need correction?

**Step 3: Implement Sequentially**
Proceed with focused requests to implement each method or function:
- Use the skeleton as primary context
- Implement one method at a time
- Validate each implementation before proceeding
- The skeleton provides the contract and context for each implementation

### Example: Implementing a Payment Service

**Step 1: Generate Skeleton**
```typescript
/**
 * PaymentService handles payment processing for orders.
 * Supports multiple payment providers through a provider abstraction.
 */
class PaymentService {
  /**
   * Process a payment for an order.
   * @param orderId - The ID of the order to process payment for
   * @param paymentMethod - The payment method to use
   * @returns Payment result with transaction ID and status
   */
  async processPayment(orderId: string, paymentMethod: PaymentMethod): Promise<PaymentResult> {
    // TODO: Implement payment processing
  }

  /**
   * Refund a payment transaction.
   * @param transactionId - The transaction ID to refund
   * @param amount - Optional partial refund amount
   * @returns Refund result with new transaction ID
   */
  async refundPayment(transactionId: string, amount?: number): Promise<RefundResult> {
    // TODO: Implement refund logic
  }

  /**
   * Get payment status for a transaction.
   * @param transactionId - The transaction ID to check
   * @returns Current payment status
   */
  async getPaymentStatus(transactionId: string): Promise<PaymentStatus> {
    // TODO: Implement status retrieval
  }
}

interface PaymentMethod {
  type: 'credit_card' | 'paypal' | 'bank_transfer';
  details: CreditCardDetails | PayPalDetails | BankTransferDetails;
}

interface PaymentResult {
  transactionId: string;
  status: 'success' | 'failed' | 'pending';
  amount: number;
  timestamp: Date;
}
```

**Step 2: Verify Structure**
- Does the service interface match the requirements?
- Are the method signatures clear and complete?
- Do the types accurately represent the domain?
- Are the docstrings sufficient?

**Step 3: Implement Methods Sequentially**
- Implement `processPayment()` first (core functionality)
- Then implement `getPaymentStatus()` (supporting functionality)
- Finally implement `refundPayment()` (extended functionality)

Each implementation uses the skeleton as context, ensuring consistency with the established structure.

### Benefits for Code Development

**Early Architecture Validation:**
- Structural issues are identified before implementation
- Interface problems are caught when they're easy to fix
- Dependencies and relationships are clarified upfront

**Focused Implementation:**
- Each method implementation is a focused task
- The skeleton provides clear context without implementation noise
- Implementation can proceed incrementally with validation at each step

**Better AI Collaboration:**
- AI can generate the skeleton using full context window
- Each implementation step uses skeleton as primary context
- Clear contracts reduce ambiguity in AI-generated code

**Reduced Rework:**
- Structural changes are made early, before implementation
- Implementation follows the established structure
- Less need to refactor due to structural issues

### How AI Helps with Skeleton-First

AI excels at pattern recognition and structure generation, making it ideal for skeleton-first development.

**The Workflow:**
1. **AI generates skeleton:** Provide requirements → AI creates complete structural contract (interfaces, signatures, types, docstrings)
2. **Human validates:** Review structure, verify interfaces and relationships, make adjustments
3. **AI implements:** For each method, provide skeleton as context → AI implements with clear constraints

**Why This Works:**
- **AI excels at structure:** Generating complete, consistent skeletons is a core strength
- **Skeleton provides constraints:** Clear contracts reduce errors and ensure consistency
- **Efficient context usage:** Skeleton uses context window for high-signal information, not implementation noise
- **Focused implementation:** Each step is a focused task with clear context

**Example Prompt Flow:**
- Step 1: "Generate a skeleton for a PaymentService class with methods for processing payments, refunds, and status checks"
- Step 2: [Human reviews skeleton, verifies structure, makes adjustments if needed]
- Step 3: "Implement the `processPayment` method from the PaymentService skeleton"

This workflow leverages AI's strengths while providing the constraints that ensure quality and consistency.

## Applying Skeleton-First to Documentation

### The Process

**Step 1: Create the Structure**
Create the outline, main sections, and organizational framework:
- Document outline with all major sections
- Section headings and subheadings
- Table of contents structure
- Cross-references and navigation

**Step 2: Verify the Structure**
Review the organizational framework:
- Does the structure serve the document's purpose?
- Are sections logically organized?
- Is the navigation clear?
- Are there any structural gaps?

**Step 3: Fill in Details Sequentially**
Develop content section by section:
- Use the structure as a guide
- Fill in one section at a time
- Maintain consistency with the established structure
- The structure ensures comprehensive coverage

### Example: Creating Architecture Documentation

**Step 1: Create Structure**
```markdown
# System Architecture

## Overview
[High-level system description]

## Core Components
[Overview of major components]

### Authentication Component
[Authentication architecture]

### Product Catalog Component
[Product catalog architecture]

### Shopping Cart Component
[Cart architecture]

### Payment Component
[Payment architecture]

## Shared Infrastructure
[Cross-cutting concerns and shared services]

## Component Interactions
[How components interact and integrate]

## Deployment Architecture
[Deployment structure and configuration]
```

**Step 2: Verify Structure**
- Does the structure cover all necessary aspects?
- Are components clearly separated?
- Is the organization logical?
- Are there missing sections?

**Step 3: Fill in Details**
- Start with Overview (establishes context)
- Then each component (one at a time)
- Then cross-cutting concerns
- Finally integration and deployment

Each section is developed with the full structure as context, ensuring consistency and completeness.

### Benefits for Documentation

**Clear Organization:**
- Structure ensures comprehensive coverage
- Logical organization makes navigation easy
- Gaps in coverage are visible early

**Progressive Development:**
- Can develop sections incrementally
- Structure guides what needs to be written
- Can validate organization before investing in details

**Better Collaboration:**
- Multiple authors can work on different sections
- Structure provides clear boundaries
- Consistent organization across sections

## How Skeleton-First Reduces Cognitive Load

Skeleton-first reduces cognitive load by establishing structure before details:
- **Clear mental model** before implementation
- **Focused work** on one piece at a time
- **Guided context** for each implementation step
- **Incremental understanding** that builds progressively

## Strategic Considerations

### The Skeleton as a Contract

The skeleton serves as a contract between design and implementation. This contract must be explicit enough to guide implementation but abstract enough to allow implementation flexibility. The key is finding the right level of detail: too abstract and the skeleton provides no guidance; too detailed and it becomes implementation itself.

**The Balance:**
- **Structure, not logic:** Skeletons define *what* exists and *how* it's organized, not *how* it works
- **Contracts, not algorithms:** Method signatures define inputs, outputs, and behavior contracts, not implementation algorithms
- **Relationships, not data flow:** Skeletons show dependencies and composition, not execution flow

**When Skeletons Are Most Valuable:**
- Complex systems where structure validation is critical
- Multi-component features where relationships matter
- Systems where structural changes are expensive
- When working with AI systems that benefit from clear contracts

### Skeleton Quality: Signal vs. Noise

The value of a skeleton lies in its signal density — the ratio of useful structural information to irrelevant detail. High-quality skeletons maximize signal by including only information that guides implementation or validates structure.

**High Signal Elements:**
- Method signatures with complete type information (enables type checking and clear contracts)
- Docstrings explaining purpose and constraints (provides context without implementation)
- Type definitions and interfaces (establishes data contracts)
- Dependency relationships (shows how components connect)

**Low Signal Elements to Avoid:**
- Implementation logic or algorithms
- Detailed data transformations
- Specific error handling implementations
- Performance optimizations

The skeleton should answer "what exists and how is it organized?" not "how does it work?"

### Verification: Structural Validation Before Investment

Skeleton verification is not about correctness of logic — it's about structural soundness. The goal is to catch architectural issues before investing in implementation.

**What to Verify:**
- **Structural coherence:** Do the pieces fit together logically?
- **Interface completeness:** Are all necessary methods and properties defined?
- **Type correctness:** Do types accurately represent the domain?
- **Relationship validity:** Do dependencies and compositions make sense?
- **Contract clarity:** Are the contracts (method signatures, interfaces) unambiguous?

**When to Verify:**
- Immediately after skeleton generation (before any implementation)
- After any structural changes
- Before proceeding to implementation of each component

Structural issues caught early are trivial to fix. Structural issues caught after implementation require refactoring.

### The Implementation Sequence: Strategic Ordering

The order in which skeleton methods are implemented matters. Not all methods are equal — some provide foundational functionality that others depend on.

**Strategic Ordering Principles:**
- **Foundation first:** Implement methods that others depend on before dependent methods
- **Core before extended:** Implement core functionality before extended features
- **Simple before complex:** Implement straightforward methods before complex ones
- **Independent before dependent:** Implement methods with no dependencies first

**Example:** For a `PaymentService` skeleton:
1. `getPaymentStatus()` - Simple retrieval, no dependencies
2. `processPayment()` - Core functionality, may use status checking
3. `refundPayment()` - Extended functionality, depends on payment processing

This ordering ensures each implementation has the context it needs from previously implemented methods.

### Documentation Skeletons: Information Architecture Decisions

Documentation skeletons encode information architecture decisions. Unlike code skeletons that define structural contracts, documentation skeletons define how knowledge is organized and accessed. The structure itself becomes a constraint that shapes what can be written and how it's discovered.

**The Core Decision:**
What organizational pattern best serves both the document's purpose and the cognitive needs of its readers?

**Why Structure Matters:**
- **Eliminates decision fatigue:** The skeleton pre-decides where information belongs, removing "where should this go?" questions during writing
- **Ensures completeness:** A complete skeleton reveals gaps — missing sections are visible before content is written
- **Enables focused context:** For AI systems, a well-structured skeleton allows providing focused sections as context rather than entire documents
- **Supports multiple entry points:** Different readers need different paths (narrative flow, reference lookup, tutorial progression)

**The Practical Trade-off:**
A skeleton that's too rigid constrains useful information. A skeleton that's too flexible provides no guidance. The balance: structure that guides without constraining, organizes without fragmenting.

**Example:** The architecture documentation skeleton shown above makes explicit decisions:
- **Top-level organization:** Overview → Components → Infrastructure → Interactions → Deployment (progressive detail)
- **Component organization:** Each major component gets its own section (enables focused AI context)
- **Relationship visibility:** Component Interactions section makes connections explicit

This structure isn't arbitrary — it reflects how architectural knowledge is consumed: high-level understanding first, then deep dives into specific areas, then how they connect.

### Common Anti-Patterns

**The Implementation Skeleton:**
Including implementation logic in skeletons defeats the purpose. Skeletons should be compilable but not functional — they define structure, not behavior.

**The Vague Skeleton:**
Skeletons that are too abstract provide no guidance. A skeleton with `processData()` and no type information is useless. A skeleton needs enough detail to serve as a contract.

**The Skipped Verification:**
Proceeding to implementation without validating the skeleton assumes the structure is correct. This assumption is often wrong, and structural issues discovered during implementation are expensive to fix.

**The Deviating Implementation:**
Implementing something different from the skeleton breaks the contract. If the skeleton needs changes, update it first. The skeleton is the source of truth for structure.

**The Parallel Implementation:**
Implementing multiple skeleton methods simultaneously increases cognitive load and reduces the value of the skeleton as context. Sequential implementation maximizes skeleton value.

## Real-World Example: NestJS Backend Generation

The NestJS sample project demonstrates skeleton-first in practice:

https://github.com/bahsim/idgl-dev-system/tree/main/02-implementation/03-nestjs-sample

**Phase 1: Scaffolding (Project Skeleton)**
- Create NestJS project structure
- Generate modules (users, wishes, wishlists, offers, auth)
- Install dependencies
- Establish basic configuration

**Phase 2: Generation (Code Skeletons)**
- Step 1: Generate entity skeletons (TypeORM entities with properties and relationships)
- Step 2: Generate DTO skeletons (validation classes with properties)
- Step 3: Generate service skeletons (service classes with method signatures)
- Step 4: Generate controller skeletons (endpoint definitions)
- Step 5: Implement each skeleton's methods sequentially

**The Workflow:**
1. **Scaffold project structure** (foundation)
2. **Generate entity skeletons** (data layer structure)
3. **Verify entity relationships** (structural validation)
4. **Generate DTO skeletons** (validation layer structure)
5. **Generate service skeletons** (business logic structure)
6. **Generate controller skeletons** (API layer structure)
7. **Implement each layer sequentially** (filling in details)

Each skeleton provides the context for the next, and each implementation uses its skeleton as primary context.

## Conclusion: Structure as Foundation

Skeleton-first recognizes that structure is foundational. By establishing the framework first, you create high-signal context that guides all subsequent work — reducing cognitive load, improving AI collaboration, and producing better results.

Whether working with code or documentation, AI assistance or traditional development, skeleton-first provides a systematic approach: validate structure early, provide clear context for implementation, enable focused incremental work, and produce maintainable, well-organized artifacts.

Structure first, details follow. This is the power of skeleton-first.
