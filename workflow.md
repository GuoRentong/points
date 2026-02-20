# Spec-First Development Workflow

HARD RULE: NEVER edit any source file (code, tests, configs) before presenting Points spec changes to the user for review and receiving explicit approval. No exceptions.

## Phase 1: Design (spec-only edits allowed)

For ANY project change — new features, bug fixes, refactors, config changes — you MUST:

1. **Read both** the relevant source code AND the relevant Points spec files. You must read both before making any assessment. Code tells you what the implementation does; the spec tells you what it should do and what it claims to do. Never assess whether the spec needs changes without reading it first.
2. **Update the Points spec** (`spec/`) to reflect the intended change. Every design point must be validated against the code — never write spec changes based on imagination or assumption.
3. **Check parent and child spec files** for consistency. After modifying any spec file, trace backward to its parent and forward to its children — update all affected files so redundancy stays aligned.

During Phase 1, the ONLY files you may edit are Points spec files under `spec/`. Do NOT touch code, tests, configs, or any other file.

## HARD STOP: Confirm before implementation

4. **Present the spec changes and ask for confirmation.** You MUST call AskUserQuestion to request explicit approval before proceeding. Show the spec diff (before/after for each changed point) and a summary of planned code changes. Do NOT proceed until the user confirms.

If the Points spec does not need changing, you MUST still present your reasoning and call AskUserQuestion. Cite the specific spec points you read and explain why they already cover the change.

## Phase 2: Implementation (only after user confirms)

5. **Implement** the approved spec changes in code, tests, and configs.
6. **Verify** Points spec and code are consistent (`/points verify` or `/points sync`).

## Rationalizations that are NOT valid reasons to skip the HARD STOP

- "It's just a bug fix"
- "The Points spec doesn't need changing"
- "It's a one-line change"
- "The user said 'fix it' so they want speed"
- "This is an implementation detail, not a design change"
- "The user already said 'proceed' to a different question"
- "The spec change is trivial so I can bundle it with code"

If you catch yourself thinking any of these, STOP. You have not passed the gate.

## When the Points language itself changes

Points is a fast-evolving language. If development reveals ambiguities, gaps, or tensions in the Points rules themselves (not in the project's spec), these are **language issues** — distinct from project-specific spec issues.

1. Record the scenario and proposal in the language issues file. Read `config.md` for the issues directory path.
2. Record the change in the language changelog. Read `config.md` for the changelog path.
3. Language issues apply exclusively to the Points language itself — its rules, spec format, actions, and file conventions. Project-specific spec issues (missing files, ID errors, redundancy gaps, content mismatches) are NOT language issues: report them directly to the user and fix them in the spec files.

Any changes to workflow.md or SKILL.md must also be synced to the Points repo.
