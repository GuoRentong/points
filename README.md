# Points

A multi-layer design description language for system design, executed by LLMs rather than compilers.

Points treats design and code as different layers of the same description. Precision comes from completeness of content, not formalism of syntax. When descriptions are incomplete, an LLM improvises — and plausible-but-wrong is the most dangerous kind of bug.

## Install as a Claude Code Skill

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/GuoRentong/points.git ~/.claude/skills/points
```

That's it. Claude Code automatically discovers skills in `~/.claude/skills/*/SKILL.md`.

Once installed, invoke with `/points` in any Claude Code conversation:

```
/points add component proxy.md PX.7 HealthCheck
/points trace backlog carry-over scenario
/points verify zsxq.md
```

### Updating

```bash
cd ~/.claude/skills/points && git pull
```

## What It Does

Points provides a structured way to write, refine, trace, and verify design specifications. The `/points` skill supports these actions:

- **add** — Add components or design points to a spec
- **refine** — Break down a component into sub-components
- **trace** — Walk through a scenario against the spec to find gaps
- **verify** — Check a spec file for internal consistency and completeness
- **evolve** — Propose and apply changes to the spec
- **sync** — Synchronize spec with code (bidirectional traceability)

Specs live in a `spec/` directory in your project and follow a flat file layout with hierarchical naming (e.g., `database.md`, `database-1.Proxy.md`, `database-1.Proxy-1.Manager.md`).

## Project Structure

```
SKILL.md       — Skill definition (rules, actions, examples)
config.md      — Configurable paths (spec location, issues, changelog)
workflow.md    — Spec-first development workflow
philosophy.md  — Design philosophy behind Points
changelog.md   — Language evolution history
issues/        — Language issue tracking (not project issues)
```

## Philosophy

There isn't a real boundary between design and code. The analogy is sketching: top-level strokes are few but critical, lower layers multiply effort, mistakes are cheapest at the top. Don't polish a module whose interface is wrong.

See [philosophy.md](philosophy.md) for more.
