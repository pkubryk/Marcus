---
inclusion: always
---

# Development Methodology

## Core Principle

Specs drive code. No implementation without a specification. AI handles the "how" from well-defined behavioral contracts; developers own intent, architecture, and domain decisions.

## Three-Layer Specification Model

Every package in this workspace MUST maintain three layers of documentation:

### Layer 1: High-Level Specs (package/docs/)
- Context, vision, domain knowledge
- Written before implementation begins
- Shared discussion artifacts, reviewed by team
- Updated as understanding evolves

### Layer 2: Steering (package/.kiro/steering/)
- Module-specific architecture, conventions, persistent context
- Supplements this workspace-level steering
- Kept concise — injected into every AI interaction for that module

### Layer 3: Specialized Specs (package/.kiro/specs/{feature}/)
- Implementation-ready contracts: requirements.md → design.md → tasks.md
- Created at implementation time using Kiro's spec assistant
- Each document reviewed before proceeding to the next
- Committed alongside the code they produce

## Required Package Layout

Every package MUST follow this structure:

```
{package-name}/
├── docs/                         # Layer 1: high-level specs
├── .kiro/
│   ├── steering/                 # Layer 2: module-specific conventions
│   └── specs/                    # Layer 3: implementation-ready specs
│       └── {feature-name}/
│           ├── requirements.md
│           ├── design.md
│           └── tasks.md
├── {source_package}/             # Source code
├── tests/
└── pyproject.toml
```

## Workflow

1. Write or refine Layer 1 high-level spec (with AI as sounding board)
2. Run Architecture Challenger review (userTriggered hook)
3. Run Product Challenger review (userTriggered hook)
4. Iterate on spec based on both challengers' feedback until satisfied
5. Create Layer 3 specialized spec using Kiro's spec assistant
6. Review requirements → review design → review tasks (step by step, you are the sole reviewer)
7. Generate code from spec, supervised task by task
8. Commit spec + code together

## RULE: Specification Validation Through Dialogue

No specification document (requirements.md, design.md, or tasks.md) may be created
until sufficient alignment has been reached through conversation. The model MUST:

1. **Challenge the request**: Ask probing questions about intent, scope, edge cases,
   and non-goals. Do not accept the first description at face value.

2. **Explore alternatives**: Present competing approaches, existing solutions, and
   tradeoffs. Ask the user why their approach is better than alternatives.

3. **Clarify boundaries**: Identify what is in scope and what is explicitly out of
   scope. Push back on scope creep and vague requirements.

4. **Validate assumptions**: Surface implicit assumptions and verify them. Ask "what
   happens when..." for edge cases and failure modes.

5. **Confirm level of detail**: Ensure the specification is neither too abstract
   (hand-wavy) nor too prescriptive (over-engineered). The right level depends on
   the module — orchestration needs more context, isolated algorithms need precision.

6. **Reach explicit agreement**: Before creating any spec document, summarize the
   key decisions and ask the user to confirm. Do not proceed on silence or ambiguity.

A specification created without this validation process is likely to produce poor
code. The cost of a thorough conversation is always less than the cost of reworking
a bad implementation.

This rule applies to ALL specification layers:
- Layer 1 high-level specs in docs/
- Layer 3 specialized specs in .kiro/specs/
- Steering files in .kiro/steering/

The only exception is trivial fixes where the change is self-evident and a full
dialogue would be overhead.

## Context Rules

- Orchestration tasks (pipelines, integration) → provide broad project context
- Isolated algorithm tasks → specialized spec alone is sufficient
- Over-providing context degrades generation quality

## Ongoing Development

- Bugs: update or create spec capturing corrected behavior before fixing
- Refactors: spec describes target architecture and transformation steps
- Features: new or revised spec captures updated requirements
- Trivial fixes: direct prompting or manual edits are acceptable

## Review Agents — Mandatory Integration

Review checklists exist at .kiro/checklists/. These MUST be integrated into the
development workflow — both in spec-driven task lists and during ad-hoc coding.

### Available Reviews

| Review | Checklist | Applies When |
|--------|-----------|--------------|
| Security | .kiro/checklists/security.md | ALWAYS — every feature, every module |
| Accessibility | .kiro/checklists/accessibility.md | Feature involves UI (tsx, jsx, html, css, templates) |
| Analytics | .kiro/checklists/analytics.md | Feature involves user-facing interactions |

### RULE: Task List Generation

When generating a tasks.md file, you MUST:

1. Analyze the feature's design.md to determine which reviews apply
2. Include review tasks as explicit, numbered tasks in the task list
3. Place review tasks AFTER all implementation and test tasks
4. Each review task MUST instruct the agent to read the specific checklist file and evaluate the code produced by the feature

Security Review is ALWAYS included. Accessibility and Analytics are included based on the feature's nature.

Example task list structure:
```
- [ ] 1. Implement X (from design)
- [ ] 2. Implement Y (from design)
- [ ] 3. Write unit and integration tests
- [ ] 4. Security Review — read .kiro/checklists/security.md and evaluate all source files created or modified in this feature against the checklist. Report findings with file, issue, and fix.
- [ ] 5. Accessibility Review — read .kiro/checklists/accessibility.md and evaluate all UI components created or modified in this feature. Report findings with file, issue, and fix. (include only if feature has UI)
- [ ] 6. Analytics Review — read .kiro/checklists/analytics.md and evaluate analytics instrumentation needs for this feature. Report what events should be tracked and what is missing. (include only if feature is user-facing)
```

### RULE: Ad-Hoc / Vibe Coding

When implementing changes outside of a formal spec (trivial fixes, exploration,
direct prompting), you MUST still consider reviews:

1. After completing the requested change, evaluate whether the change touches security-sensitive code (auth, input handling, data access, API endpoints). If yes, read .kiro/checklists/security.md and flag any concerns.
2. If the change involves UI components, read .kiro/checklists/accessibility.md and flag any concerns.
3. You do NOT need to run a full formal review — just flag concrete issues you notice based on the checklists.

### RULE: userTriggered Hooks

The following userTriggered hooks are available for deep on-demand reviews outside of task execution. Users can trigger them from the Agent Hooks panel:

- Security Review hook — performs a full security audit of the current feature
- Accessibility Review hook — performs a full a11y audit of UI components
- Analytics Review hook — evaluates analytics instrumentation completeness

These are complementary to the task-integrated reviews. Use them for:
- Post-feature comprehensive audits
- Pre-PR review gates
- Periodic codebase-wide reviews
