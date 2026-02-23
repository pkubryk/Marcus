# Marcus Consistency Checklist

Audit the .kiro/ directory for internal consistency between documentation,
steering files, hooks, and checklists.

## README Accuracy
- Every file in hooks/ is listed in the README's Structure section
- Every file in checklists/ is listed in the README's Structure section
- Every file in steering/ is listed in the README's Structure section
- The Agents table lists every userTriggered hook with correct name and checklist path
- The Automatic Hooks table lists every non-userTriggered hook
- The Steering Files table lists every steering file
- The Workflow section matches the workflow in methodology.md
- No references to files that don't exist
- No files that exist but aren't documented

## Cross-Reference Integrity
- Every checklist path referenced in a hook's prompt actually exists as a file
- Every checklist path referenced in methodology.md actually exists as a file
- Every hook mentioned in methodology.md has a corresponding .json file in hooks/
- Every review mentioned in the Available Reviews table has a corresponding checklist
- The task list example in methodology.md references only existing checklists

## Steering-Hook Alignment
- Rules described in steering are enforced by corresponding hooks (or vice versa)
- No contradictions between steering rules and hook behavior
- Quality standards in quality.md align with what hooks check for
- Methodology workflow steps align with available hooks

## Naming Consistency
- Hook file names match their internal "name" field convention
- Checklist file names match what hooks reference
- No mismatched naming between README descriptions and actual file names

## Output Format

For each inconsistency found:
1. State what is inconsistent (e.g., "README lists X but file doesn't exist")
2. State which files are involved
3. Propose the fix (update README, rename file, add missing reference, etc.)

End with: consistent / has drift / significantly out of sync
