# Spec-First Development Workflow

HARD RULE: NEVER edit any source file (code, tests, configs) before presenting Points spec changes to the user for review and receiving explicit approval. No exceptions.

## Gate

For ANY project change — new features, bug fixes, refactors, config changes — you MUST:

1. **Read** the relevant source code to understand the actual implementation — APIs, data structures, control flow. Never write Points spec changes based on imagination or assumption; every design point must be validated against the code.
2. **Update the Points spec** (`points/`) to reflect the intended change.
3. **Present** the Points spec changes to the user. Stop and wait for approval.
4. **Implement** only after the user approves.
5. **Verify** Points spec and code are consistent (`/points verify` or `/points sync`).

If the Points spec does not need changing, you MUST still present your reasoning to the user and get approval before writing code. "The Points spec is already correct, here's what I'll change in code: ..." counts as presenting for review.

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
