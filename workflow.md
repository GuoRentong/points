# Spec-First Development Workflow

HARD RULE: NEVER edit any source file (code, tests, configs) before presenting Points spec changes to the user for review and receiving explicit approval. No exceptions.

## Gate

For ANY project change — new features, bug fixes, refactors, config changes — you MUST:

1. **Read both** the relevant source code AND the relevant Points spec files. You must read both before making any assessment. Code tells you what the implementation does; the spec tells you what it should do and what it claims to do. Never assess whether the spec needs changes without reading it first.
2. **Update the Points spec** (`points/`) to reflect the intended change. Every design point must be validated against the code — never write spec changes based on imagination or assumption.
3. **Present** the Points spec changes to the user. Stop and wait for approval.
4. **Implement** only after the user approves.
5. **Verify** Points spec and code are consistent (`/points verify` or `/points sync`).

If the Points spec does not need changing, you MUST still present your reasoning to the user and get approval before writing code. You must cite the specific spec points you read and explain why they already cover the change. "The Points spec is already correct, here's what I'll change in code: ..." counts as presenting for review — but only if you actually read the spec first.

## Rationalizations that are NOT valid reasons to skip review

- "It's just a bug fix"
- "The Points spec doesn't need changing"
- "It's a one-line change"
- "The user said 'fix it' so they want speed"
- "This is an implementation detail, not a design change"

If you catch yourself thinking any of these, STOP. Present the change for review anyway.

## What "present for review" means

- Describe what you found (the problem, the mismatch, the gap).
- Show what Points spec changes are needed (or explain why none are needed).
- Show what code changes you plan to make.
- Wait for the user to say proceed. Do not proceed on your own.

## Sync rule

Any changes to this file MUST be synced to the Points repo:
```
cp .claude/rules/workflow.md .claude/skills/points/workflow.md
```
