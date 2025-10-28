---
**Title:** Glossary of Terms
**Purpose:** The definitive glossary for all technical terms, architectural concepts, and project-specific acronyms.
**Audience:** All Developers
**Maintenance:** Update as new terms or concepts are introduced.
---

# Glossary of Terms

### Architectural Domain
A high-level, strategic grouping of architectural concerns. Domains represent the "architect's view" of the system, providing a top-down map of its major components (e.g., Application Architecture, Quality Architecture).

### Architectural Pillar
A synonym for Architectural Slice. Refers to one of the fundamental, cross-cutting subsystems of the application.

### Architectural Slice
A concrete, developer-centric subsystem that cuts across multiple architectural domains. Slices represent the "developer's view" of the system's tangible components (e.g., State Management Slice, Testing Slice).

### CI/CD (Continuous Integration & Continuous Deployment)
The automated process for building, testing, and deploying the application. This is the core of the Delivery Architecture domain.

### Co-location
A code organization pattern where related files for a single component (e.g., component logic `.tsx`, styles `.scss`, and tests `.test.tsx`) are kept together in the same directory.

### Defense in Depth
A security strategy where multiple layers of security controls are implemented throughout the system. This is a core pattern of the Quality Architecture domain.

### HOC (Higher-Order Component)
An advanced React pattern for reusing component logic. A HOC is a function that takes a component and returns a new, enhanced component. This is one of the layers of the State Management model in this project.

### IoC (Inversion of Control)
An architectural pattern where the core application is decoupled from the specific logic of its extensions (especially payment methods). The core application defines a contract, and extensions "register" their own logic to be called at the appropriate time. This is the key pattern of the Integration Architecture domain.

### Monorepo
A software development strategy where code for many projects is stored in the same repository. This project is a feature-based monorepo, with the core application and its integrations managed as separate packages within a single repository.

### SDK (Software Development Kit)
A collection of software development tools in one installable package. In this project, it primarily refers to the **BigCommerce Checkout SDK**, which is the core tool for managing all checkout state.
