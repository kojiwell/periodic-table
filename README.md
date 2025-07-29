# Towards a Periodic Table of System Design Principles

This repo is a living periodic table of general-purpose design principles distilled from multiple domains in computer systems, including operating systems, databases, architecture, networking, distributed systems, and PL/compilers. The goal is to make recurring ideas easier to name, teach, compare, and reuse.

Across different domains in computer systems, the same design principles may be described in different ways. This repository offers a **shared vocabulary** so that students, researchers, and practitioners can better recognize connections across domains and discuss design principles with greater clarity.

---

## The Table

**Legend:** `Code` = unique short symbol, `Name` = principle, `Intent` = short description.

### 1) 🟪 Structure

🟪 Mo – Modularity

Partition the system into cohesive units with minimal interfaces, so that each unit can be reasoned about, replaced, or evolved independently. This principle focuses on decomposition: choosing boundaries to favor clear separation of concerns so that each responsibility sits in one module.
Example: The OSI model decomposes communication into standardised layers with well-defined boundaries that permit independent development and substitution [48].

🟪 Co – Composability

Design components that can be safely and flexibly recombined; rely on explicit contracts and type-constrained interfaces so that every legal composition remains correct, letting components be assembled like interchangeable bricks. Unlike modularity, this principle focuses on re-composition: making sure the components can be combined safely and flexibly.
Example: Unix programs (e.g., grep, sort, uniq) read from stdin and write to stdout, letting the user compose complex text processing pipelines [41].

🟪 Ex – Extensibility

Design systems to allow safe user-defined extensions, such as plug-ins, without requiring changes to the system core. When extensions come from untrusted parties, isolate them through sandboxing to preserve safety.
Example: Unix also illustrates extensibility: new programs can be added by the user without kernel changes [41].

🟪 Pm – Policy/Mechanism Separation

Separate what should be done (policy) from how it is carried out (mechanism) by exposing a common interface through which multiple policies can plug into the same mechanism.
Example: Hydra has a kernel of generic mechanisms (scheduling, paging, protection) and moved resource-allocation policies to user-level modules [32].

🟪 Gr – Generalized Design

Design a single core with explicit variation points like types, knobs, or plug-ins, so that it can serve many use cases without duplication, but specialise when doing so yields meaningful gains in performance, accuracy, or clarity.
Example: The C++ Standard Template Library is a collection of containers, iterators, and algorithms parameterized by templates [45]. Postgres allows users to add types and operators to the core database system [46].

### 2) 🟧 Efficiency

🟧 Sc – Scalability
Design the system to handle growth in data, traffic, or nodes with near-linear cost or latency.
Example: MapReduce scales across nodes by dividing work into parallel tasks and aggregating results with minimal coordination [10].

🟧 Rc – Reuse of Computation
Avoid redundant work by caching, materializing intermediate results (e.g., indexes), or incrementally updating outputs across repeated or slightly modified inputs, saving computation.
Example: A B+tree reuses its sorted key order: lookups follow the existing search path instead of rescanning the entire data set each time, thereby reusing computation [2].

🟧 Wv – Work Avoidance
Skip computation that would not alter externally observable results. Examples include lazy evaluation and predicate short-circuiting.
Example: Lazy evaluation defers work until a value is demanded, eliminating useless computation [19].

🟧 Cc – Common-Case Specialization
Detect the execution paths or data items that dominate run-time ("hot spots") and create a streamlined fast path just for them, while a slower, general path still handles every case correctly.
Example: Caching the target method for the receiver class on the first call, so that subsequent calls on that common receiver hit the fast path; uncommon classes fall back to the full method-lookup routine [5].

🟧 Bo – Bottleneck-Oriented Optimisation
Profile end-to-end performance, locate the tightest resource constraint, and focus improvement effort there until another stage becomes the limiter.
Example: Rare 99th-percentile stragglers bottleneck latency, and replicated requests help cut tail response times [9].

🟧 Ha – Hardware-Aware Design
Shape algorithms and data structures to the latency, bandwidth, parallelism, and persistence properties of underlying hardware (e.g., cache hierarchy, NUMA, SSDs, GPUs).
Example: BLAS defines cache- and vector-tuned kernels so linear-algebra code exploits hardware efficiently [31].

🟧 Op – Optimistic Design
Proceed as if the common case will succeed, skipping coordination, and rely on a (possibly expensive) recovery path only when that assumption proves wrong.
Example: Optimistic Concurrency Control runs transactions lock-free, then validates at commit and rolls back only when a conflict is detected [24].

🟧 La – Learned Approximation
Replace hand-crafted algorithms with models trained on data, trading bounded inaccuracy for efficiency or flexibility.
Example: The perceptron branch predictor learns weights online to forecast branch outcomes, outperforming fixed two-bit counters without enlarging the table [22].

---

## How to contribute

We welcome PRs that add, refine, or re-group principles, and PRs that add "classic" papers as examples. Open an issue describing:
  - **What** you are adding or changing.
  - **Why** (include a rationale for the orthogonality and generality of the principle).
  - **Citations** (classic papers preferred).
