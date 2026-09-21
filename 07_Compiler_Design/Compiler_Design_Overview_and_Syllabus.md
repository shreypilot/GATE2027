# Compiler Design — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~4 – 6 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: Lexical Analysis
* Roles of Lexical Analyzer, Tokens, Patterns, Lexemes.
* Regular expressions to DFA implementation, handling longest match / maximal munch rule.
* Counting tokens, keywords, identifiers in C snippets (GATE trap questions on comments, string literals).

### Module 2: Syntax Analysis (Parsing)
* **Top-Down Parsing:**
  * Recursive Descent Parsing, Backtracking.
  * LL(1) Parsing: Eliminating Left Recursion, Left Factoring.
  * Calculation of FIRST and FOLLOW sets.
  * LL(1) Parsing Table construction and conflict identification.
* **Bottom-Up Parsing:**
  * Shift-Reduce, Operator Precedence.
  * LR Parsing Family:
    * LR(0): Canonical collection of LR(0) items, closure and goto operations.
    * SLR(1): Using FOLLOW sets to resolve shift-reduce (S-R) and reduce-reduce (R-R) conflicts.
    * LALR(1): Merging identical core states in CLR(1).
    * CLR(1) / Canonical LR: Lookahead propagation.
  * Parser Power Hierarchy:
    $$\text{LR}(0) < \text{SLR}(1) < \text{LALR}(1) < \text{CLR}(1)$$

### Module 3: Syntax-Directed Translation (SDT) & Intermediate Code
* **Syntax-Directed Definitions (SDD):**
  * Synthesized Attributes vs Inherited Attributes.
  * S-Attributed Definitions (Evaluated bottom-up using LR parser).
  * L-Attributed Definitions (Evaluated top-down or depth-first left-to-right).
* **Intermediate Code Generation:**
  * Three-Address Code (Quadruples, Triples, Indirect Triples).
  * Directed Acyclic Graph (DAG) construction for basic blocks and subexpression reuse.

### Module 4: Code Optimization & Runtime Environments
* **Code Optimization Techniques:**
  * Local vs Global optimization.
  * Common Subexpression Elimination, Copy Propagation, Dead Code Elimination, Constant Folding.
  * Loop optimizations: Loop Invariant Code Motion (Hoisting), Loop Unrolling, Strength Reduction.
* **Runtime Storage Management:**
  * Activation Records / Stack frames (Local variables, Return address, Saved registers).
  * Parameter passing mechanisms: Call by Value, Call by Reference, Call by Name.
