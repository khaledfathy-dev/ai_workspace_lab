---
name: review-story-task
description: Optionally provide an advisory AI review of one implemented Task against its plan and tests; developer review remains the approval authority.
---

# Review Story Task Skill

## 1. Objective

When explicitly requested, provide an evidence-based advisory review of one implemented Task against its approved requirements and applicable quality standards.

This review is optional and non-authoritative. It does not replace or delay developer review, set Task status, or approve implementation.

This skill is reusable across programming languages, frameworks, architectures, and AI agents.

## 2. Required Context

Load only the context relevant to the selected Task:

1. Read `AGENTS.md` and `README.md`.
2. Read `development-guidelines/ai-workflow.md`.
3. Read `ai/current-state.md` and `ai/project-context.md`.
4. Read applicable development guidelines.
5. Read the active story's `user-story.md`.
6. Read its approved `implementation-plan.md`.
7. Read the selected task's implementation record.
8. Inspect the actual code changes, including the relevant diff.
9. Read relevant tests and prior developer feedback or advisory reports, if any.

Do not rely solely on the implementer's summary.

## 3. Scope and Preconditions

Verify that:

- The implementation plan was approved.
- The selected Task exists in the plan.
- The Task has been implemented and its changes can be identified.
- The implementation report and actual changes are available.

Review only the selected Task and relevant dependencies. No separate agent, isolated reviewer context, or source-hash baseline is required.

If the actual changes cannot be identified reliably, stop and request the missing information.

## 4. Review Criteria

Evaluate the implementation against the following criteria where applicable.

### A. Requirements and Correctness

- Does the implementation satisfy the task's objective?
- Are the relevant acceptance criteria addressed?
- Are edge cases and failure conditions handled?
- Does the implementation preserve expected existing behavior?

### B. Architecture and Maintainability

- Does the change respect the established architecture and component boundaries?
- Does it follow existing project conventions and applicable guidelines?
- Is the code understandable, cohesive, and appropriately reusable?
- Is there unnecessary duplication, complexity, or unrelated scope?

### C. Security and Data Integrity

- Are authentication and authorization requirements respected?
- Is input validation appropriate?
- Are sensitive values and data handled safely?
- Are transactions, concurrency, and data consistency handled correctly where relevant?
- Are errors and external inputs handled without exposing sensitive information?

### D. Testing and Verification

- Are relevant tests present and meaningful?
- Do tests cover important behavior and failure cases?
- Were the required verification steps executed?
- Do the reported results match the available evidence?
- Do missing tests or unexecuted checks warrant a developer request for changes?

### E. Compatibility and Operational Concerns

Where relevant, evaluate API compatibility, database migrations, configuration, performance, logging, observability, and deployment implications.

Do not report a concern as a confirmed defect unless the evidence supports that conclusion.

## 5. Findings

For every actionable finding, record:

- **Finding ID:** A stable identifier, such as `REV-001`.
- **Severity:** Critical, High, Medium, or Low.
- **Location:** File and relevant code section.
- **Category:** Correctness, security, architecture, testing, maintainability, or another relevant category.
- **Evidence:** The specific behavior or code that supports the finding.
- **Impact:** Why the issue matters.
- **Required action:** What needs to change.

Prioritize findings that affect correctness, security, data integrity, or acceptance criteria.

Distinguish confirmed defects from risks, recommendations, and questions.

Do not inflate severity or report stylistic preferences as defects.

## 6. Review Outcome

Use the following rules:

### Advisory review

Report `Advisory findings` or `No advisory findings`. If essential evidence is unavailable, report `Review limited` and explain why. Do not assign an approval state or imply that developer approval has occurred.

## 7. Restrictions

- Do not modify application source code.
- Do not fix findings.
- Do not silently change the implementation plan.
- Do not set or change Task or Story approval status.
- Do not claim tests passed without evidence.
- Do not implement additional Tasks.
- Do not commit, push, merge, or deploy.

The review is advisory; no separate reviewer context is required. Be candid about implementation risks and evidence limits.

## 8. Save the Review Report

When the user requests a saved report, use a Task-specific report name, for example:

`review-task-01.md`

Use the actual Task identifier and preserve previous developer feedback and reports. If a report already exists, create a versioned report or append a dated section instead of replacing earlier findings.

The report must include:

- Story and task identifiers.
- Review date.
- Scope reviewed.
- Files and evidence inspected.
- Verification evidence.
- Findings with severity and required actions.
- Overall outcome.
- Remaining risks and limitations.

## 9. Workflow State

Do not update Task approval status in the plan or `ai/current-state.md`. The developer records approval or requested changes after reviewing the implementation and any advisory report.

## 10. Completion Report

Report:

- The story and Task reviewed.
- The advisory outcome and evidence limitations.
- Findings by severity.
- Verification evidence and missing checks.
- Review report location.
- Required follow-up actions.

Then return the advisory findings to the developer. Do not approve the Task or begin fixes unless separately instructed.