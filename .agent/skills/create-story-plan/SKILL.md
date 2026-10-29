---
name: create-story-plan
description: Analyze a user story and project evidence, then create an ordered Task-based implementation plan without changing application code.
---

# Create Story Plan Skill

## 1. Objective

Create a practical, traceable implementation plan for a user story based on its requirements, acceptance criteria, actual project structure, and applicable development guidelines.

The skill must work across programming languages, frameworks, architectures, and AI agents.

## 2. Required Context

Before planning:

1. Read `AGENTS.md`.
2. Read `README.md`.
3. Read `development-guidelines/ai-workflow.md`.
4. Read `ai/current-state.md`.
5. Read `ai/project-context.md`.
6. Read relevant documents in `ai/project-analysis/`.
7. Read applicable development guidelines.
8. Read the selected story's requirements, if already saved.

Inspect the actual source code relevant to the story. Do not rely solely on project summaries.

## 3. Identify the Story

The user may provide a story directly in the conversation or as a file.

- If the story is provided in the conversation, save a copy to the appropriate story directory.
- If a story file already exists, read it and preserve its requirements.
- If the story's identity or acceptance criteria are insufficient to plan safely, ask the user for clarification.
- Do not invent business requirements or silently change acceptance criteria.

Use this directory structure:

`ai/stories/sprint-XX/user-story-XXX/`

Use the actual sprint and story identifiers when available. If they are unavailable, ask for clarification or use a clearly documented provisional identifier when the workflow permits it.

The story directory should contain:

- `user-story.md`
- `implementation-plan.md`

## 4. Analyze the Existing Implementation

Inspect relevant:

- Modules, components, and service boundaries.
- Existing implementations and reusable patterns.
- APIs, contracts, and integrations.
- Data models, persistence, and migrations.
- Validation, authorization, and error handling.
- Tests and test infrastructure.
- Configuration and deployment implications.

Identify dependencies and affected components before proposing changes.

Follow the existing architecture and project conventions unless the requirements explicitly call for a change. Document justified deviations.

## 5. Build the Implementation Plan

Create the fewest practical, ordered implementation Tasks that preserve coherent scope, developer control, dependencies, and reviewability. A Task is a bounded unit of related technical work, not an individual file, method, DTO, or arbitrary coding step. Group production changes and their related tests in the same Task when they form one coherent unit. Do not force a fixed Task count.

For each task, document:

1. **Task ID and title**
2. **Objective and bounded scope**
3. **Requirements and acceptance criteria addressed**
4. **Affected areas/components**, and likely files when known
5. **Expected changes and implementation approach**
6. **Relevant development guidelines**
7. **Dependencies and execution order**
8. **Tests and verification steps**
9. **Completion criteria**
10. **Security, data-integrity, compatibility risks and mitigations**, where relevant
11. **Effort estimate**, only when repository evidence supports a reasonable estimate; otherwise state `Not estimated`
12. **Initial status:** `Not Started`
13. **Developer review decision and feedback**, to be recorded during implementation

Each Task should have a clear, cohesive scope and a verifiable outcome. Make the Task substantial enough to avoid micro-planning and small enough for focused implementation and developer review.

Keep Tasks cohesive and bounded for controlled implementation and developer review. Avoid both excessively broad Tasks and unnecessary micro-Tasks.

Order tasks according to dependencies. Identify parallelizable work only when dependencies and shared-file conflicts permit it.

## 6. Define the Verification Strategy

Specify appropriate checks for the project and story, such as:

- Unit tests.
- Integration tests.
- API or contract tests.
- UI tests.
- Security and authorization checks.
- Database migration validation.
- Build and static analysis.
- Regression tests.

Select checks according to the actual project and the story's risk. Do not prescribe tools that the project does not use without documenting the proposal.

Distinguish required checks from optional recommendations.

## 7. Handle Unknowns and Risks

Document:

- Ambiguous requirements.
- Missing dependencies or information.
- Compatibility concerns.
- Data migration or integration risks.
- Security implications.
- Assumptions requiring confirmation.

Ask the user when an unresolved issue materially affects scope, safety, architecture, or the correctness of the plan.

Do not hide uncertainty behind invented details.

## 8. Restrictions

- Do not modify application source code.
- Do not implement any Task.
- Do not change project dependencies or configuration.
- Do not modify database schemas.
- Do not install packages or run database migrations while planning.
- Do not commit, push, merge, deploy, or create a pull request.
- Do not begin implementation without explicit user approval of the completed plan.
- Do not automatically proceed to the next workflow stage.

Writing or updating story documentation and the implementation plan is permitted.

## 9. Update Workflow State

After preparing the plan:

1. Save the implementation plan to the story directory.
2. Set the story status to `Plan Ready / Pending Approval` only after the Plan Quality Gate passes; otherwise retain or set an appropriate planning or blocked status and describe what prevents readiness.
3. Update `ai/current-state.md` with the active story, plan path, and pending approval.
4. Preserve previous decisions and reports.
5. Record blockers and unresolved questions.

Do not mark the plan approved unless the user explicitly approves it.

## 10. Completion Report

Report:

- Story identifier and location.
- Summary of the proposed solution.
- Number and order of implementation Tasks.
- Important dependencies and risks.
- Verification strategy.
- Unresolved questions.
- Actual plan status. Report `Plan Ready / Pending Approval` only if the Plan Quality Gate passes; otherwise identify the remaining blockers and do not request approval as though the plan were ready.

Then stop and wait for explicit user approval or requested revisions.

## Mandatory Solution-Oriented Planning Rules

Apply these rules to every confirmed technical issue, defect, architectural conflict, missing dependency, or runtime prerequisite that affects the story:

1. Investigate the issue in relevant source code, configuration, existing tests, and applicable development guidelines before selecting a solution. Confirmed facts must be based on inspected evidence.
2. Document the evidence, root cause, chosen solution, alternatives considered, affected files or components, implementation tasks, and verification criteria. If an item is not applicable or cannot yet be determined, state why rather than implying it was established.
3. Clearly distinguish verified facts, assumptions, recommendations, and unresolved questions. Never present an assumption as a verified fact.
4. Do not treat documenting a limitation, deviation, or risk as solving the underlying problem. A limitation may remain only when the plan explicitly explains its impact and provides a viable solution that satisfies the story, or when it is demonstrably outside the story's required scope.
5. Functional tests for a use case must exercise its real request handler. A test may bypass the handler only when its explicitly stated purpose is to test an isolated HTTP boundary, and it must not be presented as verification of the use case.
6. A persistence requirement must have a complete, verifiable strategy for database schema availability and save/reload behavior using the intended provider. In-memory tests alone do not establish SQL Server persistence or schema availability.
7. A proposed shared-infrastructure change must include regression tests for the affected behavior and an assessment of its other consumers, including evidence for the consumers identified.
8. Do not mark a plan `Plan Ready / Pending Approval` while any confirmed blocking technical issue or runtime prerequisite lacks a proposed, implementable solution. A documented blocker or request for later confirmation is not a substitute for that solution.
9. Ask the user only for decisions that genuinely require business intent or architectural judgment. Investigate technical questions from repository evidence; do not ask the user to answer questions that source code, configuration, tests, or guidelines can resolve.
10. Keep planning separate from implementation. Do not modify application source code, install packages, run database migrations, or implement any part of the story while producing the plan.

## Problem-Solving and Solution Design

Planning is not complete when problems, risks, or open questions have merely been listed. The agent must investigate each material issue and develop a proposed solution.

For every identified defect, architectural conflict, missing dependency, or implementation risk:

1. Inspect the relevant source code, configuration, tests, and applicable development guidelines.
2. Verify the problem and explain its impact on the requested story.
3. Evaluate reasonable solution alternatives, including the smallest safe change and the option of making no change when appropriate.
4. Recommend a concrete solution based on repository evidence, existing conventions, maintainability, security, and scope.
5. Explain the trade-offs and identify affected components, files, tests, and dependencies.
6. Incorporate the recommended solution into the implementation tasks, with explicit verification and regression criteria.
7. Keep unrelated defects out of scope unless they prevent the story from meeting its acceptance criteria. Record them as follow-up work.
8. Prefer existing project patterns and infrastructure over introducing new packages, services, abstractions, or configuration.
9. Do not treat a suspected defect as confirmed until it has been investigated.
10. Do not claim a solution is complete unless the plan covers all necessary runtime prerequisites, including database schema availability, dependency registration, configuration, and required tests.

## Decision Management

Classify decisions into these categories:

- **Verified technical fact:** Determine from repository evidence; do not ask the user to confirm it unnecessarily.
- **Recommended technical decision:** Choose the best-supported option and explain why. Ask the user only when approval is needed for a material architectural change or when project policy requires it.
- **Business decision:** Ask the user when the correct behavior depends on business intent that cannot be inferred reliably.
- **Unresolved prerequisite:** Explain why the plan cannot safely proceed without further information and propose the most useful next investigation.

Ask one focused question at a time when user input is necessary. Where possible, present a recommended option with concise alternatives.

## Plan Quality Gate

Before marking a plan as Plan Ready / Pending Approval, verify that:

- Every acceptance criterion maps to implementation work and verification.
- Every confirmed blocking defect has a proposed solution.
- Every implementation task has a clear objective, scope, dependencies, tests, and completion criteria.
- Required runtime prerequisites are addressed.
- Risks have mitigation strategies rather than descriptions alone.
- Unrelated work is separated from the story.
- Remaining open questions are explicitly identified, with a recommendation where possible.
- The plan is sufficiently detailed to implement one task at a time without requiring the implementer to redesign the solution.

If essential information is missing, continue the investigation or ask the user a focused question. Do not label an incomplete analysis as a final implementation plan.

## Create, Replan, and Update Existing Plans

The `/create-story-plan` skill supports both initial planning and replanning. An existing plan marked `Plan Ready / Pending Approval` is not a reason to refuse execution.

### 1. Detect the planning mode

Before planning, inspect the target story directory.

- **No implementation plan exists:** Create a new plan.
- **A plan exists and is not approved:** Read and evaluate the existing plan, investigate unresolved issues, and revise the existing plan in place.
- **A plan exists and is approved:** Do not overwrite or replace it silently. Ask the user for explicit permission to revise the approved plan.
- **A plan exists but its approval status is unclear:** Preserve it and clarify the status before making potentially destructive changes.

Do not create duplicate plan files merely to avoid updating an existing plan.
Set a plan version for a new plan. On each material revision, increment the version, preserve the previous version's change history and approval evidence, and retain/increment the revision-cycle counter for the current approval attempt. Reset that counter only when the developer explicitly starts a new approval attempt after escalation.

### 2. Replanning behavior

When revising an existing unapproved plan:

1. Read the user story, current plan, applicable development guidelines, relevant source code, configuration, and existing tests.
2. Evaluate every existing implementation task, decision, assumption, risk, and open question.
3. Identify incorrect decisions, unsupported assumptions, missing solutions, contradictions, incomplete dependencies, and missing verification steps.
4. Investigate technical questions using repository evidence before asking the user.
5. Design and document concrete, implementable solutions for confirmed problems.
6. Update the existing implementation plan to reflect the findings.
7. Remove resolved questions and outdated assumptions. Retain genuinely unresolved business decisions or prerequisites with a clear explanation and recommended next action.
8. Record meaningful changes in the plan's change history.
9. Keep the plan status as `Plan Ready / Pending Approval` only when the plan satisfies the Plan Quality Gate. Otherwise, explicitly mark it as requiring further investigation and explain why.

### 3. Replanning is not implementation

Replanning must not modify application source code, implement story tasks, install packages, run migrations, change runtime configuration, or perform other implementation actions.

The skill may inspect source code, configuration, and tests to establish facts and design the proposed solution.

### 4. Preserve approval boundaries

A plan being ready does not mean it is approved. Never begin implementation without explicit user approval.

When a previously approved plan must change, obtain permission before revising it and preserve a record of the prior approval and the reason for the change.

### 5. Replan completion report

After creating or revising a plan, report:

- Whether this was initial planning or replanning.
- Which sections or implementation tasks changed.
- Which problems were resolved through investigation and concrete design.
- Which genuine business decisions or blocking prerequisites remain.
- Whether the plan passes the Plan Quality Gate.
- The current approval status.

Do not claim that an issue is resolved unless the revised plan contains an evidence-based solution and appropriate verification criteria.