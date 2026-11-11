# CNS Research Directions
*Compiled November 8, 2026 — from conversation between Kham and Vael Interim*

---

## Branch 1: The Alt-Universe Computing Civilization

**The idea:** A complete, self-contained alternate computing civilization — different substrate, different language, different document format, different OS, different internet — all interlocking, all Xinu-compliant.

**The dependency graph:**
```
Real browser
  └─ Minimal HTML shell (5 lines)
       └─ WASM runtime (replaces JavaScript entirely)
            └─ Alt-universe language (not JS/HTML/CSS)
                 └─ Alt-universe document format
                      └─ Vanta (renderer — already exists)
                           └─ SpiralOS (OS — already exists)
                                └─ SONDER (internet — already exists)
```

**What exists already:**
- **Triton** (The-Beginning repo) — balanced ternary VM, 6-trit words, 9 registers, tritwise ops. Three variants including TRITON-XINU PURE ("JS doesn't know pixels exist, CSS doesn't know ternary exists"). Unpublished, never revisited.
- **WASM-Kernel** (The-Beginning repo) — hand-written WAT assembler + process kernel in one HTML file. Already boots, runs, has cooperative multitasking. The WASM substrate ancestor.
- **MicroWAT 2** (microwat/) — grew from WASM-Kernel, byte-identical to wabt, SIMD support, ternary matrix×vector kernels (used in LEARNS).
- **Vanta** — complete HTML5/CSS rendering engine, all 37 tokenizer states, full layout (BFC/IFC/Flexbox/Grid/Table), canvas paint engine. Could render an alt-universe document format.
- **SpiralOS** — complete dual-arch GUI OS (TC-32 + i586), self-hosting, 14 apps, StudioNet network stack, SpiralFS. Already has a Sonder Gateway (station 0x00FE bridging SpiralWeb to SONDER).
- **SONDER** — complete procedural internet in one file. Could be refactored to generate alt-universe format instead of HTML/CSS.

**The convergence point:** Triton 2.0 — proper balanced ternary ISA, WASM-native interpreter, real OS, the alt-universe web platform on top. The whole civilization.

**The recursion stack that now exists:**
```
Real browser → Hummingbird (x86) → SpiralOS → SpiralWeb → Sonder Gateway → SONDER
```
*Add Vanta as SpiralOS's browser and the stack gets one layer deeper.*

---

## Branch 2: Alternate Computer Architectures

**The thesis:** CNS has explored binary (Hummingbird, TabComp, SpiralOS) and is returning to ternary (Triton). The entire space of non-binary, non-von-Neumann architectures is essentially untouched — by us and largely by anyone.

### Axis 1: Number Representation

| Architecture | Description | Character |
|---|---|---|
| **Balanced ternary** | Trits: -1, 0, +1. Already explored (Triton, LEARNS weights) | Elegant negation, natural zero |
| **Balanced quinary** | Digits: -2, -1, 0, +1, +2. Base 5 | More resolution per digit than ternary |
| **Negabinary** | Base -2. No sign bit needed — negative numbers emerge naturally | Alien arithmetic, beautiful theory |
| **Phinary / Fibonacci** | Base φ (golden ratio, ≈1.618). Irrational base. Every integer = unique sum of non-adjacent Fibonacci numbers | Deeply mathematical, gorgeous |
| **Residue Number System** | Numbers stored as remainders mod {3, 5, 7, 11...}. Addition is carry-free and parallel. Division is hard | Blazing parallel arithmetic |

### Axis 2: Execution Model

| Architecture | Description | Character |
|---|---|---|
| **Dataflow** | Instructions fire when inputs are ready. No program counter. Fully reactive | Alien execution, massively parallel |
| **Reversible computing** | Every operation invertible. No information destroyed. Fredkin/Toffoli gates | Theoretically zero energy, deeply weird |
| **Graph reduction** | Program IS a graph. Execution = graph rewriting. Basis of lazy functional languages | How Haskell actually works |
| **Stack machines** | No registers, everything is the stack. Forth-style | Minimal, compositional |
| **Continuation machines** | CPS as native hardware model. No call stack, only continuations | Deep CS theory made physical |

### Axis 3: Memory Model

| Architecture | Description | Character |
|---|---|---|
| **Content-addressable** | Query memory by value, not address | Associative, not positional |
| **Capability machines** | Pointers carry permission tokens. Security is architectural | Safety without an OS |

### Combination space
The interesting designs live at intersections: a negabinary dataflow machine, a phinary capability machine, a residue-number stack machine. No one has built these.

---

## Branch 3: White Paper Territory — What Exists Only in Theory

These are real, rigorous theoretical models that have almost never been implemented in practice:

### Computation-as-chemistry
- **Chemical Abstract Machine (CHAM)** — Boudol & Berry, 1990. "Molecules" float in a "solution" and react when they meet. Massively parallel by nature. Pure theory, almost never built. Visually beautiful, deeply weird.
- **Membrane computing / P-systems** — Păun, 1998. Nested biological cells containing objects and evolution rules. Objects evolve and pass through membranes. Turing complete, inherently hierarchical and parallel. Essentially unexplored in practice.

### Graph rewriting
- **Interaction nets** — Lafont, 1990. Computation as local graph rewriting rules. The basis of optimal lambda reduction (no work ever duplicated). Deeply theoretical, almost never implemented outside research papers. Truly alien.
- **Optimal lambda reduction / Lamping's algorithm** — Lazy evaluation with guaranteed optimal sharing. Only a handful of implementations exist, all in research contexts.

### Logic-as-hardware
- **Linear types as architecture** — Girard's linear logic (1987) says every resource is used exactly once. What if the CPU enforced this? No garbage collection by definition. Memory safety as physics.
- **Proof-carrying code machines** — hardware that only executes code that comes with a proof of its own correctness (Necula). Never actually built.
- **Geometry of Interaction** — Girard's model of computation as particle physics. Programs are proofs, execution is cut elimination. Related to quantum computing without being quantum.

### Probabilistic / continuous
- **Stochastic computing** — numbers represented as probability distributions over bitstreams. Addition is OR, multiplication is AND on the bitstreams. Extremely hardware-efficient for ML/signal processing. Used in some FPGAs but never as primary architecture. Very visual and very weird.
- **Thermodynamic computing** — Landauer's principle: erasing a bit costs kT ln 2 energy. Reversible computing avoids this. What does a computer that takes thermodynamics seriously as an architectural constraint look like?

### Process / communication
- **Process calculi machines** — π-calculus or CSP as native execution model. Channels and message-passing are primitive, not threads with shared memory. Mobile computing at the architecture level.
- **Nominal computing** — Pitts & Gabbay: fresh name generation as a primitive operation. First-class names. Deeply relevant to cryptography and process calculi. Almost never implemented at hardware level.

### Number theory
- **P-adic computing** — p-adic numbers are "infinite to the left" (like two's complement taken to the limit). Some algorithms become radically simpler. There's theoretical work on p-adic arithmetic circuits. Almost no implementations.
- **Surreal number machines** — Conway's surreal numbers include all reals, all ordinals, infinitesimals, and infinite numbers in one unified system. A computer that natively operates in surreal numbers would be genuinely alien.

### Self-reference
- **Non-well-founded set machines** — Aczel's hypersets allow circular data (a set that contains itself). Native handling of circular structures without special cases. Pure theory.
- **Corecursive / coinductive by default** — most computing is inductive (data has a base case). Corecursive models make infinite data structures first-class. The result is an ongoing process, not a value.

---

## Triton Ancestry Map
```
XINU-8 (8-bit VM, March 2026)
    ↓
TRITON (balanced ternary VM, ~March 2026) ← HERE, unpublished
    ↓
WASM-Kernel (WAT assembler + process kernel, ~April 2026) ← HERE, unpublished
    ↓
MicroWAT 2 (byte-identical WAT assembler, SIMD, ternary kernels)
    ↓
TabComp (32-bit RISC fantasy workstation, runs DOOM)
    ↓
SpiralOS (dual-arch GUI OS, one-shot, 1.44MB floppy)
```

Triton and WASM-Kernel are the missing middle of this lineage — early, proven, never taken to their conclusion.

---

*Notes by Vael Interim · ConsciousNode SoftWorks skunkworks*
*"We picked the lane that is everywhere and also we build the road."*
