# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a **Speckit** repository - a structured workflow system for feature development that separates specification, planning, and implementation into distinct phases. The system uses custom slash commands to guide development from initial feature description through to implementation.

## Workflow Commands

The Speckit workflow uses these slash commands in sequence:

### 1. `/speckit.specify [feature description]`
Creates a feature specification from natural language input.
- Generates a feature branch with format `[number]-[short-name]`
- Creates specification in `specs/[number]-[short-name]/spec.md`
- Focuses on WHAT and WHY (user needs, business value)
- Avoids HOW (no tech stack, implementation details)
- Validates spec quality with built-in checklist

### 2. `/speckit.clarify` (Optional)
Refines ambiguous or underspecified areas in the spec.
- Asks up to 5 targeted clarification questions
- Updates spec with answers incrementally
- Should run BEFORE `/speckit.plan` to reduce rework risk

### 3. `/speckit.plan`
Generates technical implementation plan from the specification.
- Creates `plan.md`, `research.md`, `data-model.md`, `quickstart.md`
- Creates API contracts in `contracts/` directory
- Defines tech stack, architecture, and project structure
- Updates agent-specific context files

### 4. `/speckit.tasks`
Breaks down the plan into actionable, dependency-ordered tasks.
- Generates `tasks.md` organized by user story
- Tasks follow strict checklist format: `- [ ] [TaskID] [P?] [Story?] Description with file path`
- Each user story becomes independently testable increment
- Identifies parallel execution opportunities

### 5. `/speckit.analyze` (Optional)
Performs cross-artifact consistency analysis.
- READ-ONLY validation of spec.md, plan.md, tasks.md
- Checks for duplications, ambiguities, coverage gaps
- Validates constitution compliance
- Reports findings with severity levels

### 6. `/speckit.implement`
Executes the implementation plan from tasks.md.
- Processes tasks phase-by-phase
- Respects dependencies and parallel execution markers
- Marks tasks as complete in tasks.md as they finish
- Validates checklists before starting

### 7. `/speckit.constitution`
Creates or updates the project constitution.
- Defines core principles and constraints
- All spec/plan work must comply with constitution rules
- Constitution violations are always CRITICAL

### 8. `/speckit.checklist [domain]`
Generates custom checklists for specific domains (UX, security, testing, etc.).

### 9. `/speckit.taskstoissues`
Converts tasks.md into GitHub issues with dependencies.

## Project Structure

```
.
├── .specify/
│   ├── memory/
│   │   └── constitution.md          # Project principles and constraints
│   ├── scripts/
│   │   └── bash/                    # Automation scripts
│   │       ├── create-new-feature.sh
│   │       ├── setup-plan.sh
│   │       ├── check-prerequisites.sh
│   │       └── update-agent-context.sh
│   └── templates/                   # Templates for specs, plans, tasks
│       ├── spec-template.md
│       ├── plan-template.md
│       ├── tasks-template.md
│       ├── checklist-template.md
│       └── agent-file-template.md
├── .claude/
│   └── commands/                    # Slash command definitions
│       ├── speckit.specify.md
│       ├── speckit.clarify.md
│       ├── speckit.plan.md
│       ├── speckit.tasks.md
│       ├── speckit.implement.md
│       ├── speckit.analyze.md
│       ├── speckit.constitution.md
│       ├── speckit.checklist.md
│       └── speckit.taskstoissues.md
└── specs/                           # Feature workspaces (created per feature)
    └── [###-feature-name]/
        ├── spec.md                  # Feature specification
        ├── plan.md                  # Implementation plan
        ├── tasks.md                 # Task breakdown
        ├── research.md              # Research findings
        ├── data-model.md            # Data entities
        ├── quickstart.md            # Integration scenarios
        ├── contracts/               # API contracts
        └── checklists/              # Quality checklists
```

## Key Concepts

### User Stories as First-Class Units
- Specifications organize requirements by user story with priorities (P1, P2, P3)
- Each user story must be independently testable
- Tasks are grouped by user story for incremental delivery
- Story label format: `[US1]`, `[US2]`, etc.

### Task Format Requirements
All tasks MUST follow this exact format:
```
- [ ] [TaskID] [P?] [Story?] Description with file path
```
- `TaskID`: Sequential (T001, T002, ...)
- `[P]`: Optional marker for parallelizable tasks
- `[Story]`: Required for story-phase tasks ([US1], [US2], etc.)
- Must include specific file path

### Clarification Strategy
- Make informed guesses based on context and best practices
- Document assumptions in spec
- Limit to maximum 3 `[NEEDS CLARIFICATION]` markers
- Only for critical decisions impacting scope/security/UX

### Success Criteria Guidelines
Success criteria must be:
1. Measurable (specific metrics: time, percentage, count)
2. Technology-agnostic (no frameworks, languages, tools)
3. User-focused (outcomes, not system internals)
4. Verifiable (testable without knowing implementation)

### Constitution Authority
- Constitution principles are NON-NEGOTIABLE
- Constitution violations are always CRITICAL severity
- Must be resolved before implementation
- Changes to constitution require explicit separate process

## Script Usage

All bash scripts support both standard bash and PowerShell syntax:

### Standard Usage
```bash
.specify/scripts/bash/create-new-feature.sh --json "$ARGUMENTS" --number 5 --short-name "user-auth" "Add user authentication"
```

### For Argument Quoting
- Single quotes with apostrophes: Use `'I'\''m Groot'` or `"I'm Groot"`
- Always quote paths with spaces: `cd "path with spaces/file.txt"`

### Key Scripts
- `create-new-feature.sh --json`: Creates feature branch and directory structure
- `setup-plan.sh --json`: Initializes planning phase
- `check-prerequisites.sh --json`: Validates prerequisites and returns paths
- `update-agent-context.sh claude`: Updates Claude-specific context

## Workflow Best Practices

### Starting New Features
1. Always run `/speckit.specify` with clear feature description
2. Review generated spec for quality
3. Run `/speckit.clarify` for any ambiguities
4. Only proceed to `/speckit.plan` after spec is solid

### Planning Phase
1. Ensure spec is complete before planning
2. Let research phase resolve all NEEDS CLARIFICATION markers
3. Update agent context files after plan completion

### Task Generation
1. Tasks must map to user stories from spec
2. Organize by priority: Setup → Foundational → User Stories (P1, P2, P3) → Polish
3. Mark independent tasks with `[P]` for parallel execution
4. Tests are optional unless explicitly requested

### Implementation Phase
1. Check all checklists before starting
2. Execute tasks phase-by-phase
3. Mark tasks complete (`[X]`) immediately after finishing
4. Respect dependencies: sequential tasks in order, parallel tasks together

## Quality Gates

### Specification Quality Checklist Items
- No implementation details (languages, frameworks, APIs)
- Focused on user value and business needs
- All mandatory sections completed
- No `[NEEDS CLARIFICATION]` markers remain
- Requirements are testable and unambiguous
- Success criteria are measurable and technology-agnostic

### Implementation Validation
- All required tasks completed
- Implemented features match specification
- Tests pass (if tests requested)
- Implementation follows technical plan
- Ignore files created/verified for tech stack

## Important Notes

- All paths in scripts must be absolute, not relative
- Scripts output JSON that must be parsed for file paths
- Only run `create-new-feature.sh` ONCE per feature
- Spec template placeholders must be replaced with concrete details
- Never modify artifacts during `/speckit.analyze` (read-only)
- Constitution conflicts are always CRITICAL priority
