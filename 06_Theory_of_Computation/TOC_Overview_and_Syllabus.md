# Theory of Computation (TOC) — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~7 – 9 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: Finite Automata & Regular Languages
* **Deterministic Finite Automata (DFA):**
  * Formal definition ($Q, \Sigma, \delta, q_0, F$).
  * Construction of minimal DFAs (strings ending with, containing, divisible by $k$).
  * Myhill-Nerode Theorem & DFA Minimization (Table filling / Partition method).
* **Nondeterministic Finite Automata (NFA):**
  * $\epsilon$-NFA, NFA to DFA conversion via Subset Construction ($2^{|Q|}$ upper bound).
* **Regular Expressions & Grammars:**
  * Arden's Theorem for finding regular expressions.
  * Equivalence of DFA, NFA, and Regular Expressions.
  * Closure properties of Regular Languages (Union, Intersection, Complement, Kleene Star, Homomorphism, Reversal).
* **Pumping Lemma for Regular Languages:**
  * Proving non-regularity of languages like $\{a^n b^n \mid n \ge 0\}$.

### Module 2: Context-Free Languages & Pushdown Automata
* **Context-Free Grammars (CFG):**
  * Derivations (Leftmost vs Rightmost), Parse trees.
  * Ambiguity in grammars and inherently ambiguous languages.
  * Simplification of CFGs (Removal of useless symbols, unit productions, $\epsilon$-productions).
  * Chomsky Normal Form (CNF) and Greibach Normal Form (GNF).
* **Pushdown Automata (PDA):**
  * Deterministic PDA (DPDA) vs Non-deterministic PDA (NPDA).
  * Equivalence of NPDA and CFLs.
  * Closure properties of CFLs and DCFLs.
  * Pumping Lemma for Context-Free Languages.

### Module 3: Turing Machines & Computability Theory
* **Turing Machines (TM):**
  * Standard TM, Multi-track, Multi-tape, Non-deterministic TMs (Computational equivalence).
  * Recursively Enumerable (RE) Languages vs Recursive (REC) Languages.
* **Chomsky Hierarchy:**
  * Type 3 (Regular) $\subset$ Type 2 (Context-Free) $\subset$ Type 1 (Context-Sensitive) $\subset$ Type 0 (Unrestricted / RE).
* **Decidability & Halting Problem:**
  * Undecidability of the Halting Problem (Diagonalization proof).
  * Rice's Theorem (Part I & Part II) for semantic properties of RE languages.
  * Post Correspondence Problem (PCP) and Modified PCP.
  * Decidable vs Undecidable decision problems across language classes.
