---
**Title:** Spec Maintenance Guidelines
**Purpose:** To define the process for keeping specs synchronized with their corresponding documents.
**Audience:** All Developers, Architects, Documentation Maintainers
---

# Spec Maintenance Guidelines

## Overview

Specs in this directory define the structure and content requirements for their corresponding documents. **Specs must be kept in sync with documents** to ensure they remain accurate blueprints.

## Maintenance Process

### When Updating a Document

Whenever you modify a document (e.g., `03a-the-customer-step.md`), you **must** also update its corresponding spec (e.g., `specs/03a-the-customer-step.spec.md`):

1. **Review document changes:**
   - What new content was added?
   - What content was removed?
   - What content was modified?

2. **Update the spec:**
   - Add new mandatory content sections if new topics were added
   - Remove or update sections if content was removed or changed
   - Update verification criteria if new understanding goals were introduced
   - Ensure all mandatory content sections match what's in the document

3. **Verify alignment:**
   - Every major section in the document should have a corresponding section in the spec
   - Every mandatory content item in the spec should be present in the document
   - Verification criteria should reflect all learning objectives

### Spec Update Checklist

When updating a spec, verify:

- [ ] All new document sections are reflected in the spec's "Mandatory Content"
- [ ] All removed document sections are removed from the spec
- [ ] Modified content descriptions match the current document
- [ ] Verification criteria include all new understanding goals
- [ ] Diagram requirements match what's in the document
- [ ] Prohibited content list is still accurate

## Spec Structure

Each spec follows this structure:

1. **Objective:** What the document must achieve
2. **Rationale:** Why this document is needed
3. **Core Components:** Required top-level sections
4. **Mandatory Content:** Detailed requirements for each section
5. **Prohibited Content:** What should NOT be included
6. **Mandatory Diagrams:** Required visual representations
7. **Verification Criteria:** How to know the document is complete
8. **Maintenance Note:** Reminder to keep spec in sync (if applicable)

## Best Practices

1. **Update spec first, then document:** When planning changes, update the spec first to define requirements, then implement in the document
2. **Review both together:** When reviewing a document change, always review the spec alongside it
3. **Use spec as contract:** The spec is the contract for what the document should contain—if the document doesn't match, either update the document or update the spec
4. **Document exceptions:** If you intentionally deviate from the spec, document why in both the spec and the document

## Automation

While manual updates are required, you can use these techniques to stay in sync:

- **Before committing:** Review both spec and document files together
- **Code review:** Reviewers should check that spec and document are aligned
- **Regular audits:** Periodically review all spec/document pairs for consistency

---

**Remember:** A spec that doesn't match its document is worse than no spec at all—it creates confusion and false expectations. Keep them synchronized.

