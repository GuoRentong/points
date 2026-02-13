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

## Action Rules

1. For any changes to code, configuration files, or other logic, we must first update, extend, and most importantly verify the design in the **Points Spec**, and only then implement, while strictly ensuring all related referenced sections remain fully consistent and exactly aligned with the code.
2. If we modify a spec file, make sure to update its parent and child files as needed, using both backward-tracing and recursive updates.
3. Points is a fast-evolving language, so if we need to revise it during development, we must first record the scenario and proposal and log that day’s issues in a separate Markdown file. Read `config.md` to determine the issues directory and any other relevant configurations.
4. If the language is modified in any way, we must record a changelog, and if a change fixes an issue or adds a feature proposed in an issues file, we must reference the corresponding issue(s) number. Read `config.md` to determine the changelog path and any other relevant configurations.

## Skill Config

* The configuration file `config.md` is located at `.claude/skills/points/config.md` .

## Example
* Assume the project name is "database".
* "database" has a component "proxy", "WAL", "data node", "storage".
* "proxy" has a component "proxy manager", "load balancer".
* "proxy manager" has a component "proxy pool".

For simplicity, in the Points language there is no fundamental distinction between a topic, a component, and a point; within a spec, “components” are the items under a topic and “points” are the items under a component, while generically we refer to topics, components, and points uniformly as the concept points.


## Format Rules

1. **One sentence per line.** Every word earns its place.

2. There are two types of spec files: **topic specs** and **inter-file dependency specs**.

3. **Each file has exactly two information levels**: components + key design points under each.
   - Example:
     - In the top-level spec file where the topic is "database", database's first component is "proxy", proxy's first point is "proxy manager".

4. **Each component becomes its own child file** for refinement (its design points become that file's components).

   * **Intentional redundancy** between parent and child files — this creates chained recursive review.

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
   * The first-level entries are multi-party’s inter-related component, and the set of dependent components forms a single dependency group. If they can be partitioned into multiple independent groups, do not mix them together.

9. **Spec file name**
   * **Topic spec** use the topic’s full ID as the filename, e.g. `Database-1.Proxy-1.ProxyManager-1.ProxyPool`.
   * **Inter-file dependency spec files** use filenames with two parts: the `Dep-` prefix and `<ShortenedFirstID>-<ShortenedSecondID>` format multi-party combination, e.g. `Dep-1.1.ProxyManager-1.2.LoadBalancer`.
   
10. **Spec text format**

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
      * Use 3-space indentation per hierarchy level: 3 spaces for components and 6 spaces for points.
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
└── Dep-1.1.ProxyManager-1.2.LoadBalancer.md
```

## Example Spec Files



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
2. Determine the correct ID (next available under the parent).
3. Write one sentence per line, precise and complete.
4. Update `## Relationships` in the file if the new point has cross-references.
5. Update `deps.md` if the new point has inter-file dependencies.
6. If adding a component to the root file, check whether a child file should be created.

### `refine` — Expand a point into more detail
1. Read the point and its context.
2. If refining a component: create or update its child file.
3. Maintain parent-child redundancy (the parent's key design point should match the child's component).
4. Update relationships and deps.

### `trace` — Run a narrated simulation
1. Read the relevant definition files.
2. State the scenario clearly.
3. Walk through step by step: event → state change → invariant check.
4. If an invariant breaks, name the problem and propose a definition fix.
5. Output the trace as a numbered walkthrough with explicit state at each step.

### `verify` — Check a file or the whole spec for consistency
1. Every point has a globally unique ID (no duplicates across all files).
2. Every file follows format rules (one sentence per line, two levels, numbered hierarchy).
3. All cross-references use explicit IDs.
4. All inter-file references appear in `deps.md`.
5. Parent-child redundancy is present.
6. Report any issues found.

### `evolve` — Update definitions based on code changes
1. Read the code that changed (git diff or specified files).
2. Compare against current Points definitions.
3. Identify gaps: new behaviors, changed invariants, new components.
4. Propose specific definition updates with exact point IDs.

### `sync` — Align code and spec
1. Read both code and Points spec for a component.
2. Identify mismatches in either direction (spec says X, code does Y; code does Z, spec is silent).
3. For each mismatch, recommend whether to update the spec or the code, with rationale.
