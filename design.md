# Points

A natural-language design language for describing systems that agents can mentally execute.


## What Points Is

Points is a structured natural language for describing high-level system designs. Each line is one sentence. Each sentence carries only essential information. Every word earns its place. The hierarchy is expressed through numbered items and indentation. Agents can read a Points description, construct state, step through transitions, and verify invariants — all without executing real code.


## Why Points Exists

Current options all miss the mark for agent-driven development:

- Natural language docs are ambiguous, verbose, and bury decisions in paragraphs.
- UML and SysML are visual-only with no text-native form agents can reason over.
- Formal ADLs like AADL and Wright are academic and overkill.
- Code itself is too low-level to see architectural intent.
- YAML and JSON schemas are structural but semantically empty.
- TLA+ and Alloy are formal verification tools, not communication tools.

Points fills the gap: the decision layer between requirements and code.


## Design Principles

1 Every sentence earns its place.
  1.1 If removing a sentence doesn't lose information, it shouldn't be there.

2 State is narrated, not declared.
  2.1 Instead of type declarations, describe what something holds in plain words.
  2.2 The agent understands the structure from the description.

3 Rules are behaviors, not branches.
  3.1 Instead of if/else, describe when something happens and what follows.
  3.2 The agent infers the control flow.

4 Invariants are plain assertions.
  4.1 Write what must always be true as a sentence.
  4.2 No formal quantifiers needed.

5 Traces are stories with accountability.
  5.1 Each step states what happened, why, and what the world looks like after.
  5.2 The agent narrates its reasoning, catches its own mistakes, and discovers problems.

6 Findings are first-class.
  6.1 When a trace reveals something unexpected, the agent names it.
  6.2 Proposed fixes are written in the same natural language.
  6.3 Fixes flow back into the definitions as revisions.

7 Precision comes from completeness of information, not from syntax.
  7.1 The executor is an LLM, not a compiler.
  7.2 Structured natural language with logical precision is sufficient.


## Format Rules

1 One sentence per line.
  1.1 Every word in that sentence must carry meaning.
  1.2 If you can remove a word without losing information, remove it.

2 Indentation means "in the context of the parent."
  2.1 A child elaborates, constrains, or specifies.
  2.2 A child is only relevant when its parent is active.

3 Numbering shows hierarchy.
  3.1 Top-level items are numbered 1, 2, 3.
  3.2 Children are numbered 1.1, 1.2, 1.3.
  3.3 Deeper levels continue: 2.2.1, 2.2.1.1.
  3.4 One space between the number and the sentence.

4 Each level indents two spaces deeper than its parent.

5 A blank line separates top-level items for readability.

6 Component or section names stand alone as headers.


## Two Layers

Points has two layers: definitions and traces.

1 Definitions describe what is and how it behaves.
  1.1 They are the system at rest, the rules before anything happens.

2 Traces walk through what happens when events occur.
  2.1 Each step states the event, the reasoning, and the resulting state.
  2.2 Sub-items under a step show the derivation.
  2.3 Invariant checks appear after each step's state is established.
  2.4 Findings and corrections are part of the trace.
  2.5 They feed back into the definitions as revisions.


## What Makes Points Logically Runnable

1 Closed state: all state is described, no hidden variables, the agent sees everything.

2 Deterministic rules: same state plus same event produces the same next state.

3 Explicit effects: behaviors list all mutations and all emitted messages.

4 Checkable invariants: assertions can be evaluated against any state snapshot.

5 Branchable traces: traces can fork to explore alternatives.

6 Self-contained snapshots: the trace includes full state at each step, no external lookups.

7 Composable: component traces can be combined into system traces.


## How Agents Use Points

1 An architect agent reads the definitions and proposes a system structure.

2 A reasoning agent writes traces to verify the design mentally.
  2.1 It steps through scenarios, derives state, and checks invariants.
  2.2 It explores failure modes by branching traces.
  2.3 It discovers bugs by finding invariant violations.

3 A coder agent reads definitions to generate implementation scaffolding.
  3.1 Behaviors become function signatures and logic.
  3.2 Invariants become assertions and property-based tests.
  3.3 Traces become integration tests.

4 A profiler agent reads constraints to know what thresholds to verify.

5 A reviewer agent compares code against definitions to check alignment.

6 Findings from traces feed back into definitions, closing the loop.


## What Points Includes (Decision-Level)

- What something is and what it holds.
- What happens when events occur.
- What must always be true.
- What was decided and why.
- What was rejected and why.
- What would cause a decision to be revisited.
- How things fail and what happens when they do.


## What Points Excludes (Code-Level)

- Data structure implementations.
- Error handling details.
- Logging and observability plumbing.
- Configuration specifics.
- Serialization formats.

These live in the source code. Points describes what matters. The code tells you how.


## Full Example: Trading Platform


### Definition Layer

```
Trading Platform

1 This system matches buy and sell orders with pre-trade risk control.
  1.1 Orders arrive from clients through the Gateway.
  1.2 Each order is checked against account risk limits before matching.
  1.3 Fills produced by matching are sent to downstream clearing.


Gateway

1 The Gateway receives, validates, and routes client orders.
  1.1 It holds each order as pending until a risk decision arrives.
  1.2 It enforces a 50ms deadline on every risk check.

2 When an order arrives from a client:
  2.1 The Gateway validates the message format.
    2.1.1 Malformed orders are rejected immediately with a reason.
  2.2 The order is forwarded to Risk for checking.
  2.3 A 50ms deadline timer starts for this order.

3 When Risk responds with clearance:
  3.1 The timer is cancelled.
  3.2 The order is forwarded to the Book for placement.
  3.3 The order is removed from pending.

4 When Risk responds with a breach:
  4.1 The timer is cancelled.
  4.2 The order is rejected back to the client with the breach reason.
  4.3 The order is removed from pending.

5 When the deadline fires before Risk responds:
  5.1 The order is rejected to the client as a timeout.
  5.2 The order is removed from pending.
  5.3 A cancellation is sent to Risk to release reserved exposure.
    5.3.1 We chose explicit cancellation over passive expiry.
      5.3.1.1 Passive expiry leaves a window where exposure is incorrectly held.


Risk Engine

1 Risk maintains an exposure and a limit per account.
  1.1 Exposure is the total value of all live and pending orders.
  1.2 Exposure must never go negative.
  1.3 Exposure must reflect only orders that are actually live or pending.

2 When a risk check arrives:
  2.1 New exposure would be current exposure plus order value.
    2.1.1 Order value is quantity times price.
  2.2 If new exposure is within the limit:
    2.2.1 Exposure is updated.
    2.2.2 Clearance is sent back to Gateway.
  2.3 If new exposure exceeds the limit:
    2.3.1 Exposure is unchanged.
    2.3.2 A breach is sent back with the reason.

3 When a cancellation arrives:
  3.1 The reserved exposure for that order is subtracted.


Order Book

1 The Book holds buy orders and sell orders, and accumulates fills.
  1.1 Buys are ordered by price highest first, then by arrival time.
  1.2 Sells are ordered by price lowest first, then by arrival time.

2 When a buy order is placed:
  2.1 It matches against resting sells, starting from the cheapest.
  2.2 A match occurs when the buy price meets or exceeds the sell price.
    2.2.1 The fill executes at the resting sell's price.
      2.2.1.1 We chose resting price over midpoint for simplicity.
    2.2.2 The filled quantity is the lesser of the two remaining quantities.
    2.2.3 A fully filled resting order is removed.
  2.3 Matching continues until no eligible sells remain or the buy is fully filled.
  2.4 Any remaining quantity rests in the book.
  2.5 Selling follows the same logic against the buy side.

3 The best buy price must always be strictly less than the best sell price.
  3.1 If this fails, a match was missed.

4 Total filled plus resting plus cancelled must equal total placed.
  4.1 If this fails, quantity was created or destroyed.
```


### Trace Layer

```
Scenario: basic matching

1 Starting state:
  1.1 Book has no buys, no sells, no fills.

2 Alice places a buy, 10 units at 100.
  2.1 No sells exist, no match possible.
  2.2 The order rests.
  2.3 Book now has buys [Alice: 10 at 100], sells empty.

3 Bob places a sell, 6 units at 99.
  3.1 Best resting buy is Alice at 100.
  3.2 Bob's 99 is less than or equal to Alice's 100, match.
  3.3 Fill 6 at price 100, resting side's price.
  3.4 Bob is fully filled, removed.
  3.5 Alice reduced from 10 to 4.
  3.6 Book now has buys [Alice: 4 at 100], sells empty.
  3.7 Fills so far: 6 units at 100 between Alice and Bob.
  3.8 Best buy 100, no sells, no crossing possible, holds.
  3.9 Conservation: 6 filled plus 4 resting equals 10 placed, holds.

4 Carol places a sell, 5 units at 101.
  4.1 Best resting buy is Alice at 100.
  4.2 Carol's 101 exceeds Alice's 100, no match.
  4.3 Carol rests.
  4.4 Book now has buys [Alice: 4 at 100], sells [Carol: 5 at 101].
  4.5 Best buy 100 is less than best sell 101, no crossing, holds.
  4.6 Conservation: 6 filled plus 4 plus 5 resting equals 15.
    4.6.1 Total placed is 10 plus 6 plus 5 equals 21.
    4.6.2 Each fill consumes from both sides: 6 from Alice's buy, 6 from Bob's sell.
    4.6.3 Consumed: 6 plus 6 equals 12, remaining: 4 plus 5 equals 9, total: 21, holds.


Scenario: Risk timeout with exposure cleanup

1 Starting state:
  1.1 Gateway has no pending orders.
  1.2 Risk knows account A with exposure 1000 and limit 5000.
  1.3 Book is empty.

2 Client submits a buy, 10 units at 100 for account A.
  2.1 Gateway validates format, acceptable.
  2.2 Gateway holds the order as pending.
  2.3 Gateway forwards to Risk and starts the 50ms timer.

3 Risk receives the check.
  3.1 Current exposure for A is 1000.
  3.2 Order value is 10 times 100, which is 1000.
  3.3 New exposure would be 2000.
  3.4 2000 is within the limit of 5000.
  3.5 Risk updates A's exposure to 2000.
  3.6 Risk sends clearance, but the network is slow.

4 The timer fires at 50ms, before clearance arrives.
  4.1 Gateway rejects the order to the client as a timeout.
  4.2 Gateway removes the order from pending.
  4.3 Gateway sends a cancellation to Risk for this order.

5 Risk receives the cancellation.
  5.1 Risk subtracts the reserved 1000 from A's exposure.
  5.2 A's exposure returns to 1000.
  5.3 Exposure is non-negative, holds.
  5.4 No live orders remain, exposure reflects reality, holds.

6 The late clearance arrives at Gateway.
  6.1 Gateway looks up the order, not found in pending.
  6.2 Gateway discards the message.
  6.3 The order never reaches the Book.

7 Final state:
  7.1 Gateway has no pending orders.
  7.2 Risk has account A at exposure 1000, same as start, clean recovery.
  7.3 Book is empty, no spurious orders.


Scenario: partial fill then cancel

1 Starting state:
  1.1 Book has one resting sell, Carol 10 units at 100.

2 Alice's buy arrives, 7 units at 100.
  2.1 Best sell is Carol at 100.
  2.2 Alice's price 100 meets Carol's price 100, match.
  2.3 Fill 7 at price 100 between Alice and Carol.
  2.4 Alice is fully filled.
  2.5 Carol is reduced from 10 to 3.
  2.6 Book now has no buys, sells have Carol 3 at 100.
  2.7 No buys exist so no crossing is possible, holds.

3 Carol cancels her remaining order.
  3.1 Carol's 3 units are removed from the book.
  3.2 Book is now empty.
  3.3 Conservation check:
    3.3.1 Carol placed 10, filled 7, cancelled 3, 7 plus 3 equals 10, holds.
    3.3.2 Alice placed 7, filled 7, 7 equals 7, holds.
  3.4 This confirms cancellations must be tracked in the conservation rule.
    3.4.1 The original phrasing "filled plus resting equals placed" would break here.
    3.4.2 Revised to "filled plus resting plus cancelled equals placed."
```


## The Workflow

1 Define the system in Points.
  1.1 Describe components, state, behaviors, invariants, and decisions.

2 An agent writes traces against the definitions.
  2.1 It steps through scenarios, deriving state and checking invariants.
  2.2 It branches to explore failures and edge cases.
  2.3 It discovers bugs and records findings.

3 Findings feed back into the definitions.
  3.1 Invariants are tightened.
  3.2 Missing behaviors are added.
  3.3 Decisions are revisited if findings warrant it.

4 Once definitions are stable, agents generate real code.
  4.1 Definitions inform the architecture and interfaces.
  4.2 Traces become test cases.
  4.3 The source code handles implementation details that Points intentionally omits.

5 Points remains the living description of the system.
  5.1 When the system changes, Points is updated first.
  5.2 Agents re-trace to verify the change is sound before touching code.
