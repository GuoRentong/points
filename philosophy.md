# Philosophy

This document explores the beliefs behind Points.


## 1. Why Multi-Layer Language Matters

There isn't a real boundary between design and code. They're better thought of as different layers of the same description, they should be thought of as a binded language system.

The analogy is sketching. At the top level you focus on the skeleton and the layout, without worrying about details. As you go lower, you get more detail-oriented. The top-level strokes are a tiny fraction of the total, but they're the most critical. The lower you go, the more strokes you need and the more time you spend refining. Each layer down multiplies the effort. This is why mistakes are cheapest to catch at the top — erasing one line in a sketch is trivial; reworking a thousand lines of rendering because the skeleton was off is not.

A common failure mode: jumping to detail too early. Getting attached to a beautifully rendered corner while the overall composition is broken. The equivalent in software is polishing a module's implementation while its interface is wrong. Experienced artists keep the whole piece at roughly the same level of finish and refine together. Experienced system builders should do the same — don't write detailed code for a component whose role in the system is still sketchy.

A big reason coding agents hallucinate — or drift from what you intended in the implementation — is that this kind of multi-layered description is missing. People often give a very vague, high-level "requirement," which is basically like sketching an imprecise, incomplete skeleton on the canvas, and then handing the pencil to the agent. But from that kind of skeleton, there are tons of valid ways to refine it, and the final result can vary wildly. That's why, in human–agent or multi-agent coding, the accuracy and completeness of the higher-level description is absolutely critical.


## 2. The Executor Problem

Most languages are designed for a deterministic executor. A compiler or interpreter reads syntax, follows grammar rules, and produces the same output every time. The language designer's job is to make the syntax unambiguous so the machine never has to guess.

Points has a different executor: an LLM. It doesn't parse — it reads. It doesn't follow grammar rules — it reconstructs intent from context. This changes what precision means. A formal spec is precise because its syntax eliminates ambiguity. Points is precise because its content eliminates ambiguity. The burden shifts from structure to completeness.

This is powerful. An LLM can read "exposure must never go negative" and understand what exposure is, what operations modify it, and when to check — without type declarations, function signatures, or assertion macros. It can follow "selling follows the same logic against the buy side" without needing the logic restated. It fills structural gaps the way a human reader would.

But this is also the danger. When the description is incomplete, a compiler fails. An LLM improvises. It fills the gap with something plausible, and plausible is the most dangerous kind of wrong — it looks right, passes casual review, and embeds a silent assumption that nobody agreed to.

Programs need memory to run. Higher-level languages do too — but in a different way. We need to design a context that serves as the "working memory" for agents running a Points program. That context should support things like human-style brainstorming, mental experiments, and validation.


## 3. Trace

You can verify a system without running it. Read the definitions, construct the state in your head, step through an event, and check whether the invariants hold. Humans do this naturally — it's called thinking through a design. Points makes this process explicit and structured enough that an agent can do it too.

This is what traces are. A trace is a narrated simulation. The agent reads the definitions, picks a scenario, and walks through it step by step — stating what happened, what changed, and whether the rules still hold. It's not executing code. It's reasoning about what the code would do if it existed.

The value isn't just in confirming that things work. It's in what happens when they don't. A trace that violates an invariant is a discovery. The agent names the problem, proposes a fix, and the fix flows back into the definitions. The trading platform example shows this: the conservation invariant had to be revised because a trace revealed that cancellations weren't accounted for. That bug was found without writing a single line of code.

This feedback loop is the engine of Points. Definitions are never finished on the first pass — they're hypotheses. Traces test those hypotheses. When a trace breaks, the definitions learn. Invariants get tightened. Missing behaviors get added. Decisions get revisited. Each cycle produces a more complete description, and a more complete description produces better traces. The definitions and traces co-evolve until the design stabilizes.

This means the trace layer isn't secondary to the definition layer. It's not documentation of what the definitions say — it's the mechanism that makes the definitions trustworthy.

Simulation has limits. An agent can miss cases a fuzzer would catch. It can reason correctly from incomplete definitions and never notice the gap. It won't find performance problems or race conditions that only emerge under load. But for structural and logical errors — wrong boundaries, missing behaviors, broken invariants — narrated simulation catches them at the cheapest possible moment: before implementation begins.


## 4. Language vs. Formalism

Natural language is expressive but ambiguous. Formal specification is precise but hostile to read. Most design notations pick a spot on this spectrum and stay there. Points tries to stay as close to natural language as possible while remaining logically executable.

The bet is that you don't need syntax to be precise — you need completeness. "Exposure must never go negative" is as precise as `assert exposure >= 0` if the surrounding description makes clear what exposure is, what modifies it, and when the check applies. The formalism is in the content, not the notation.


## 5. Organization

Points enforces only two structural devices: hierarchy and one sentence per line. Hierarchy gives you scope — a child elaborates or constrains its parent. One sentence per line forces economy — every word has to earn its place. Beyond that, everything depends on the quality of the writing.

For easier tracing and search — and for better clarity and concision — Points organizes each topic as a separate file. Within each file, we enforce exactly two levels of information: the components under that topic, and the key design points for each component.

Refinement happens in the next layer of files: each component becomes its own standalone topic (and its "key design points" become the components of that new file). We then refine those components at the second level of that file. There's no limit on how deep the file hierarchy can go, but the rule stays the same: each file contains only two information levels.

There is intentional redundancy between parent and child files. That redundancy is critical — it ensures that as we expand details in lower-level files, we repeatedly review the parent file's key design points, and the overlap naturally creates recursion and backtracking in the design process.


## 6. Dependency

Each topic, component, or design item must have a globally unique ID. For convenience, we refer to topics, components, and design items collectively as points.

The overall description forms a tree-like structure, but it isn't a strict tree, because points can have cross-links and dependencies.

In each file, in addition to the descriptions of the points themselves, we also require two kinds of relationship descriptions: relationships among points within the file — how the points interact with or depend on each other inside this topic — and relationships between points in this file and points in other files — cross-topic dependencies and references.

All relationships must explicitly list the involved point IDs. Intra-file relationships are documented in the same file. Inter-file relationships — dependencies across multiple files — are documented in a separate shared file, and that file should be referenced by all related topic files.
