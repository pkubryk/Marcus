# Marcus — Development Methodology Enforcement

Marcus is a reusable `.kiro/` directory that enforces a spec-driven development methodology across any project workspace. Clone it as `.kiro/` in any workspace to get consistent steering, hooks, and review agents.

## Setup

```bash
# As a submodule (recommended — pins version per project)
git submodule add https://github.com/pkubryk/Marcus.git .kiro

# Or as a direct clone
git clone https://github.com/pkubryk/Marcus.git .kiro
```

## Structure

```
.kiro/
├── steering/                        # Passive context — always injected
│   ├── methodology.md               # Workflow, three-layer spec model, review integration rules
│   ├── quality.md                    # Coding standards, type safety, testing requirements
│   └── no-documentation.md          # Prevents unnecessary summary/report generation
│
├── hooks/                           # Active enforcement — fires on events
│   ├── enforce-structure.kiro.hook       # Gate: validates layout, spec placement, Layer 1→3, spec-before-code
│   ├── doc-drift-check.kiro.hook         # Auto: detects stale docs/specs/READMEs after every session
│   ├── coverage-check.kiro.hook          # Auto: runs pytest --cov after each task
│   ├── readme-freshness.kiro.hook        # Auto: checks README accuracy after each task
│   ├── architecture-review.kiro.hook     # On-demand: senior architect review
│   ├── product-review.kiro.hook          # On-demand: senior PM review
│   ├── security-review.kiro.hook         # On-demand: security audit
│   ├── accessibility-review.kiro.hook    # On-demand: accessibility audit
│   ├── analytics-review.kiro.hook        # On-demand: analytics instrumentation check
│   ├── hygiene-audit.kiro.hook           # On-demand: project hygiene audit
│   └── consistency-audit.kiro.hook       # On-demand: Marcus internal consistency check
│
└── checklists/                      # Review content — referenced by hooks and tasks
    ├── architecture-review.md       # Technical soundness, alternatives, failure modes
    ├── product-review.md            # Problem validation, user value, scope, UX
    ├── security.md                  # Auth, input validation, data protection, API security
    ├── accessibility.md             # Semantic markup, contrast, touch targets, screen readers
    ├── analytics.md                 # Event tracking, privacy compliance, metrics
    ├── hygiene.md                   # Dead code, naming, orphaned files, dependencies
    └── consistency.md               # Internal consistency between Marcus docs and files
```

## Three-Layer Specification Model

```
{package}/
├── docs/                            # Layer 1: high-level specs
│   └── {feature-name}/              # One directory per feature
│       └── spec.md                  # Context, vision, domain knowledge
├── .kiro/
│   ├── steering/                    # Layer 2: module-specific conventions
│   └── specs/                       # Layer 3: implementation-ready specs
│       └── {feature-name}/          # Mirrors docs/{feature-name}/
│           ├── requirements.md
│           ├── design.md
│           └── tasks.md
├── {source_package}/
├── tests/
└── pyproject.toml
```

Layer 1 and Layer 3 use the same feature name. The mapping is 1:1 — `docs/{feature}/` is the high-level foundation, `.kiro/specs/{feature}/` is the implementation contract.

## Workflow

```
1. Draft high-level spec (Layer 1)     → {package}/docs/{feature}/spec.md
2. Run Architecture Challenger          ← userTriggered hook
3. Run Product Challenger               ← userTriggered hook
4. Iterate until satisfied
5. Create implementation spec (Layer 3) ← requirements → design → tasks
6. Review each step (you are sole reviewer)
7. Execute tasks from spec
8. Commit spec + code together
```

## Agents (userTriggered Hooks)

| Agent | Role | When to Trigger | Checklist |
|-------|------|-----------------|-----------|
| Architecture Challenger | Senior architect — challenges technical decisions, proposes alternatives, finds failure modes | After drafting a high-level spec or design | `checklists/architecture-review.md` |
| Product Challenger | Senior PM — challenges product decisions, validates user value, questions scope | After drafting a high-level spec or design | `checklists/product-review.md` |
| Security Review | Security auditor — evaluates auth, input validation, data protection, API security | After feature implementation is complete, before PR | `checklists/security.md` |
| Accessibility Review | Accessibility specialist — evaluates semantic markup, contrast, keyboard nav, screen readers | After UI implementation is complete (skip for non-UI) | `checklists/accessibility.md` |
| Analytics Review | Analytics analyst — evaluates event tracking, funnel instrumentation, privacy compliance | After user-facing feature is complete (skip for backend-only) | `checklists/analytics.md` |
| Hygiene Audit | Project janitor — finds dead code, orphaned files, naming issues, stale docs, unused deps | Periodically, or before major releases | `checklists/hygiene.md` |
| Marcus Consistency Audit | Meta-auditor — validates that README, steering, hooks, and checklists are in sync | After modifying any Marcus file, or periodically | `checklists/consistency.md` |

**Primary trigger**: Review agents are automatically included as tasks in every `tasks.md` by the spec assistant (see methodology.md). They execute as part of the normal task sequence — no manual intervention needed.

**Secondary trigger**: All agents are also available as userTriggered hooks in the Agent Hooks panel for ad-hoc use outside of spec execution (e.g., periodic audits, pre-PR reviews, exploratory checks).

## Automatic Hooks (Gates and Checks)

| Hook | Trigger | Purpose |
|------|---------|---------|
| Enforce Structure | `fileCreated` | Validates package layout, rejects misplaced specs, checks Layer 1 exists before Layer 3, warns if code created without a spec. All checks on file creation only. |
| Doc Drift Check | `agentStop` | After every agent session, checks if code changes made specs, READMEs, or Layer 1 docs stale. Updates them if so. |
| Coverage Check | `postTaskExecution` | Runs `pytest --cov --cov-fail-under=80` after each task completes. Reports coverage per module. |
| README Freshness | `postTaskExecution` | Checks if package README accurately reflects changes made during task execution. |

These fire automatically — no manual trigger needed.

## Steering Files (Passive Context)

| File | Inclusion | Purpose |
|------|-----------|---------|
| `methodology.md` | Always | Spec-driven workflow, three-layer model, feature-scoped package layout, dialogue validation rule, review agent integration, ad-hoc drift checking |
| `quality.md` | Always | Type safety, error handling, code structure, testability, testing standards |
| `no-documentation.md` | Always | Prevents AI from creating summary docs, cleanup reports, or process documentation unless explicitly requested |

Steering is injected into every AI interaction automatically.

## Task List Integration

When generating a `tasks.md`, review tasks are automatically included based on the feature's nature:

- **Security Review** — ALWAYS included as a task
- **Accessibility Review** — included when the feature has UI components
- **Analytics Review** — included when the feature is user-facing

Review tasks reference the checklist files and instruct the agent to evaluate the code produced by the feature.

## Key Rules

1. **No spec, no code** — enforced by `enforce-structure` hook on file creation
2. **Layer 1 before Layer 3** — enforced by `enforce-structure` hook on file creation
3. **Specs in packages, not workspace root** — enforced by `enforce-structure` hook
4. **Feature directories match** — `docs/{feature}/` maps 1:1 to `.kiro/specs/{feature}/`
5. **Layer 1 specs require architecture diagrams** — Mermaid data flow and module structure diagrams are mandatory
6. **No spec without dialogue** — the AI must challenge, question, and validate before creating any spec document
7. **Reviews are mandatory in task lists** — Security always, Accessibility and Analytics conditionally
8. **No stale docs** — the `doc-drift-check` hook catches drift even from ad-hoc changes
9. **Specs and code commit together** — specs are the living source of truth

## Per-Project Customization

Marcus provides workspace-level enforcement. Individual packages add their own:
- `{package}/docs/{feature}/` — high-level specs (Layer 1)
- `{package}/.kiro/steering/` — module-specific conventions (Layer 2)
- `{package}/.kiro/specs/{feature}/` — implementation specs (Layer 3)

These are project-specific and NOT part of Marcus.
