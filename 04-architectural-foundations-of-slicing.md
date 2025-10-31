# Chapter 4: Architectural Foundations of Slicing

## The Management of Cross-Cutting Concerns: Architecting Global Slices in Modern Frontend Systems


### Section 1: Foundations of Architectural Decomposition and the Problem of Cross-Cutting Concerns

The inherent complexity of large-scale frontend applications mandates architectural decomposition. While feature-based organization (vertical slicing) has become the contemporary standard for managing business complexity, all systems inevitably develop a parallel structure based on technical, non-functional requirements. These technical requirements necessitate a form of "global slicing" that affects the entire application horizontally. This report formally defines and analyzes this crucial architectural dimension, rooted in the principles of Cross-Cutting Concerns (CCCs).

#### 1.1 Defining Core vs. Cross-Cutting Concerns (CCCs)

In software architecture, a Concern refers to a functional part of the system.1 Concerns are fundamentally divided into two categories based on their scope and application throughout the system.
Core Concerns represent the primary functionality or business logic of the system. They are the essential requirements for which the application exists.1 For instance, in an application designed to manage medical records, the core concern is the efficient indexing and management of those records.2
In contrast, Cross-Cutting Concerns (CCCs) are system-wide functionalities applicable across the entire application.1 They are aspects of a program that affect several modules simultaneously, often making it impossible to encapsulate them cleanly within any single module.2 These concerns are generally secondary requirements that facilitate the core business logic but are not the logic itself. Classic examples of CCCs in any application, including the frontend, include logging, security and authentication systems, caching mechanisms, data validation logic, and persistence management.1 An authentication system, for example, must interact with nearly every component that accesses restricted data, thus cutting across the entire software structure.2
This architectural phenomenon can be analogized to the essential systems of a physical structure: if a house’s core concern is providing shelter, cross-cutting concerns are the essential, ubiquitous utilities like the electrical wiring or the security system.2 These systems run through the walls of every room (module), connecting multiple points of entry and consumption, and cannot be confined to just one specific area.2

#### 1.2 The Intrinsic Challenge: Scattering and Tangling

The ubiquitous nature of CCCs presents two major implementation difficulties that degrade code quality and maintainability: tangling and scattering.2
Tangling occurs when the code responsible for a CCC is interwoven with the code of the core concern within a single module or function.3 This intertwining obscures the primary purpose of the module, making the core logic less clear and harming reusability.3 If a function's primary goal is to perform a business calculation, but half of its lines are dedicated to logging the input and output, the logging logic is tangled with the core calculation.3 The module can no longer be reused for its core purpose without also dragging along the logging infrastructure.
Scattering describes the maintenance nightmare where identical or near-identical implementations of a CCC are distributed or duplicated across numerous modules throughout the codebase.2 If an architect decides to change how system events are logged—perhaps switching from an entering method to an info method and adding extra metadata—this change might need to be replicated manually across hundreds or thousands of files.3 This scattering of technical logic makes system-wide modifications excruciatingly complex and prone to errors, significantly hindering maintenance efforts and module traceability.4
Architectural methodologies for managing global slices are fundamentally an effort to combat these two anti-patterns. By externalizing and encapsulating CCCs into dedicated, decoupled units, architects aim to reduce tangling in core feature logic and eliminate scattering across the entire system.3

#### 1.3 From AOP to Composition: The Architectural Shift

Historically, the resolution to scattering and tangling in backend systems was Aspect-Oriented Programming (AOP). AOP proposed encapsulating scattered concerns into reusable "Aspects" that could be woven into the core code at compile time.
In modern, component-driven frontend frameworks, the concept of AOP has been superseded by Composition-Oriented Programming. This approach achieves the same goal—decoupling technical concerns from business logic—but through dynamic composition rather than inheritance or metaprogramming.5 Higher-Order Components (HOCs) and, more recently, Custom Hooks function as effective composition-based "aspects." These compositional patterns dynamically inject CCC logic (such as authentication context or data subscription management) into components without requiring direct modification of the components' internal structure. This technique directly addresses the problem of tangling at the component level, ensuring that the component's internal logic remains focused on its core rendering responsibility.5 This shift leverages the compositional nature of libraries like React to provide elegant, runtime solutions for architectural problems that were once solved by complex compiler mechanisms.

### Section 2: Architectural Slicing Models and the Horizontal/Vertical Tension

The architectural debate in scalable applications often centers on how to organize the codebase. The resulting tension between feature-first organization (vertical slicing) and technical-first organization (horizontal slicing) defines the structure of modern large-scale frontends.

#### 2.1 Vertical Slicing: Domain Encapsulation (Feature-First)

Vertical Slicing Architecture (VSA) dictates that code should be organized around vertical segments of business functionality or use cases, rather than around horizontal technical layers like data, business, and presentation.7
This methodology encourages exceptional encapsulation.8 All technical elements required to execute a business function—the UI, the state, the data fetching logic—are co-located within a single, business-scoped unit.7 This self-contained organization ensures that when a feature needs to be added or changed, the modifications are scoped exclusively to that area of business concern, rather than spreading across disparate technical layers.7 This clarity and encapsulation significantly improve long-term maintainability and support rapid feature delivery, making vertical models, such as Feature-Sliced Design (FSD), ideal for large, complex applications.8 In FSD, layers like pages, widgets, and features are broken down into distinct slices based on the domain (e.g., pages/home, features/checkout).9

#### 2.2 Horizontal Slicing: The Necessary Technical Domain

Despite the dominance of feature-centric architectures, technical requirements and fundamental infrastructure necessitate a distinct form of global organization—the horizontal slice. This slicing is based purely on technical function or universally reusable infrastructure, answering the user's observation of non-feature-level decomposition.
The central conflict arises when feature architectures, which emphasize strict vertical boundaries, must account for highly reusable, non-domain-specific technical components, such as a core logging utility, a shared API wrapper, or the global authentication context provider. These elements must be accessed by every feature, making them inherently horizontal.
Methodologies like Feature-Sliced Design acknowledge and formalize this requirement by explicitly designating specific top-level folders that are exceptions to the feature-based slicing rule.9 These technical domains, typically categorized as the App and Shared layers, contain segments based on technical purpose, not business logic.10 For instance, shared might contain ui (reusable components without business logic) or api (low-level network utilities), while app might handle routes and global analytics initialization.9 These layers are, by definition, intentionally designed horizontal slices that house CCCs and essential infrastructure designed for global reuse and decoupling.

#### 2.3 Trade-offs in Decomposition Strategy (Hybrid Approach)

The analysis confirms that architectural success in large-scale applications is achieved through a pragmatic hybrid model, not a rigid adherence to pure vertical or horizontal layering.11 While vertical slicing is crucial for optimizing business agility and feature delivery 12, infrastructure requires dedicated horizontal slicing.
Abstract models, isolated components, and technical services offer significantly higher reusability and are easier to manage when they are grouped based on their technical function rather than being scattered across feature domains.11 Therefore, the architecture must support both highly cohesive domain logic (maintained through vertical slicing) and highly reusable, decoupled infrastructure components (maintained through strictly governed horizontal slicing).11 This hybrid approach, treating technical domains as first-class, globally accessible, yet strictly isolated slices, is the necessary compromise to achieve high scalability and maintainability.

**Table: Architectural Slicing: Comparison of Decomposition Methods**

| Criterion | Vertical (Feature/Domain) Slicing | Horizontal (Technical/Global) Slicing |
| :--- | :--- | :--- |
| **Primary Goal** | Encapsulation of business logic; focused feature delivery. | Decoupling and reuse of infrastructure logic (CCCs); maintaining system integrity. |
| **Organizational Basis** | Business use case or domain model (e.g., user-profile, checkout). | Technical concern or functional type (e.g., logging, api-service, design-system). |
| **Common Pitfall** | Scattering of shared utility/technical logic; potential duplication of core infrastructure. | High coupling between disparate feature systems if not governed; increased cognitive load for new developers. |
| **Governing Principle**| Cohesion and Modularity (DDD). | Decoupling and Reusability (SoC). |


### Section 3: The Cognitive and Maintenance Burden of Dispersed Logic

The presence of unmanaged or poorly structured technical slicing—a phenomenon exacerbated by adherence to older N-Tier (pure technical layer) architectures—imposes substantial hidden costs related to developer efficiency and architectural debt.

#### 3.1 The Failure of Pure Technical Layering

In traditional layered frontend architectures, code is organized strictly by technical type (e.g., dedicated global directories for components/, services/, utils/). While this structure appears logically consistent, it fails catastrophically at scale because it prioritizes technical type over business cohesion.
To implement or debug a single feature change (e.g., modifying the calculation of tax on an order), a developer must navigate across numerous technical layers: from the page definition, down into a presentation component, across to a state hook, and finally into a remote service layer.13 This non-scalable structure inherently slows down development cycles, introduces an elevated risk of bugs due to changes propagating unexpectedly across unrelated technical layers, and creates a steep learning curve for new team members attempting to map the system's runtime behavior.8 Organizing code by domain (feature-based) rather than type is a proven strategy to mitigate this problem by promoting modularity and encapsulation.13

#### 3.2 Quantifying Cognitive Load in Layered Systems

The most significant consequence of dispersed technical logic is the steep increase in developer cognitive load, defined as the mental effort required to understand and operate within the system.14 When logic related to a single business concern is fractured across horizontal technical layers, the developer is constantly forced to engage in context switching and mental mapping.
This cognitive burden is intensified because pure technical layering requires developers to constantly connect the business requirement to its dispersed technical implementation.15 If a technical slice—such as error code handling or data formatting—is scattered or inconsistently implemented, engineers must mentally recreate the specific mapping (ee.g., linking a numeric status code to its human-readable meaning and required action) every time they encounter it.15 Furthermore, structuring engineering teams around technical layers (frontend, backend) compounds this issue, as shipping a single vertical feature requires coordination and handoffs across multiple technical silos, further increasing mental effort.14 Shifting to development teams that own a complete slice of functionality (stream-aligned teams) significantly reduces this mental juggling and promotes more focused problem-solving.14

#### 3.3 The Role of Encapsulation in Reducing Debt

Unmanaged technical slicing, leading to high levels of scattering and tangling, is a direct precursor to increased technical debt and system fragility.4 When technical logic is tightly coupled or duplicated, maintaining the code becomes economically unsustainable.16 Projects become unmaintainable as requirements shift, requiring increasingly long periods just to locate the correct file or debug issues that span multiple, tightly coupled systems.16
The establishment of governed technical slices, such as the shared or core modules, serves as a crucial defensive strategy. By centralizing and encapsulating infrastructural and cross-cutting concerns within these defined boundaries, the system ensures that changes to, for instance, a networking library or a logging service, do not indiscriminately destabilize unrelated business features. Structuring code by domain and ensuring strong encapsulation are critical principles for building maintainable and sustainable frontends.8

### Section 4: Implementation Patterns: Evidences of Architectural Slicing in Modern Frameworks

In modern component-based frameworks, the governance of global technical slices relies heavily on composition patterns that achieve decoupling at runtime. These patterns are the concrete "evidences of slicing" observed across complex frontend projects.

#### 4.1 Decoupling Logic using Composition Patterns

The industry has evolved specific patterns to inject CCC logic cleanly, reducing the tangling within core components.

##### Higher-Order Components (HOCs)

HOCs were historically a key technique in React for reusing component logic.5 Concretely, an HOC is a function that accepts a component and returns a new, enhanced component, acting as a powerful decorator.5 They were frequently utilized to handle classic cross-cutting concerns, such as authentication checks, conditional rendering based on user permissions, or applying centralized logging and error handling wrappers.17 HOCs replaced earlier, problematic mechanisms like mixins which often introduced more coupling than they solved.5

##### Custom Hooks

Custom Hooks represent an architectural refinement in the evolution of compositional programming.6 They are functions that encapsulate reusable stateful logic and side effects (like subscriptions, data fetching, or timers) without modifying the component hierarchy itself.6
Hooks offer several advantages over HOCs. They promote cleaner, more composable code by eliminating the complexity associated with component nesting and "prop drilling," which often arose when chaining multiple HOCs.6 Custom Hooks have become the canonical solution for abstracting complex infrastructure APIs. For instance, a technical slice responsible for network monitoring might encapsulate browser APIs (navigator.onLine) or specialized React APIs (useSyncExternalStore) into a simple useOnlineStatus hook, allowing feature components to consume this cross-cutting status without needing to understand the underlying implementation complexity or edge cases.18 This represents the modern architectural pattern for applying cross-cutting logic directly to functional components.

#### 4.2 Slicing the Global Infrastructure: Specific Architectural Targets

Specific CCCs necessitate dedicated, globally accessible slices, regardless of the vertical feature structure.

##### Error Handling and Logging Slice

Effective observability requires decoupling error logic from the component presentation. Relying solely on localized try-catch blocks results in logging logic being scattered and tangled throughout the application.19
The architectural solution involves hooking into global infrastructure entry points. In environments utilizing advanced data fetching libraries (such as TanStack Query), the implementation of global slicing is highly formalized. These libraries provide global event handlers (callbacks) on the QueryCache and MutationCache.20 Architects define centralized onError callbacks within the application's bootstrapping phase. This configuration creates a single, governed place to intercept and handle all network failures, ensuring consistent logging to external tools (like Sentry) and managing global error messages without modifying the thousands of feature components that initiate data retrieval.20 This technique ensures that failure management logic is centralized, preventing scattering, and allowing for graceful degradation of functionality.19

##### Data Access and Persistence Slice

Data access is inherently cross-cutting as most features rely on network communication. Even within feature-first designs, it is necessary to abstract and formalize the technical concern of network interaction into dedicated segments.
In FSD, for example, the entities layer contains slices (representing core domain objects like a blog or user) that are further divided into technical segments like model and ui.22 The model segment is specifically designated to define data fetching and state management mechanisms, serving as a clean, technical slice for persistence logic that is distinct from the visual components.10 This segment acts as the localized infrastructure wrapper for the entity, ensuring that data retrieval specifics are encapsulated and reusable.

##### State Management Slice

State management itself is often a critical technical slice. While developers are encouraged to use local state where possible, global state remains essential for managing infrastructure-level CCCs, such as application-wide authentication status, session validity, or global notifications.13
For large-scale state systems (like Redux or Zustand), the global store is intentionally partitioned into distinct "slices," each corresponding to a specific technical or domain concept (e.g., authSlice, themeSlice).13 This approach slices the global state domain to prevent tight coupling. Instead of one monolithic state object that must be accessed everywhere, developers interact with isolated, bounded contexts of state, improving scalability and maintainability.13

**Table: Implementation Patterns for Cross-Cutting Concerns in Frontend Architecture**

| Cross-Cutting Concern (CCC) | Architectural Role | Primary Implementation Pattern | Framework Technique |
| :--- | :--- | :--- | :--- |
| **Security/Authentication** | Access Control & Authorization | Component Decorator / Router Guards | HOCs, Custom Hooks (e.g., useAuth) 6 |
| **Logging & Error Reporting** | System Observability | Global Cache Interceptors | React Query Cache Callbacks, Root Error Boundaries 20 |
| **Data Synchronization** | State & Side Effects | Encapsulated State Logic | Custom Hooks (e.g., useOnlineStatus) 6 |
| **Persistence & API Calls** | Network Abstraction | Dedicated Service/Model Segments | API Wrappers, Data Access Layer Modules 10 |
| **UI Appearance/Theming** | Presentation Consistency | Centralized Context Provider | React Context, HOCs for theming 25 |

### Section 5: Strategic Governance of Global Slices

The mere creation of global technical slices is insufficient; they require rigorous governance rules to ensure they facilitate decoupling rather than becoming tightly coupled bottlenecks.

#### 5.1 Architectural Rules for Dependency Management

To maximize reusability and minimize the blast radius of infrastructure changes, strict dependency management must be enforced upon all horizontal slices.
The core principle is that technical slices (often residing in shared or core modules) must be entirely agnostic of specific business features. They are designed to be utilities that serve feature modules but must never introduce dependencies on those features. This "upward" dependency flow ensures that the infrastructure remains stable and decoupled.
This governance strategy is intrinsically linked to testability. Isolated services and abstract models are significantly more reusable and testable when they are cleanly separated from high-level feature logic.11 By enforcing this clean separation, architects maximize the potential for reuse across simultaneous projects and simplify maintenance.11 The governance of the technical slice is thus measured by its success in achieving maximal reusability while eliminating any possibility of feature leakage back into the infrastructure layer.

#### 5.2 Organizing the Global Slices: The Shared and App Modules

A successful hybrid architecture formally separates global concerns into specialized modules with distinct responsibilities:

##### The Shared Module (or Entities and low-level Features)

The Shared slice is dedicated to housing universally reusable code that is independent of specific application logic. This includes the centralized design system library 25, reusable UI components that contain no business logic (e.g., a generic table component) 16, low-level utility functions (e.g., date formatting), and all fundamental type definitions used across the application.9 This module is the application's library of pure, reusable building blocks.

##### The App Module (or Core)

The App module is dedicated to application bootstrap, configuration, and the initialization of global framework concerns.9 This slice houses the global root providers (such as AuthContext, global ThemeContext), application-level routing definitions, and the initializers for analytics, logging, and global error boundaries.9 This layer effectively manages the application life cycle and provides the global contexts that HOCs and Custom Hooks consume to implement CCCs. All core infrastructure logic that is necessary for the application to function, irrespective of feature implementation, resides here.16

#### 5.3 Modular Monolith Strategy for Technical Scaling

For enterprise environments managing large codebases, the Modular Monolith strategy provides an essential architectural framework for governing these technical slices. A Modular Monolith dictates the explicit identification of boundaries (slices) within a single codebase.8
This identification process applies directly to technical slices. By treating the Authentication Service or the Data Access Layer as dedicated, internal modules, the architecture retains the low maintenance overhead and agility of a single monolith while enforcing high separation of concerns.8 This approach ensures that the infrastructure is correctly sliced and encapsulated from day one. Should scaling demands later require the extraction of a specific CCC—such as moving the Authentication service into a dedicated platform microservice—the modular boundaries already established within the frontend significantly ease the transition, since the technical slice is already decoupled and highly cohesive internally.

### Section 6: Visualization and Communication of Cross-Cutting Flow

One of the greatest challenges in managing cross-cutting concerns is communication: their inherent scattering makes their flow difficult to visualize using static component diagrams. Specialized visualization techniques are essential for translating the abstract rules of horizontal slicing into actionable operational maps.

#### 6.1 Mapping the Architectural Structure with the C4 Model

The C4 model provides a consistent, hierarchical set of abstractions for describing software architecture to both technical and non-technical audiences.26 This model is effective for mapping the physical location of a CCC slice.
At the Container level, the frontend application itself is documented as an independently deployable unit.26 The CCCs, organized as dedicated technical slices (e.g., the Authentication module, the core API service wrapper, or a payment card validator), are mapped as Components within that Container.26 Static C4 diagrams (Context and Container diagrams) visually communicate where the technical slice resides within the overall architecture.

#### 6.2 Tracing a Cross-Cutting Flow in Runtime

While static diagrams show location, they fail to illustrate the runtime collaboration required to execute a user story, especially when a CCC is involved. For this, dynamic models are essential.
Dynamic Diagrams (C4) and UML Sequence Diagrams are designed to illustrate how components collaborate at runtime in a time-ordered sequence of steps.28 They are the definitive tools for visualizing a single, specific CCC flow, such as a security validation or a request-logging process.30
To trace a CCC flow:
Define the Scenario: Focus on a single use case (e.g., a user submitting a form that requires authentication and transaction logging).29
Order the Interactions: Use numbered interactions to precisely indicate the sequence of messages and component collaborations (e.g., "1. Feature Component calls API; 1a. Logging Component records API request start; 1b. Authentication Service validates token").28
Highlight the Path: Visualization tools can employ color coding or "Flows" to visually emphasize only the messages and components related to the CCC being analyzed (e.g., making all security-related interactions bold or red).30
Model Failure States: Sequence diagrams allow the use of combined fragments (such as negative fragments) to explicitly model error paths or timeouts related to a CCC (e.g., demonstrating the steps taken if an authentication token expires).31

#### 6.3 Communication of CCC Impact

The capability to visualize the dynamic flow of a CCC provides a powerful governance mechanism. Cross-cutting concerns, such as security, reliability, and usability, are fundamental quality concerns, and their scattered nature can easily lead to unseen problems and architectural non-conformance if left undocumented.4
When a developer can clearly trace the flow of a CCC (e.g., visualizing how the global logging component intercepts data validation errors before they reach the user interface), they gain a breadth of understanding about the quality concern's relationship with functional artifacts.4 This process translates the abstract architectural rule (the existence of a horizontal slice) into a practical, traceable operation. This enhanced clarity is crucial for developers to perform code modifications with confidence, prevent inadvertent introduction of new bugs, and conduct a detailed, holistic change impact analysis across the system.4

### Conclusions and Recommendations

The analysis confirms that the "global slices" observed across frontend projects are not random structural artifacts but are the necessary architectural mechanisms deployed to manage the inherent challenges posed by Cross-Cutting Concerns (CCCs). Successful large-scale architecture requires a deliberate hybrid strategy, integrating feature-centric vertical slicing with strictly governed technical horizontal slicing.
Formalize the Horizontal Slice: Every major CCC (Authentication, Logging, Data Access) must be formalized as a distinct, dedicated architectural slice, typically contained within the App or Shared boundaries. These slices must be treated as downward-facing infrastructure, strictly prohibited from depending on any specific feature implementation.
Adopt Compositional Decoupling: The use of Custom Hooks is the current best practice for implementing CCCs compositionally. They provide superior decoupling compared to HOCs, abstracting complex stateful logic (like data fetching and error management) into reusable units without introducing the nesting and prop management complexities that lead to tangling.
Govern via Global Interceptors: Centralize technical operations, particularly observability, using global event handling mechanisms provided by modern application libraries (e.g., query cache callbacks). This eliminates the scattering of error logging and security checks across thousands of individual components, making system-wide policy changes manageable.
Mandate Dynamic Flow Visualization: Since CCCs are invisible on static diagrams, architects must mandate the use of Dynamic Diagrams (C4) or UML Sequence Diagrams. These diagrams should specifically trace the flow of critical CCCs (e.g., security validation or transaction logging) to ensure developers understand the system's operational behavior and to verify architectural conformance, thereby reducing the risk of technical debt related to quality concerns.
Prioritize Encapsulation to Reduce Cognitive Load: Recognizing that traditional N-Tier organization leads to high cognitive burden by requiring constant mental mapping between technical layers and business requirements, development effort must be focused on ensuring all code, especially business logic, adheres to high encapsulation standards facilitated by vertical, feature-based boundaries. The technical slices should provide a stable platform, allowing feature teams to focus purely on domain logic.

### Источники
1. Cross cutting concern example - Stack Overflow, дата последнего обращения: октября 29, 2025, https://stackoverflow.com/questions/23700540/cross-cutting-concern-example
2. Cross-cutting concern - Wikipedia, дата последнего обращения: октября 29, 2025, https://en.wikipedia.org/wiki/Cross-cutting_concern
3. What are scattering and tangling in aop - Stack Overflow, дата последнего обращения: октября 29, 2025, https://stackoverflow.com/questions/37547695/what-are-scattering-and-tangling-in-aop
4. Detecting Scattered and Tangled Quality Concerns in Source Code to Aid Maintenance and Evolution Tasks - ResearchGate, дата последнего обращения: октября 29, 2025, https://www.researchgate.net/publication/369013476_Detecting_Scattered_and_Tangled_Quality_Concerns_in_Source_Code_to_Aid_Maintenance_and_Evolution_Tasks
5. Higher-Order Components - React, дата последнего обращения: октября 29, 2025, https://legacy.reactjs.org/docs/higher-order-components.html
6. Custom React JS Hooks: What Are They and When to Use Them? | Turing, дата последнего обращения: октября 29, 2025, https://www.turing.com/blog/custom-react-js-hooks-how-to-use
7. Microservices Killer: Modular Monolithic Architecture | by Mehmet Ozkaya - Medium, дата последнего обращения: октября 29, 2025, https://medium.com/design-microservices-architecture-with-patterns/microservices-killer-modular-monolithic-architecture-ac83814f6862
8. Organizing Large Frontends The LIFT vs. Feature-Sliced Design Dilemma - Leapcell, дата последнего обращения: октября 29, 2025, https://leapcell.io/blog/organizing-large-frontends-the-lift-vs-feature-sliced-design-dilemma
9. Overview | Feature-Sliced Design - GitHub Pages, дата последнего обращения: октября 29, 2025, https://feature-sliced.github.io/documentation/docs/get-started/overview
10. Feature-Sliced Design and good frontend architecture - codecentric AG, дата последнего обращения: октября 29, 2025, https://www.codecentric.de/en/knowledge-hub/blog/feature-sliced-design-and-good-frontend-architecture
11. Project Architecture - Do you organize by "Features" ? : r/dotnet - Reddit, дата последнего обращения: октября 29, 2025, https://www.reddit.com/r/dotnet/comments/124to6h/project_architecture_do_you_organize_by_features/
12. Modular Monoliths: Have we come full circle? - DEV Community, дата последнего обращения: октября 29, 2025, https://dev.to/infoxicator/modular-monoliths-have-we-come-full-circle-o4f
13. Strategies for Handling Large-Scale Frontend Applications - DEV ..., дата последнего обращения: октября 29, 2025, https://dev.to/hasunnilupul/strategies-for-handling-large-scale-frontend-applications-5ffl
14. Cognitive Load in Engineering Teams: The Hidden Barrier to Scale | by Sangram | Medium, дата последнего обращения: октября 29, 2025, https://medium.com/@Sangram.s_16290/cognitive-load-in-engineering-teams-the-hidden-barrier-to-scale-b48ceaba859f
15. Cognitive load is what matters - GitHub, дата последнего обращения: октября 29, 2025, https://github.com/zakirullin/cognitive-load
16. How to create a scalable and maintainable front-end architecture ..., дата последнего обращения: октября 29, 2025, https://www.ronaldjamesgroup.com/blog/how-to-create-a-scalable-and-maintainable-front-end-architecture-by-kevin-pennekamp
17. How to use React higher-order components - LogRocket Blog, дата последнего обращения: октября 29, 2025, https://blog.logrocket.com/react-higher-order-components/
18. Reusing Logic with Custom Hooks - React, дата последнего обращения: октября 29, 2025, https://react.dev/learn/reusing-logic-with-custom-hooks
19. Error Handling in Frontend Development - DEV Community, дата последнего обращения: октября 29, 2025, https://dev.to/drishti1920/error-handling-in-frontend-development-dik
20. How to globally handle error for all react queries at once and refetch? - Stack Overflow, дата последнего обращения: октября 29, 2025, https://stackoverflow.com/questions/75869683/how-to-globally-handle-error-for-all-react-queries-at-once-and-refetch
21. Design patterns: exception / error handling - Stack Overflow, дата последнего обращения: октября 29, 2025, https://stackoverflow.com/questions/15542608/design-patterns-exception-error-handling
22. Developing Scalable Frontends with Feature-Sliced Design (FSD) - Bits and Pieces, дата последнего обращения: октября 29, 2025, https://blog.bitsrc.io/developing-scalable-frontends-with-feature-sliced-design-fsd-a2e5aa33d02c
23. Managing State - React, дата последнего обращения: октября 29, 2025, https://react.dev/learn/managing-state
24. State Management in React vs. Angular - Telerik.com, дата последнего обращения: октября 29, 2025, https://www.telerik.com/blogs/state-management-react-vs-angular
25. Scalable and Maintainable Frontend Advices? - Reddit, дата последнего обращения: октября 29, 2025, https://www.reddit.com/r/Frontend/comments/1jm78jq/scalable_and_maintainable_frontend_advices/
26. C4 model | Lightweight standard for visualizing software architecture, дата последнего обращения: октября 29, 2025, https://c4model.info/
27. Visualizing software architecture with the C4 model | by IcePanel - Medium, дата последнего обращения: октября 29, 2025, https://icepanel.medium.com/visualizing-software-architecture-with-the-c4-model-9255025c70b2
28. Dynamic diagram | C4 model, дата последнего обращения: октября 29, 2025, https://c4model.com/diagrams/dynamic
29. Sequence Diagram Tutorial - Complete Guide with Examples - Creately, дата последнего обращения: октября 29, 2025, https://creately.com/guides/sequence-diagram-tutorial/
30. Dynamic diagrams in the C4 model - IcePanel, дата последнего обращения: октября 29, 2025, https://icepanel.io/blog/2024-09-12-dynamic-diagrams-c4-model
31. How to create Sequence Diagrams - IONOS, дата последнего обращения: октября 29, 2025, https://www.ionos.com/digitalguide/websites/web-development/sequence-diagrams/
