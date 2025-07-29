# Towards a Periodic Table of System Design Principles

This repo is a living periodic table of general-purpose design principles distilled from multiple domains in computer systems, including operating systems, databases, architecture, networking, distributed systems, and PL/compilers. The goal is to make recurring ideas easier to name, teach, compare, and reuse.

Across different domains in computer systems, the same design principles may be described in different ways. This repository offers a **shared vocabulary** so that students, researchers, and practitioners can better recognize connections across domains and discuss design principles with greater clarity.


# The Periodic Table

## Table of Contents

- [🟪 Group 1: Structure](#group-1-structure)  
  *How to carve and connect parts with clear boundaries and extension points.*
- [🟧 Group 2: Efficiency](#group-2-efficiency)  
  *Do less work—or do it cheaper—by focusing effort where it pays.*
- [🟨 Group 3: Semantics](#group-3-semantics)  
  *Specify behavior and interfaces precisely.*
- [⬜️ Group 4: Distribution](#group-4-distribution)  
  *Coordinate work and data across distributed architectures.*
- [🟩 Group 5: Planning](#group-5-planning)  
  *Select plans automatically from goals, costs, and constraints.*
- [🟦 Group 6: Operability](#group-6-operability)  
  *Observe, adapt, and evolve running systems with minimal disruption.*
- [🟥 Group 7: Reliability](#group-7-reliability)  
  *Stay correct under faults, concurrency, and partial failure.*
- [🟫 Group 8: Security](#group-8-security)  
  *Bound authority and enforce isolation to preserve safety and integrity.*

**Legend:** `Code` = unique short symbol, `Name` = principle, `Intent` = short description.

## 🟪 Group 1: Structure

🟪 **Mo – Modularity**

Partition the system into cohesive units with minimal interfaces, so that each unit can be reasoned about, replaced, or evolved independently. This principle focuses on decomposition: choosing boundaries to favor clear separation of concerns so that each responsibility sits in one module.

**Example:** The OSI model decomposes communication into standardised layers with well-defined boundaries that permit independent development and substitution [48].

🟪 **Co – Composability**

Design components that can be safely and flexibly recombined; rely on explicit contracts and type-constrained interfaces so that every legal composition remains correct, letting components be assembled like interchangeable bricks. Unlike modularity, this principle focuses on re-composition: making sure the components can be combined safely and flexibly.

**Example:** Unix programs (e.g., grep, sort, uniq) read from stdin and write to stdout, letting the user compose complex text processing pipelines [41].

🟪 **Ex – Extensibility**

Design systems to allow safe user-defined extensions, such as plug-ins, without requiring changes to the system core. When extensions come from untrusted parties, isolate them through sandboxing to preserve safety.

**Example:** Unix also illustrates extensibility: new programs can be added by the user without kernel changes [41].

🟪 **Pm – Policy/Mechanism Separation**

Separate what should be done (policy) from how it is carried out (mechanism) by exposing a common interface through which multiple policies can plug into the same mechanism.

**Example:** Hydra has a kernel of generic mechanisms (scheduling, paging, protection) and moved resource-allocation policies to user-level modules [32].

🟪 **Gr – Generalized Design**

Design a single core with explicit variation points like types, knobs, or plug-ins, so that it can serve many use cases without duplication, but specialise when doing so yields meaningful gains in performance, accuracy, or clarity.

**Example:** The C++ Standard Template Library is a collection of containers, iterators, and algorithms parameterized by templates [45]. Postgres allows users to add types and operators to the core database system [46].

## 🟧 Group 2: Efficiency

🟧 **Sc – Scalability**

Design the system to handle growth in data, traffic, or nodes with near-linear cost or latency.

**Example:** MapReduce scales across nodes by dividing work into parallel tasks and aggregating results with minimal coordination [10].

🟧 **Rc – Reuse of Computation**

Avoid redundant work by caching, materializing intermediate results (e.g., indexes), or incrementally updating outputs across repeated or slightly modified inputs, saving computation.

**Example:** A B+tree reuses its sorted key order: lookups follow the existing search path instead of rescanning the entire data set each time, thereby reusing computation [2].

🟧 **Wv – Work Avoidance**

Skip computation that would not alter externally observable results. Examples include lazy evaluation and predicate short-circuiting.

**Example:** Lazy evaluation defers work until a value is demanded, eliminating useless computation [19].

🟧 **Cc – Common-Case Specialization**

Detect the execution paths or data items that dominate run-time ("hot spots") and create a streamlined fast path just for them, while a slower, general path still handles every case correctly.

**Example:** Caching the target method for the receiver class on the first call, so that subsequent calls on that common receiver hit the fast path; uncommon classes fall back to the full method-lookup routine [5].

🟧 **Bo – Bottleneck-Oriented Optimisation**

Profile end-to-end performance, locate the tightest resource constraint, and focus improvement effort there until another stage becomes the limiter.

**Example:** Rare 99th-percentile stragglers bottleneck latency, and replicated requests help cut tail response times [9].

🟧 **Ha – Hardware-Aware Design**

Shape algorithms and data structures to the latency, bandwidth, parallelism, and persistence properties of underlying hardware (e.g., cache hierarchy, NUMA, SSDs, GPUs).

**Example:** BLAS defines cache- and vector-tuned kernels so linear-algebra code exploits hardware efficiently [31].

🟧 **Op – Optimistic Design**

Proceed as if the common case will succeed, skipping coordination, and rely on a (possibly expensive) recovery path only when that assumption proves wrong.

**Example:** Optimistic Concurrency Control runs transactions lock-free, then validates at commit and rolls back only when a conflict is detected [24].

🟧 **La – Learned Approximation**

Replace hand-crafted algorithms with models trained on data, trading bounded inaccuracy for efficiency or flexibility.

**Example:** The perceptron branch predictor learns weights online to forecast branch outcomes, outperforming fixed two-bit counters without enlarging the table [22].


## 🟨 Group 3: Semantics

🟨 **Al – Abstraction Lifting**

Wrap low-level operations behind a higher-level interface or domain-specific language that expresses intent rather than steps. This enables internal optimization and also allows a single definition to target diverse back-ends.

**Example:** SQL queries declare the result to retrieve; the DBMS chooses access paths, join orders, and physical operators automatically \[44].

🟨 **Lu – Language Homogeneity**

Adopt a single, well-specified intermediate representation (or language) across core components and extensions so semantics align, tools compose, and cross-layer optimisations and reuse happen with minimal effort.

**Example:** LLVM exposes a typed, SSA-based IR that many front ends target and many back ends share, enabling cross-language optimisation and reuse of the same middle-end passes \[30].

🟨 **Se – Semantically Explicit Interfaces**

Specify an interface precisely (covering effect visibility, ordering, durability, etc.) so that users can reason about a call’s true externally observable state without guessing about hidden buffering or replication.

**Example:** SQL isolation levels specify precise anomaly semantics and make visibility guarantees explicit \[3].

🟨 **Fs – Formal Specification**

Describe system behaviour using mathematical models or logic to support rigorous reasoning, verification, or synthesis. Mechanisms for realizing this principle include temporal logic, state machines, and other formalisms that make system properties analyzable.

**Example:** TLA+ shows how to specify and check systems using logic and set theory to catch design errors before coding \[27].

🟨 **Ig – Invariant-Guided Transformation**

Use formally stated invariants to drive safe refactoring, optimisation, or reconfiguration.

**Example:** In compilers, SSA treats "one definition per name" as an IR invariant; passes rewrite code while preserving semantics and then re-establish SSA \[8]. In query optimisers, relational-algebra equivalences (e.g., selection/projection pushdown) preserve result semantics \[44].

## ⬜️ Group 4: Distribution

⬜️ **Lt – Location Transparency**

Hide the physical whereabouts of resources so clients interact via uniform names or handles.

**Example:** Programs can call remote procedures as if they were local, masking host location \[4].

⬜️ **Dc – Decentralised Control**

Distribute decision-making among many nodes to avoid single points of failure or bottlenecks.

**Example:** Dynamo partitions data via consistent hashing and uses gossip-based membership, avoiding any central coordinator \[12].

⬜️ **Fp – Function Placement**

Place functionality where the necessary context and resources exist to achieve correctness and efficiency, avoiding redundant work elsewhere.

**Example:** The end-to-end argument shows that functions like reliability checks achieve correctness only at the endpoints \[42].

⬜️ **Lo – Locality of Reference**

Place related data and operations close together in time and space to preserve access patterns and minimize separation between computation and state.

**Example:** The working-set model formalises temporal locality to keep hot pages in memory \[11].

## 🟩 Group 5: Planning

🟩 **Ep – Equivalence-based Planning**

Apply algebraic/logic rewrite rules over a common IR that preserve semantic equivalence; defer final choice to later cost/constraint stages.

**Example:** Starburst’s rule-based rewrite system applies relational equivalences (e.g., predicate pushdown) to generate logically equivalent queries \[39].

🟩 **Cm – Cost-based Planning**

When a system must choose among alternative designs, configurations, or execution strategies, use a cost model to guide the search toward low-cost solutions (energy, money, etc.) without needing to enumerate the full space.

**Example:** The Selinger query optimizer selects the lowest-cost plan under a cost model \[44].

🟩 **Cp – Constraint-based Planning**

Encode decisions and hard or soft constraints and rely on a solver (ILP/SMT etc.) to find a feasible or optimal assignment.

**Example:** Quincy formulates cluster scheduling as a min-cost flow with locality and fairness constraints and solves it to obtain an assignment \[21].

🟩 **Gd – Goal-Directed Planning**

Accept a declarative description of the desired end-state and automatically synthesise a concrete sequence of operations to reach it, shielding the user from implementation details.

**Example:** The Cascades query optimizer turns an SQL query (the goal) into an executable plan via rule-based transformation and cost-guided search \[14].

🟩 **Bb – Black-Box Tuning**

When analytic cost models are not available, search the plan/configuration space by measuring candidates on the target system, iteratively choosing better ones (e.g., heuristic or Bayesian search), and caching the winner.

**Example:** ATLAS empirically times candidate BLAS kernel configurations on the target CPU and fixes the best-performing parameters, without an analytic cost model \[47].

🟩 **Ah – Advisory Hinting**

Provide non-binding hints that systems may exploit to improve performance, without changing correctness or requiring enforcement.

**Example:** Lampson advocates optional "hints" that help performance but must not affect correctness if ignored \[29].

## 🟦 Group 6: Operability

🟦 **Ad – Adaptive Processing**

Monitor runtime conditions and automatically adjust parameters or strategy.

**Example:** Eddies continuously reorder query operators at runtime based on feedback, adapting without stopping execution \[1].

🟦 **Ec – Elasticity**

Automatically adjust resource allocation in response to shifting demand and cost goals. Examples include predictive autoscaling and load shaping.

**Example:** Chase et al. dynamically provision servers based on load and utility, exemplifying elastic resource management \[6].

🟦 **Wa – Workload-Aware Optimisation**

Continuously observe workload shape (skew, locality, access frequency, etc.), and adapt data layouts, algorithm choices, or resource allocations to match current patterns.

**Example:** Database "cracking" incrementally reorganises column data based on query predicates, adapting the data layout continuously to the observed workload \[20].

🟦 **Au – Automation and Autonomy**

Let the system perform routine or reactive tasks without human intervention, often by learning from traces or user-provided examples.

**Example:** AutoAdmin automatically recommends indexes/materialized views from workload traces \[7]. Programming-by-example systems automate tasks by generalizing from a few user-provided examples \[33].

🟦 **Ho – Human Observability**

Expose internal state of the system, like metrics, traces, plans, to make the system intentionally transparent; that transparency improves observability, debugging, introspection, and control.

**Example:** Paxson’s end-to-end Internet packet dynamics analysis demonstrates how rich measurement and tracing enable informed debugging and tuning \[37].

🟦 **Ev – Evolvability**

Design so the system can change with minimal downtime or rewrites and do so without breaking external contracts or observable behaviour for existing clients. Unlike extensibility that lets outsiders add new behavior via defined hook points without touching the core, evolvability lets the system’s internals change over time without breaking existing external contracts.

**Example:** Parnas presents how a modular design makes system easier to extend without disruptive rewrites \[36].

## 🟥 Group 7: Reliability

🟥 **Ft – Fault Tolerance**

Design the system to continue operating, perhaps in degraded form, despite component failures.

**Example:** Gray’s analysis of why computers stop shows that replication and automatic restart let services keep running through hardware and software faults \[15].

🟥 **Is – Isolation for Correctness**

Prevent unintended interference among components so local reasoning remains valid.

**Example:** Two-phase row-level locking stops one transaction from reading or overwriting another’s uncommitted data, preserving isolation guarantees \[16].

🟥 **At – Atomic Execution**

Group multiple operations so they appear indivisible, either all take effect or none do.

**Example:** With Transactional Memory, memory operations inside a transaction speculatively execute, then commit atomically; if any conflict or fault occurs, the entire block aborts and leaves no partial state \[18].

🟥 **Cr – Consistency Relaxation**

Deliberately relax strong consistency or ordering constraints, but only within documented bounds, to improve performance, availability, or concurrency.

**Example:** Bayou lets mobile clients update replicas while disconnected, guaranteeing eventual convergence when replicas reconnect, trading strict consistency for offline availability \[38].

## 🟫 Group 8: Security

🟫 **Sy – Security via Isolation**

Enforce strong boundaries so faults or hostile code cannot affect other components.

**Example:** A correct virtual machine monitor presents each guest with a complete, isolated machine and intercepts privileged operations, preventing one guest from compromising others or the host \[40].

🟫 **Ac – Access Control and Auditing**

Define permissions and log every access for accountability.

**Example:** Lampson’s taxonomy of access-control lists, capabilities, and audit trails underpins modern security mechanisms \[28].

🟫 **Lp – Least Privilege**

Grant only minimal authority needed for a task, shrinking the blast radius.

**Example:** The post-mortem on the 1988 Internet Worm shows how excess privilege let the worm spread and spurred widespread adoption of least-privilege daemons \[35].

🟫 **Tq – Trust via Quorum**

Rely on agreement from multiple, independent participants rather than a single authority.

**Example:** Paxos algorithm replicates state across a majority quorum so the service stays correct even if minority nodes crash or act maliciously \[26].

🟫 **Cf – Conservative Defaults**

Ship with restrictive, safe settings; let experts opt-in to riskier, faster modes.

**Example:** With a "default no-access" policy, every protection mechanism should allow access only when explicitly granted \[43].

🟫 **Sa – Safety by Construction**

Structure code or data so entire classes of errors become impossible rather than merely detected.

**Example:** Rust’s ownership and borrow checker prevent data races and dangling pointers at compile time \[34].

# How to contribute

We welcome PRs that add, refine, or re-group principles, and PRs that add "classic" papers as examples. Open an issue describing:
  - **What** you are adding or changing.
  - **Why** (include a rationale for the orthogonality and generality of the principle).
  - **Citations** (classic papers preferred).
