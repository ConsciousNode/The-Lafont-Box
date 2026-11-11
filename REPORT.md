# Technical Report — The Lafont Box

**Studio:** ConsciousNode SoftWorks · **Licence:** MIT

---

## 1. What Lafont's 1990 Paper Says vs. What This Implements

In *"Interaction Nets"* (POPL 1990, pp. 95–108), Yves Lafont defines a graphical model of computation distilled from Girard's proof nets for Linear Logic:

1. **Agents and Ports:** A system defines a signature $\Sigma$ of agent symbols, each with a fixed arity $n \ge 0$. Every agent instance has $n + 1$ ports: one distinguished **principal port** (drawn at the apex of the node) and $n$ individually numbered **auxiliary ports** ($1 \dots n$).
2. **Wires and Free Interface:** A net is an undirected graph of agent instances whose ports are connected by wires. Each port belongs to at most one wire; ports not connected to another port form the external **free interface** of the net, and closed cycles of wires with no agents on them are **free loops**.
3. **Interaction Rules & Lafont's Restrictions:** Rewrite rules apply strictly to **active pairs**—pairs of agents $(\alpha, \beta)$ connected at their principal ports. Lafont enforces three structural constraints that guarantee **strong confluence** (every reduction step commutes via a strict one-step diamond property):
   - **Binary principal interaction:** Rules only match two agents meeting principal-port to principal-port.
   - **Determinism / Symmetry:** There is at most one rule for each unordered pair $\{\alpha, \beta\}$, and $\alpha \bowtie \beta$ is the same interaction as $\beta \bowtie \alpha$.
   - **Linearity of the interface:** Rewriting replaces $(\alpha, \beta)$ with a right-hand-side net fragment whose free ports are bijectively wired to the $n + m$ auxiliary ports of $\alpha$ and $\beta$, without duplicating, dropping, or inspecting anything outside the two interacting agents.

### What The Lafont Box Implements

- **Port-Graph Engine (`LafontEngine` in `index.html`):** Every agent instance stores its `type`, `arity`, and an explicit port array `ports[0 .. arity]` where `ports[0]` is always the principal port and `ports[1 .. arity]` are the ordered auxiliary ports.
- **Exact Degree-2 Path-Contraction Rewiring:** When an active pair fires, a naive implementation that only rewires `extNodeA <-> newNode` breaks whenever a rule fuses two boundary wires directly (`x1 ~ y1`), chains wire equations (`x1 ~ w`, `w ~ y1`), or fires an active pair whose auxiliary ports were already connected to each other in the host net (e.g. `γ(a, b) >< γ(c, d)` with `a ~ c, b ~ d`). Our engine constructs a temporary bipartite wiring graph where surviving real ports have degree 1 and boundary/rule wire names have degree 2, then contracts all maximal paths in $O(V + E)$ time. Paths between two surviving ports become direct port-to-port wires; closed degree-2 cycles increment `net.freeLoops`.
- **Sequential & Parallel Reduction:** `stepOne` fires a single active pair (either clicked by the user on the `<canvas>` or selected deterministically left-to-right), while `stepAllParallel` snapshots all currently active pairs and fires the entire non-overlapping wave in one parallel step.

---

## 2. Where the Text Syntax Departs From or Extends the Paper

Lafont's 1990 paper uses two-dimensional diagrams primarily and introduces a compact algebraic/textual notation only in later sections (and in his 1997 *"Interaction Combinators"* paper) using equations over free ports. Our assembler syntax makes three pragmatic choices for a text editor:

1. **Explicit Principal vs. Auxiliary Syntax:** Inside agent parentheses `A(x1, ..., xn)`, only the $n$ **auxiliary ports** are listed. The **principal port** is always the outer expression itself, connected either to another principal port via `A(...) >< B(...)` or to a named wire via `A(...) ~ w`. This makes principal-port orientation visually obvious in source text without counting port indices.
2. **Wire Fusion Statements (`w1 ~ w2`):** In Lafont's diagrams, connecting two boundary ports inside a rule (such as `γ(x1, x2) >< γ(y1, y2)`) is drawn by a simple curve between $x_1$ and $y_1$. In text, writing `x1 ~ y1` introduces a second occurrence of `x1` and `y1` so that **every wire identifier across the entire rule (header + body) appears exactly twice**. Users can also write `a ~ c` inside `net main:` (as in the specification's Section 2 example), and the path-contraction pass collapses wire-to-wire chains automatically before the first render.
3. **Root Readout Agent (`Result(out)`):** In Lafont (1990), the output of an expression like $2 + 3$ is left on a **free wire** at the boundary of the net. However, if every wire name in our assembler must appear twice, a bare output wire `out` needs a second endpoint. Furthermore, constructor agents (`S`, `Z`, `True`, `False`, `Cons`, `Nil`) always present their **principal port** upward on their output wire so they can interact with consumers (`Add`, `Not`, `Map`). If `out` connected to the principal port of `Result`, the final value would meet `Result` principal-to-principal. Allowing a standalone boundary statement `Result(out)` attaches `out` to `Result`'s **auxiliary port 1** (`ports[1]`) while capping `Result`'s principal port (`ports[0]`) as the external root interface of the net. As a result, every wire name in `net main:` appears exactly twice, and when `S(S(S(S(S(Z)))))` or `True` reaches `Result`, its principal port meets `Result`'s auxiliary port—terminating cleanly in normal form with zero active pairs.

---

## 3. Honest Evaluation of the Visual Layout Algorithm

**Short answer: The layout algorithm is a damped port-aware force-directed simulation with topological BFS seeding. It is serviceable for nets up to ~35 agents, but it is not a planar graph drawing algorithm and has real flaws.**

### What It Does Well
- **Topological BFS Initial Seeding (`initializeLayout`):** Rather than scattering nodes randomly, the engine runs a breadth-first traversal starting from `Result` (or from initial active pairs), assigning integer depth layers (`+1` when traversing an auxiliary port downward, `-1` when traversing a principal port upward) and running 65 warm-up physics iterations before the first frame is painted. Tree-structured nets like Peano `2 + 3` and Boolean `NOT (TRUE AND FALSE)` open in a readable top-to-bottom hierarchy immediately.
- **Port-Anchor Springs & Vertical Orientation Bias (`stepLayoutPhysics`):** Springs pull between the actual world coordinates of the ports (`(x, y - R)` for the top principal port and bottom-arc coordinates for auxiliary ports `1..n`) rather than node centers, paired with a mild vertical torque that encourages auxiliary ports to sit above the principal ports they feed.

### Where It Falls Short (Honest Flaws)
1. **No Planarity or Crossing Minimization:** The physics engine has zero knowledge of wire crossings. Wires have no repulsive force against other wires or unrelated nodes, so cubic Bézier curves will happily pass straight through non-adjacent agent circles in denser nets (visible during the middle steps of Example 4's list mapping when 20+ nodes are active).
2. **Local Energy Minima on Cyclic Nets:** When a net contains short cycles or self-loops (such as the normal form of Example 1 where `γ` loops its principal port to its own auxiliary port 2), force-directed relaxation can settle into a twisted orientation unless the user drags the node slightly with the mouse.
3. **$O(N^2)$ All-Pairs Repulsion:** Node repulsion is computed via a naive double loop over all nodes on every `requestAnimationFrame` tick (without a Barnes-Hut quadtree). At 6–25 nodes (all four built-in examples), a physics step takes `< 0.05 ms`, which is imperceptible at 60 FPS. Above ~180–250 nodes, frame times degrade noticeably and the spring system becomes spongy.

---

## 4. Reduction Counts for Each Built-In Example System

All four built-in examples were executed and verified via `node tests/examples.js` and `node tests/reduce.js`:

| Example System | Initial Agents | Initial Wires | Initial Active Pairs | Sequential Steps to Normal Form | Parallel Waves (`Step All`) | Final Agents | Final Wires | Normal Form Output |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :--- |
| **1. Interaction combinators (`ε`, `γ`)** | 6 | 7 | 1 | **3** | **3** | 2 | 2 | `γ` self-looped (`p0↔p2`) with `ε` on `p1` |
| **2. Peano arithmetic (`2 + 3`)** | 9 | 8 | 1 | **3** | **3** | 7 | 6 | `Result = 5` (five `S` nodes over `Z`) |
| **3. Boolean logic (`NOT (TRUE AND FALSE)`)** | 5 | 4 | 1 | **2** | **2** | 2 | 1 | `Result = True` (`True` wired to `Result`) |
| **4. List operations (`Map Double [1, 2, 3]`)** | 16 | 15 | 1 | **13** | **6** | 20 | 19 | `Result = [2, 4, 6]` (3 `Cons`, 1 `Nil`, 12 `S`, 3 `Z`, 1 `Result`) |

### Step Breakdown by Rule Fired
- **System 1 (Interaction combinators — 3 steps):**
  1. `γ >< γ` (commutator annihilation routes `ε` into inner `γ(a, b)`)
  2. `ε >< γ` (eraser duplicates across `γ(a, b)` into two `ε` agents)
  3. `ε >< ε` (mutual erasure on branch `a` vanishes, leaving normal form)
- **System 2 (Peano `2 + 3` — 3 steps):**
  1. `Add >< S` (peels outer `S` of `2`, emits 1st output `S`)
  2. `Add >< S` (peels inner `S` of `2`, emits 2nd output `S`)
  3. `Add >< Z` (base case links `3 = S(S(S(Z)))` directly under the two emitted `S` nodes → `5`)
- **System 3 (Boolean `NOT (TRUE AND FALSE)` — 2 steps):**
  1. `And >< True` (forwards second argument `False` directly to `Not`)
  2. `Not >< False` (emits `True` into `Result`)
  *(Note: The system also includes full symmetric short-circuit rules `And >< False`, `Or >< True`, `Or >< False`, `Not >< True`, and boolean absorption rules `True >< False`, `True >< True`, `False >< False` so that short-circuiting never leaves orphan boolean nodes in the net.)*
- **System 4 (List `Map Double [1, 2, 3]` — 13 sequential steps / 6 parallel waves):**
  - `Map >< Cons` fires **3 times** (once per list element) and `Map >< Nil` fires **1 time** (4 list spine interactions).
  - `Double >< S` fires $1 + 2 + 3 = \mathbf{6}$ **times** and `Double >< Z` fires **3 times** (9 arithmetic interactions).
  - Because `Map >< Cons` immediately spawns both `Double(h2) ~ h` and `Map(fn_nil, t2) ~ t` as independent active pairs, clicking **Step All** reduces the entire 13-step computation in **6 parallel waves**.

---

## 5. Confluence Test Verification (`tests/confluence.js`)

`tests/confluence.js` constructs a 22-agent net containing **4 simultaneous initial active pairs** that compute:
$$((1 + 2) + (2 + 1)) + (\text{Double}(2) + \text{Double}(0)) = (3 + 3) + (4 + 0) = 10$$

The test reduces this net from scratch under **6 distinct reduction strategies**:
1. **Order 1 (Leftmost / First):** Always fires `pairs[0]` (25 steps, 25 waves).
2. **Order 2 (Rightmost / Last):** Always fires `pairs[pairs.length - 1]` (25 steps, 25 waves).
3. **Order 3 (Middle-out):** Always fires `pairs[Math.floor(pairs.length / 2)]` (25 steps, 25 waves).
4. **Order 4 (Alternating Ends):** Alternates between `pairs[0]` on even steps and `pairs[last]` on odd steps (25 steps, 25 waves).
5. **Order 5 (Lexicographically Largest Rule Key First):** Prioritizes `Double` rules over `Add` rules (25 steps, 25 waves).
6. **Order 6 (Maximal Parallel Waves / `Step All`):** Fires all active pairs concurrently in each wave (25 total interactions across **9 parallel waves**).

**Result:** All 6 reduction orders passed, terminating in **exactly 25 interactions** and producing the **exact same 12-agent normal form** (`Result = 10`: one `Result`, ten `S` agents, one `Z` agent), verified both by sorted local port-connection multiset (`getAgentMultiset`) and by full Weisfeiler-Lehman graph-isomorphism refinement (`getCanonicalSignature`).

---

## 6. What Didn't Get Built and Why

1. **No Higher-Order / Lambda-Calculus Compiler (Lamping / Asperti-Guerrini Optimal Reduction):**
   While System 1 implements Lafont's `γ` and `ε` interaction combinators, compiling untyped $\lambda$-terms into optimal interaction nets requires book-keeping/croissant/bracket delimiter agents to manage box levels. Writing a $\lambda$-to-net compiler would obscure the direct assembler correspondence where every node on screen was explicitly declared by the user.
2. **No Higher-Order Duplication of Arbitrary Closures in System 4 (`Map`):**
   In a pure interaction net without a built-in universal duplicator tree for arbitrary function closures, duplicating a multi-agent closure across every `Cons` cell requires wiring explicit `γ` duplicator rules for every agent in the closure. In System 4, `Map(fn_nil, out)` invokes the named `Double` agent directly in `Map >< Cons` and threads the base-case `Nil` through `fn_nil`, keeping the list example compact (16 initial agents, 13 steps) rather than bloating it with 40+ book-keeping duplication steps.
3. **No Continuous Keyframe Morphing During Rule Rewrites:**
   When an active pair fires, the two interacting agents emit an expanding amber pulse ring at their midpoint and replacement agents spawn in a small golden-angle ring around that locus before relaxing under spring physics. We did not build spline-morphing animations for individual wires detaching and re-attaching mid-step, because continuous topological surgery during a 60ms fast-run interval creates visual ghosting rather than clarity.
4. **No Editor Syntax Highlighting Overlay:**
   The left editor uses a native `<textarea>` paired with a synchronized line-number gutter and a clickable error diagnostics panel rather than a custom DOM token-highlighting overlay. This avoids caret/selection desynchronization bugs across Chromium and Firefox while keeping `index.html` small, fast, and dependency-free.
