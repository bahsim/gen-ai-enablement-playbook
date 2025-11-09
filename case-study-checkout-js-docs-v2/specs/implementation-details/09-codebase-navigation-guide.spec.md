---
**Title:** Spec for: Codebase Navigation Guide
**Purpose:** To define the structure and mandatory content for a scenario-based codebase navigation guide that helps developers quickly find exact files for common checkout changes.
**Audience:** Internal
---

# Spec: Codebase Navigation Guide

## 1. Document Purpose

The document `09-codebase-navigation-guide.md` must serve as a **quick reference guide** for developers to navigate the codebase by choosing a scenario and immediately seeing which files to investigate, in order.

## 2. Mandatory Sections

The document must contain the following sections:

### 1. Introduction
*   Must state that the guide is scenario-based
*   Must instruct users to pick their scenario to see files in investigation order

### 2. Customer Step Scenarios
*   Must include scenarios:
    *   Change Customer Step Completion Logic
    *   Change Customer View Types (Login/Guest/Create Account)
    *   Add/Modify Wallet Payment Integration
    *   Change Guest Checkout Flow
*   Each scenario must list files in investigation order (numbered list)
*   Each file path must be exact and verifiable

### 3. Shipping Step Scenarios
*   Must include scenarios:
    *   Change Shipping Step Completion Logic
    *   Modify Multi-Shipping Mode
    *   Change Promotional Items Handling
    *   Modify Custom Shipping Options
*   Each scenario must list files in investigation order (numbered list)

### 4. Billing Step Scenarios
*   Must include scenarios:
    *   Change Billing Step Completion Logic
    *   Modify Payment Method Detection
    *   Change Billing Address Handling
*   Each scenario must list files in investigation order (numbered list)

### 5. Payment Step Scenarios
*   Must include scenarios:
    *   Change Payment Step Completion Logic
    *   Modify Order Submission Flow
    *   Change Payment Method Registration
*   Each scenario must list files in investigation order (numbered list)

### 6. Cross-Step Scenarios
*   Must include scenarios:
    *   Change Step Editability Rules
    *   Modify Step Navigation/Progression
    *   Change Step Filtering (Required/Optional Steps)
    *   Modify Data Flow Between Steps
*   Each scenario must list files in investigation order (numbered list)

### 7. Orchestration Scenarios
*   Must include scenarios:
    *   Change Step Orchestration Logic
    *   Modify Step Lazy Loading
*   Each scenario must list files in investigation order (numbered list)

### 8. Entry Points
*   Must list application initialization files
*   Must include component hierarchy diagram (ASCII or text-based)

### 9. Quick File Reference by Category
*   Must organize files by category:
    *   Orchestration
    *   Customer step
    *   Shipping step
    *   Billing step
    *   Payment step
    *   Utilities
*   Each category must list relevant files with brief descriptions

### 10. Search Strategies
*   Must provide grep patterns for finding:
    *   Functions/components
    *   Data access patterns
    *   Test files
*   Must mention semantic search for understanding behavior

## 3. Principles

*   **Scenario-based:** All content organized by developer scenarios, not by file structure
*   **Exact file paths:** All file paths must be exact and verifiable (no placeholders)
*   **Investigation order:** Files listed in logical investigation order (high-level to low-level)
*   **Quick reference:** Must enable quick navigation without reading full architectural docs
*   **No implementation details:** Focus on file locations, not code explanations
*   **No fabrications:** Only include verified file paths from actual codebase

## 4. Mandatory Content

### File Path Format
*   All file paths must use forward slashes: `packages/core/src/app/...`
*   All file paths must include full path from workspace root
*   File paths must match actual codebase structure

### Scenario Format
*   Each scenario must have:
    *   Clear scenario title
    *   "Files to check (in order):" header
    *   Numbered list of files (1., 2., 3., etc.)
    *   Brief comment for each file explaining its relevance

### Component Hierarchy
*   Must show ASCII/text-based tree structure
*   Must show parent-child relationships
*   Must include all four step components (Customer, Shipping, Billing, Payment)

## 5. Verification Criteria

*   All file paths exist in codebase (or are verified patterns)
*   Investigation order is logical (orchestration → step → utilities)
*   Scenarios cover common change tasks
*   No duplicate scenarios
*   Search strategies use correct grep patterns
*   Component hierarchy matches actual structure

## 6. Maintenance Note

This document must be updated when:
*   File paths change
*   New common scenarios emerge
*   Component hierarchy changes
*   New utility files are added

