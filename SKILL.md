---
name: points
description: Write, refine, trace, and verify Points specs. Use when creating design specs, adding components, running traces, checking invariants, or evolving the points/ directory. Points is a multi-layer description language for system design, executed by LLMs rather than compilers.
argument-hint: [action] [target] (e.g. "add component proxy.md PX.7 HealthCheck", "trace backlog carry-over scenario", "verify zsxq.md")
---

# Points — Design Description Language

You are working with a Points spec. Points is a multi-layer description language where precision comes from completeness of content, not formalism of syntax. Your executor is an LLM — you read and reconstruct intent, you don't parse grammar. This means incomplete descriptions are dangerous: you will improvise plausibly, and plausible-but-wrong is the hardest bug to catch.

## Philosophy

There isn't a real boundary between design and code. They are different layers of the same description — a bound language system.

The analogy is sketching. Top-level strokes are few but critical. Lower layers multiply effort. Mistakes are cheapest at the top. Don't polish a module whose interface is wrong. Keep the whole piece at roughly the same level of finish.

When the description is incomplete, a compiler fails. An LLM improvises. Fill gaps with something plausible, and plausible is the most dangerous kind of wrong — it looks right, passes casual review, and embeds a silent assumption nobody agreed to. Therefore: completeness over formalism. Every word earns its place.

## Workflow

See `.claude/rules/workflow.md` for the spec-first development workflow, parent/child consistency rules, and language evolution tracking.

## Skill Config

The configuration file `config.md` is located at `.claude/skills/points/config.md` . Same level as `SKILL.md`.

## Example
* Assume the project name is "database".
* "database" has a component "proxy".
* "proxy" has a component "proxy manager", "load balancer".
* "proxy manager" has a component "proxy pool".

For simplicity, in the Points language there is no fundamental distinction between a topic, a component, and a point; within a spec, “components” are the items under a topic and “points” are the items under a component, while generically we refer to topics, components, and points uniformly as the concept points.


## Spec Rules

1. **One sentence per line.** Every word earns its place.

2. There are two types of spec files: **topic specs** and **inter-file dependency specs**.

3. **Each file has exactly two information levels**: components + key design points under each.
   - Example:
     - In the top-level spec file where the topic is "database", database's first component is "proxy", proxy's first point is "proxy manager".

4. **Each component becomes its own child file** for refinement (its design points become that file's components).

   * **Intentional redundancy** between parent and child files — this creates chained recursive review.

   * **Parent points that expand into child files must not reference implementation details.** These points describe what the component is responsible for, not how it works. Full evolving detail lives exclusively in the child file. Points that do not expand into child files carry full detail as normal.

   * Example:

     - In the top-level spec file where the topic is "database", database's first component is "proxy", proxy's first point is "proxy manager".

     - In the proxy spec file where the topic is "proxy", proxy's first component is "proxy manager", proxy manager's first point is "proxy pool".

5. **IDs are hierarchical.** 
   - The ID format is `<ProjectName>-<TopicNumber>.<TopicName>-<ComponentNumber>.<ComponentName>`.
   - Note that an ID is a nested structure: a component at the current level can become the topic at the next level - like `<Level1>-<Level2>-<...>-<LevelN>`.
   - Each topic and component must have a globally unique ID. Uniqueness is automatically guaranteed by the numbering scheme.
   - Example:
     - In the top-level spec file, the topic ID is `Database`, proxy's ID is `Database-1.Proxy`, proxy manager's ID is `Database-1.Proxy-1.ProxyManager`.
     - In the proxy spec file, the topic ID is `Database-1.Proxy`, proxy manager's ID is `Database-1.Proxy-1.ProxyManager`, proxy pool's ID is `Database-1.Proxy-1.ProxyManager-1.ProxyPool`.
     - If a point does not correspond to a named concept (e.g., a component, algorithm, or policy) and will not be further expanded into a topic-level or component-level spec, its ID may include only the numeric suffix, for example `Database-1.Proxy-1.ProxyManager-2`.
     - To keep the spec concise, a spec file’s topic ID uses the full name (e.g. `Database-1.Proxy`), while components and points under it may use shortened numeric prefixes (e.g. `1.1.ProxyManager`, `1.1.1.ProxyPool`), where `1.1.1.ProxyPool` is equivalent to `Database-1.Proxy-1.ProxyManager-1.ProxyPool`.

6. **Cross-references**

   * For completeness, a point’s description may reference other points (where “points” is used in the generic sense).
   * All references—whether intra-file or inter-file—must use full IDs, not shortened numeric prefixes (e.g. `Database-1.Proxy-1.ProxyManager-1.ProxyPool`).
   * Note that references and dependencies may overlap in content, but they are fundamentally different: references exist only to make a point’s description complete and clear, while dependencies must contain a full dependency specification.

7. **Intra-file dependencies**

   * Each spec file must include an Intra-file Dependencies section.
   * Like components/points, Intra-file Dependencies uses the same two-level structure: the first level lists dependent component pairs (e.g. `1.1.ProxyManager-1.2.LoadBalancer`), which is globally unique, and the second level lists the specific dependency description points.
   * Each dependency point also has a globally unique ID in the format `<ShortenedFirstID>-<ShortenedSecondID>.<LocalPointIndex>`, where `<ShortenedFirstID>` and `<ShortenedSecondID>` are both component IDs. For example `1.1.ProxyManager-1.2.LoadBalancer.1`.
   * Note that intra-file dependencies may involve more than two parties; in that case, the first-level title must list all participating IDs in the format `<FirstID>-<SecondID>-<ThirdID>` (and so on).
   * Note that a first-level dependency title is unordered (it does not encode “A depends on B”); we group mutual dependencies between A and B under the same first-level title.
   * These dependencies may drill down into very fine-grained levels, so they are refined layer by layer along the spec file hierarchy: details captured as **intra-file dependencies** in the current spec will be described as **inter-file dependencies** at the next level.
   * If we need to trace details, rewrite the first-level ID as infer-file name (refer to **8. Spec file name** for the file name format) and then search for the corresponding inter-file dependency spec file.

8. **Inter-file dependencies**

   * Inter-file dependencies must be described in a separate spec file.
   * Each inter-file dependency spec file should describe one minimal multi-party dependency.
   * An inter-file dependency spec file uses the same internal structure as **Intra-file Dependencies**.
   * In an inter-file dependency spec file, there is only a **Dependencies** section, no **Components** section included.
   * The first-level entries are multi-party's inter-related component, and the set of dependent components forms a single dependency group. If they can be partitioned into multiple independent groups, do not mix them together.
   * Two dependency pairs are independent if removing either would not affect the description or correctness of the other.
   * Pairs that share a mechanism (e.g., the same callback, bridge, or data flow) belong in one file; pairs that merely share a common party but describe unrelated interactions should be split into separate files.

9. **Spec file name**
   * **Topic spec** use the topic’s full ID as the filename, e.g. `Database-1.Proxy-1.ProxyManager-1.ProxyPool`.
   * **Inter-file dependency spec files** use filenames with two parts: the `Dep-` prefix and `<ShortenedFirstID>-<ShortenedSecondID>` format multi-party combination, e.g. `Dep-1.1.ProxyManager-1.2.LoadBalancer`.
   
10. **Code traceability**

    Points supports bidirectional traceability between spec and source code.

    * **Spec → Code (`## Code Mapping` section)**
      * Each topic spec file may include a `## Code Mapping` section, placed after `## Intra-file Dependencies`.
      * Inter-file dependency spec files do not have a Code Mapping section.
      * First level: bold component ID (same IDs used in `## Components`).
      * Second level (3-space indent): `<relative-path> :: <keyword1>, <keyword2>, ...`
      * Keywords are grep-friendly identifiers: class names, function names, constants, or other tokens that locate the relevant code.
      * One line per source file.
      * Components with no implementation yet are omitted from this section.

    * **Code → Spec (source file header)**
      * At the top of each source file (after shebang/encoding lines), a comment block lists related spec points.
      * Marker line: `# Points Spec References:` (using the language's comment syntax).
      * Each entry (3-space indent): `#   [<FullID1>, <FullID2>, ...] <one-sentence why>`
      * Point IDs use full IDs (consistent with Spec Rule 6).
      * Multiple related points are grouped in one bracket with a shared reason.
      * Files with no spec link omit the block entirely.

11. **Proposals**

    * Spec files describe only what is implemented (ground truth).
    * Aspirational designs, planned features, and proposed changes live in a separate `proposals/` subdirectory under the spec directory.
    * Proposal files are named `proposals/<YYYY-MM-DD-HH> <proposal-brief>.md`, where the timestamp is creation time and the brief is a short kebab-case summary.
    * The file opens with a brief introduction (one or two sentences) describing what the proposal aims to achieve.
    * Each section (level-2 heading = component being extended) contains two parts:
      * **Involved points**: the existing spec points that the proposal touches or depends on, listed with their full IDs and current descriptions.
      * **Proposal**: the proposed changes, written in the same style as spec points (one sentence per line, 3-space indentation) so they can be moved directly into the spec file with minimal editing; no global IDs.
    * When a proposal is implemented, integrate its content into the main spec file and assign spec IDs to the new points during integration.
    * Once a proposal file is implemented, move it to `proposals/resolved/` (preserving its filename) for historical reference; do not delete or clean up resolved proposal content.

12. **Spec text format**

    * File format:
      * All Points spec files must be written in Markdown.

    * Topic:
      * In a spec file, the topic uses a level-1 heading, e.g. `# Database`.
      * Topic's description uses normal body text.
    * Components and points:
      * All components must be listed under the **Components** section. The section title uses a level-2 heading, e.g. `## Components`.
      * Components ID use bold text, e.g. `**1.Proxy**`.
      * Components' description uses normal body text.
      * Points under a component use normal body text, e.g. `1.1.ProxyManager`.
      * Points' description uses normal body text.
      * Use 3-space indentation: components at no indentation, points at 3-space indentation.  
    * Intra-file Dependencies
      * All intra-file dependencies must be listed under the **Dependencies** section. The section title uses a level-2 heading, e.g. `## Intra-file Dependencies`.

## File Structure

Points uses a flat file layout with no nested directories; hierarchy is expressed through filenames.

```
points/
├── Database.md    ← System overview + top components
├── Database-1.Proxy.md   ← Refinement of one root component
├── Database-1.Proxy-1.ProxyManager.md   ← Refinement of one second layer component
├── ...
├── Dep-1.1.ProxyManager-1.2.LoadBalancer.md
└── proposals/
    ├── 2026-02-14-15 proxy-graceful-drain.md   ← Aspirational design proposal
    └── resolved/
        └── 2026-02-13-10 proxy-health-timeout.md   ← Implemented proposal (archived)
```

## Example Spec Files

**Database.md (root topic spec)**

```markdown
# Database

A distributed database that routes queries through a proxy layer.

## Components

**1.Proxy**
Accepts client connections, parses queries, and forwards them to the correct data node.
   1.1.ProxyManager
   Maintains a pool of proxy instances and handles lifecycle (start, stop, restart).
   1.2.LoadBalancer
   Distributes incoming connections across proxy instances using round-robin.
```

**Database-1.Proxy.md (child topic spec)**

```markdown
# Database-1.Proxy

Accepts client connections, parses queries, and forwards them to the correct data node.

## Components

**1.1.ProxyManager**
Maintains a pool of proxy instances and handles lifecycle (start, stop, restart).
   1.1.1.ProxyPool
   Fixed-size pool of reusable proxy workers; grows on demand up to a configured cap.
   1.1.2.HealthCheck
   Pings each proxy worker on a timer; removes unresponsive workers from the pool.

**1.2.LoadBalancer**
Distributes incoming connections across proxy instances using round-robin.
   1.2.1.WeightTable
   Per-instance weight derived from recent latency; updated every health-check cycle.

## Intra-file Dependencies

**1.1.ProxyManager-1.2.LoadBalancer**
   1.1.ProxyManager-1.2.LoadBalancer.1
   LoadBalancer reads the live worker list from ProxyPool to know which instances are available.
   1.1.ProxyManager-1.2.LoadBalancer.2
   HealthCheck removal of a worker triggers LoadBalancer to recompute the WeightTable.

## Code Mapping

**1.1.ProxyManager**
   src/database/proxy/manager.py :: ProxyManager, start_worker, stop_worker

**1.2.LoadBalancer**
   src/database/proxy/balancer.py :: LoadBalancer, WeightTable, recompute_weights
```

**Dep-1.1.ProxyManager-1.2.LoadBalancer.md (inter-file dependency spec)**

```markdown
# Dep-1.1.ProxyManager-1.2.LoadBalancer

## Dependencies

**1.1.1.ProxyPool-1.2.1.WeightTable**
   1.1.1.ProxyPool-1.2.1.WeightTable.1
   WeightTable entries are keyed by worker ID; when ProxyPool adds or removes a worker, WeightTable must insert or delete the corresponding entry.
   1.1.1.ProxyPool-1.2.1.WeightTable.2
   ProxyPool exposes a change-notification callback; WeightTable subscribes at startup.

**1.1.2.HealthCheck-1.2.1.WeightTable**
   1.1.2.HealthCheck-1.2.1.WeightTable.1
   Each health-check cycle emits per-worker latency; WeightTable uses this to recalculate weights.
```

**proposals/2026-02-14-15 proxy-graceful-drain.md (proposal file)**

```markdown
# Proposals: Database-1.Proxy

Allow proxy workers to finish in-flight connections before being removed, avoiding abrupt disconnects during scaling or maintenance.

## 1.1.ProxyManager

- **Involved points**
   1.1.1.ProxyPool Fixed-size pool of reusable proxy workers; grows on demand up to a configured cap.
   1.1.2.HealthCheck Pings each proxy worker on a timer; removes unresponsive workers from the pool.

- **Proposal: Graceful drain mode**
   ProxyPool gains a drain state that stops accepting new connections while existing ones finish.
   HealthCheck skips draining workers so they are not removed prematurely.
   Once all connections on a draining worker close, the worker is removed from the pool.
```

## Two Layers: Definitions and Traces

### Definitions (what the system is)
- What each component is and what state it holds.
- What happens when key events occur.
- Invariants that must always hold.
- Key decisions and their rationale.

### Traces (testing without code)
- A trace is a narrated simulation: pick a scenario, walk through step by step.
- State what happened, what changed, whether invariants hold.
- A trace that violates an invariant is a discovery — name the problem, propose a fix.
- Definitions and traces co-evolve: traces test hypotheses, broken traces fix definitions.
- Traces are not documentation of definitions — they are the mechanism that makes definitions trustworthy.

## Actions

When the user invokes `/points`, determine the action from $ARGUMENTS:

### `add` — Add a new point or component
1. Read the target file.
2. Determine the correct ID (next available under the parent), following the hierarchical ID format (Spec Rule 5).
3. Write the new content following spec text format: bold component IDs, 3-space indentation for points, one sentence per line (Spec Rules 1, 12).
4. If the new point references other points, include cross-references using full IDs inline in the description (Spec Rule 6).
5. Update the `## Intra-file Dependencies` section if the new point introduces intra-file dependencies (Spec Rule 7).
6. If the new point introduces inter-file dependencies, create or update the corresponding `Dep-*.md` file (Spec Rules 8, 9).
7. Update parent and child spec files to maintain consistency (see `workflow.md`).
8. If adding a component, check whether a child file should be created (Spec Rule 4).
9. If the new component has known code, add entries to `## Code Mapping` in the spec and `# Points Spec References:` in the source files (Spec Rule 10).
10. If the new point is aspirational (not yet implemented), add it to the proposals file instead of the main spec (Spec Rule 11).
11. Read `config.md` for issues/changelog paths if this addition requires a language revision note (see `workflow.md`).

### `refine` — Expand a point into more detail
1. Read the point and its context.
2. If refining a component: create or update its child file, following the spec file naming convention (Spec Rule 9).
3. Maintain parent-child redundancy — the parent's key design point must match the child's component (Spec Rule 4).
4. Update parent and child spec files as needed (see `workflow.md`).
5. Update `## Intra-file Dependencies` if refinement introduces new intra-file dependencies (Spec Rule 7).
6. Create or update `Dep-*.md` files if refinement introduces inter-file dependencies (Spec Rules 8, 9).
7. Ensure all cross-references use full IDs (Spec Rule 6).
8. Update `## Code Mapping` if refined components have known code (Spec Rule 10).

### `trace` — Run a narrated simulation
1. Read the relevant definition files.
2. State the scenario clearly.
3. Walk through step by step: event → state change → invariant check.
4. If an invariant breaks, name the problem and propose a definition fix.
5. Output the trace as a numbered walkthrough with explicit state at each step.

### `verify` — Check a file or the whole spec for consistency

**Full mode** (default): checks the entire spec or a single file.
1. Every point has a globally unique ID — no duplicates across all files (Spec Rule 5).
2. Every file follows spec text format: bold component IDs, 3-space indentation for points, one sentence per line, two information levels per file (Spec Rules 1, 3, 12).
3. All cross-references use full IDs, not shortened numeric prefixes (Spec Rule 6).
4. Every topic spec file has an `## Intra-file Dependencies` section (Spec Rule 7).
5. All inter-file dependencies have a corresponding `Dep-*.md` file with correct naming and structure (Spec Rules 8, 9).
6. Parent-child redundancy is present and consistent (Spec Rule 4).
7. If `## Code Mapping` is present, verify that referenced files exist and keywords are findable via grep; verify that source files with `# Points Spec References:` reference valid point IDs (Spec Rule 10).
8. Report spec consistency issues directly to the user (these are project-specific, not language issues — see `workflow.md`).
9. If the verify process reveals ambiguities, gaps, or tensions in the Points language rules themselves, record those observations in the language issues file (see `workflow.md`).

**Local mode** (`--local`): scoped verification of only the changed parts.
Invoked as `/points verify --local <spec-file-or-point-IDs>`.
1. Identify the changed spec points (from the provided file or point IDs).
2. Use `## Code Mapping` to find the source files that implement those points.
3. Verify spec-to-code: each changed point's description matches the actual code behavior; Code Mapping keywords are findable via grep.
4. Verify code-to-spec: `# Points Spec References:` in the affected source files reference valid, current point IDs.
5. Check parent-child consistency: trace the changed file's parent and children for redundancy alignment (Spec Rule 4).
6. Report mismatches to the user.

### `evolve` — Update definitions based on code changes
1. Read the code that changed (git diff or specified files).
2. Use `# Points Spec References:` headers in changed files to identify affected spec points.
3. Use `## Code Mapping` sections to find all spec files that reference the changed code.
4. Compare against current Points definitions.
5. Identify gaps: new behaviors, changed invariants, new components.
6. Propose specific definition updates with exact point IDs.
7. Update both `## Code Mapping` and `# Points Spec References:` if code locations changed (Spec Rule 10).

### `sync` — Align code and spec
1. Read both code and Points spec for a component.
2. Use `## Code Mapping` to locate relevant source files and grep keywords.
3. Use `# Points Spec References:` in source files to cross-check the reverse direction.
4. Identify mismatches in either direction (spec says X, code does Y; code does Z, spec is silent).
5. Check that Code Mapping entries and Points Spec References are consistent with each other.
6. For each mismatch, recommend whether to update the spec or the code, with rationale.
