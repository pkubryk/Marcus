# Project Hygiene Checklist

Audit the project for cleanliness, organization, and maintainability.

## File and Directory Hygiene
- No orphaned files outside of a proper package structure
- No empty directories
- No leftover temporary files, scratch files, or debug artifacts
- No duplicate files or near-duplicate implementations
- All `__init__.py` files either export public API or document the package purpose

## Dead Code
- No unused imports (run linter to verify)
- No unreachable code or commented-out code blocks
- No unused functions, classes, or variables
- No unused dependencies in pyproject.toml / package.json
- No test files without corresponding source files (or vice versa)

## Naming Consistency
- All packages, modules, classes, functions follow the naming conventions in quality.md
- No inconsistent naming patterns (e.g., mixing camelCase and snake_case)
- No vague names (utils.py, helpers.py, misc.py) — name for what it does
- Test file names match source file names with test_ prefix

## README Freshness
- Package README accurately describes current functionality
- Setup/install instructions are correct and tested
- API/CLI documentation reflects current interface
- Architecture diagrams or descriptions match current structure
- No references to removed or renamed components

## Dependency Hygiene
- All dependencies pinned to specific versions or ranges
- No unused dependencies
- No deprecated dependencies with available replacements
- Dev dependencies separated from production dependencies

## Configuration Hygiene
- No secrets or credentials in code or config files
- Environment variables documented
- Default values are sensible and documented
- No conflicting or redundant configuration

## Output Format

For each issue found:
1. State the file or area
2. Describe the issue
3. Suggest the fix

End with a summary: overall hygiene score (clean / needs attention / messy)
and the top 3 items to fix first.
