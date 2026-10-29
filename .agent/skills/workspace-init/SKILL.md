---
name: workspace-init
description: Analyze an existing software project and initialize its persistent project context and architecture documentation without changing application source code.
---

# Workspace Initialization Skill

## 1. Objective

Inspect the selected software project and create or update the persistent project documentation required for future AI-assisted development.

This skill is reusable across programming languages, frameworks, project types, and AI agents.

Do not assume a particular architecture, technology stack, repository layout, or development tool.

## 2. Required Context

Before starting, load the workspace and active-project context needed for this initialization:

1. Read `AGENTS.md`.
2. Read `README.md`.
3. Read `development-guidelines/ai-workflow.md`.
4. Read `ai/current-state.md`.
5. Read `ai/project-context.md`.
6. Identify and read only the development guidelines applicable to the selected project.

If a required file does not exist, record that fact and continue only when doing so is safe.

## 3. Identify the Target Project

1. Inspect the directories under `code/`.
2. Identify the project specified by the user or recorded in the current workflow state.
3. If there are multiple plausible projects and the intended target is unclear, ask the user before proceeding.
4. Record the actual project-relative path. Do not use a wildcard as the selected project path.
5. Do not rename, move, or restructure the source project.

## 4. Analyze the Project

Inspect the existing source code, documentation, configuration, tests, and build files relevant to the selected project. Do not repeatedly reload unrelated story records or guidelines.

Analyze:

- Project purpose and business domain.
- Programming languages, frameworks, and major dependencies.
- Repository and directory structure.
- Architectural style and application boundaries.
- Major modules and their responsibilities.
- Database, persistence, and data ownership.
- External APIs, services, and integrations.
- Authentication, authorization, and security mechanisms.
- Logging, observability, caching, and messaging where applicable.
- Build, test, and deployment processes.
- Existing coding conventions and testing practices.
- Known limitations and technical debt supported by evidence.

Use evidence from the actual project. Distinguish verified facts from assumptions and unresolved questions.

Do not infer that a technology or architectural pattern is used merely because it is mentioned in a guideline.

## 5. Create or Update Analysis Documents

Use the following directory:

`ai/project-analysis/`

Create or update these documents when relevant:

- `project-overview.md` — project purpose, scope, technology stack, and structure.
- `architecture.md` — architecture, components, boundaries, dependencies, and data flow.
- `modules.md` — major modules and their responsibilities.
- `database.md` — persistence, data model, migrations, and data ownership.
- `integrations.md` — external systems, APIs, and integration behavior.

Adapt the level of detail to the project. Do not invent information to fill a document.

Create additional analysis documents only when they provide meaningful value.

## 6. Update Persistent Context

Update `ai/project-context.md` with a concise summary of verified project facts.

Update `ai/current-state.md` with:

- The selected project path.
- The initialization status.
- The workflow stage.
- The next recommended action.
- Any blockers or unresolved questions.

Preserve valid existing decisions and history. Do not overwrite unrelated information.

## 7. Restrictions

- Do not modify application source code.
- Do not implement features or user stories.
- Do not create implementation plans for individual stories as part of this operation.
- Do not modify database schemas or production configuration.
- Do not install packages or change dependencies.
- Do not execute destructive commands.
- Do not expose secrets or copy credentials into documentation.
- Do not claim that builds or tests passed unless they were actually executed and passed.

Reading source files and inspecting existing configuration is permitted.

## 8. Verification

Before finishing:

1. Verify that the analysis documents exist at the intended locations.
2. Check that documented technologies and architectural claims have supporting evidence.
3. Confirm that unknowns are clearly identified.
4. Confirm that the selected project path is correct.
5. Confirm that application source code has not been modified.
6. Update the persistent workflow state.

## 9. Completion Report

Report:

- Selected project and source path.
- Documents created or updated.
- Key architectural and technical findings.
- Important unknowns and blockers.
- Verification performed.
- Confirmation that no application source code was modified.
- Recommended next action.

Do not automatically start another workflow stage. Stop and wait for the user's instruction.

## Interactive Clarification and Decision Recording

After inspecting the project and producing the initial analysis:

1. Review the analysis documents and identify unresolved questions that materially affect the business scope, architecture, security, data model, integrations, or implementation decisions.
2. Separate questions that can be answered by inspecting the repository from questions that require user or business input. Do not ask the user to confirm facts that can be verified from the source code.
3. Ask the user one clear, focused question at a time through the current conversation. Explain briefly why the answer matters and provide options when they help the user decide.
4. Wait for the user's answer before asking the next question. Do not invent answers, assume approval, or ask all questions in a single message.
5. If the user does not know the answer, record it as an open decision. Recommend a default only when supported by repository evidence or established project constraints.
6. After receiving an answer, update the relevant analysis document and `ai/project-context.md`. Update `ai/current-state.md` to reflect remaining questions and decisions.
7. Distinguish confirmed user decisions, verified repository facts, assumptions, and unresolved questions.
8. Do not modify application source code, implement features, install packages, or change project configuration during this process.
9. Continue until all material questions are answered or explicitly recorded as unresolved. Then summarize the decisions, remaining uncertainties, and recommended next action.
10. Stop and wait for the user's next instruction. Do not automatically invoke story planning or implementation.