---
inclusion: always
---

# Quality Standards

## Core Principles

- No demo, mock, or temporary implementations
- No hardcoded values or placeholder data
- No TODO comments without an immediate implementation plan
- All business logic and data persistence on the server side
- Specs and code committed together — specs are the living source of truth

## Type Safety

- All public functions fully typed — parameters and return values
- No untyped dynamic values (any, object, dict) without documented justification
- Use dataclasses, TypedDict, or Pydantic models for structured data

## Error Handling

- All errors caught, logged, and surfaced appropriately
- No silent failures — every except block has a meaningful action
- Custom exception types for domain errors
- Error messages include context: what failed, why, what to do

## Code Structure

- Business logic separated from I/O, UI, and infrastructure
- Dependencies injectable — no hard-wired external calls in business logic
- Each module has a single clear responsibility
- Functional style preferred: immutable data, pure functions where practical

## Code Clarity

- Docstrings on all public classes and functions
- Functions do one thing and are named for what they do
- No magic numbers or strings — use named constants
- Comments explain why, not what
- Prefer explicit over clever

## Naming Conventions

- Packages: lowercase with hyphens for directory names (e.g., `nestor-core`), underscores for Python imports (e.g., `nestor_core`)
- Modules: lowercase snake_case (e.g., `llm_router.py`, `vector_store.py`)
- Classes: PascalCase (e.g., `LLMRouter`, `DocumentStore`)
- Functions and methods: snake_case (e.g., `ingest_document`, `get_profile`)
- Constants: UPPER_SNAKE_CASE (e.g., `DEFAULT_CHUNK_SIZE`, `MAX_RETRIES`)
- Private members: single leading underscore (e.g., `_parse_response`)
- Test files: `test_` prefix matching source module (e.g., `test_llm_router.py`)
- Test functions: `test_` prefix describing behavior (e.g., `test_returns_empty_when_no_results`)
- Spec feature directories: kebab-case (e.g., `llm-abstraction`, `knowledge-pipeline`)
- No abbreviations in public APIs unless universally understood (e.g., `url`, `id`, `api`)

## File Organization

- No orphaned files — every source file belongs to a package with `__init__.py`
- No empty `__init__.py` files without purpose — use them for public API exports or leave a comment explaining the package
- Imports grouped: stdlib → third-party → local, separated by blank lines
- No circular imports — if detected, refactor to break the cycle
- One class per file for major classes; small related utilities can share a file
- Keep files under 300 lines — split if larger

## Testability

- Public API designed for unit testing in isolation
- Side effects pushed to the edges (ports and adapters)
- No global mutable state
- Test fixtures and factories over hardcoded test data

## Testing Standards

- Unit tests for business logic (>80% coverage target)
- Integration tests for API endpoints and data layer
- Property-based tests for critical algorithms and data transformations
- Test isolation — no shared mutable state between tests
- Tests co-located in tests/ within each package
- Tests run in CI before merge
