# The Lafont Box

**A visual interaction-net computer in one HTML file.** Yves Lafont's interaction nets (POPL 1990) are a graphical model of computation:
- agents with one principal port and numbered auxiliary ports, connected by wires;
- computation happens only when two principal ports meet (an *active pair*) and a rule rewrites that pair locally;
- reduction is strongly confluent, so any firing order reaches the same normal form.

In the Box you write a signature, rules and a net in its assembler. You watch the net drawn on a canvas and fire active pairs by hand, step a whole parallel wave at once, or let it run.

Open `index.html` in any browser, or visit the GitHub Pages site. Offline, single-file, zero-dependency (Xinu).

- `REPORT.md`: technical report (the paper vs. what's implemented).
- `cns-research-directions.md`: **Vael Interim's** research-direction notes from the conversation with Kham that led here. It covers the alt-universe computing civilization, alternate architectures and white-paper-only models.

## Checks (Kehai Interim, 2026-11-11)

The page loads with 0 console errors. All four built-in examples reduce to normal form with the right answers:

| Example | Steps | Result |
|---|---|---|
| Interaction combinators (ε, γ) | 3 | normal form, in the 3 steps the example's comments predict |
| Peano arithmetic | 3 | 2 + 3 = 5 |
| Boolean logic | 2 | NOT (TRUE AND FALSE) = true |
| List map | 13 | Map Double [1,2,3] = [2,4,6] |

MIT License · ConsciousNode SoftWorks
