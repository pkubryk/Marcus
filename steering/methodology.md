---
inclusion: always
---

# Development Methodology

## Core Principle

Specs drive code. No implementation without a specification. AI handles the "how" from well-defined behavioral contracts; developers own intent, architecture, and domain decisions.

## Three-Layer Specification Model

Every package in this workspace MUST maintain three layers of documentation:

### Layer 1: High-Level Specs (package/docs/{feature}/)
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
│   └── {feature-name}/           # One directory per feature
│       └── spec.md               # Context, vision, domain knowledge
├── .kiro/
│   ├── steering/                 # Layer 2: module-specific conventions
│   └── specs/                    # Layer 3: implementation-ready specs
│       └── {feature-name}/       # Mirrors docs/{feature-name}/
│           ├── requirements.md
│           ├── design.md
│           └── tasks.md
├── {source_package}/             # Source code
├── tests/
└── pyproject.toml
```

## RULE: Spec Placement

Spec files (requirements.md, design.md, tasks.md) MUST be created inside the
package they belong to, at `{package-name}/.kiro/specs/{feature-name}/`.

Specs MUST NEVER be created at the workspace-root `.kiro/specs/` directory. The
workspace-root `.kiro/` is reserved for workspace-level steering, hooks, checklists,
and settings only.

When using Kiro's spec assistant, always verify the target path before creating
any spec document. If the spec workflow defaults to `.kiro/specs/`, override it
to the correct package path.

## RULE: Layer 1 Before Layer 3

No Layer 3 implementation spec (requirements.md, design.md, tasks.md in
`{package}/.kiro/specs/{feature}/`) may be created until a Layer 1 high-level
spec exists at `{package}/docs/{feature}/`.

Layer 1 and Layer 3 use the same feature name. The mapping is 1:1:
- `{package}/docs/{feature-name}/spec.md` → high-level context and vision
- `{package}/.kiro/specs/{feature-name}/` → implementation-ready contracts

The high-level spec captures context, vision, and domain knowledge. It is the
foundation that Layer 3 specs refine into implementation-ready contracts. Skipping
Layer 1 produces Layer 3 specs that lack grounding and architectural coherence.

The workflow is:
1. Write Layer 1 high-level spec in `{package}/docs/{feature-name}/`
2. Run Architecture Challenger and Product Challenger reviews
3. Iterate until satisfied
4. Only then create Layer 3 specs in `{package}/.kiro/specs/{feature-name}/`

This is enforced by the `layer1-before-layer3` hook.

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
- Layer 1 high-level specs in docs/{feature}/
- Layer 3 specialized specs in .kiro/specs/{feature}/
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
direct prompting), you MUST still consider reviews AND documentation drift:

1. After completing the requested change, evaluate whether the change touches
   security-sensitive code (auth, input handling, data access, API endpoints).
   If yes, read .kiro/checklists/security.md and flag any concerns.
2. If the change involves UI components, read .kiro/checklists/accessibility.md
   and flag any concerns.
3. You do NOT need to run a full formal review — just flag concrete issues you
   notice based on the checklists.
4. **Documentation drift**: Check whether the change invalidates or makes stale
   any of the following:
   - The package's README.md
   - Layer 1 specs in `{package}/docs/{feature}/`
   - Layer 3 specs in `{package}/.kiro/specs/{feature}/` (requirements.md, design.md)
   - The workspace-level `.kiro/README.md` (if `.kiro/` files were changed)
   If any artifact is stale, update it as part of the change. Do not leave
   documentation out of sync, even for a one-line fix.

This is also enforced by the `doc-drift-check` hook which fires after every
agent execution.

### RULE: userTriggered Hooks

The following userTriggered hooks are available for deep on-demand reviews outside of task execution. Users can trigger them from the Agent Hooks panel:

- Security Review hook — performs a full security audit of the current feature
- Accessibility Review hook — performs a full a11y audit of UI components
- Analytics Review hook — evaluates analytics instrumentation completeness

These are complementary to the task-integrated reviews. Use them for:
- Post-feature comprehensive audits
- Pre-PR review gates
- Periodic codebase-wide reviews
