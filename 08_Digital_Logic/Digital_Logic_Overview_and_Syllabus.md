# Digital Logic — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~5 – 7 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: Boolean Algebra & Logic Gates
* Number Systems: Binary, Octal, Hexadecimal, 1's and 2's complement representations, Range and overflow conditions.
* Boolean Laws: De Morgan's Laws, Consensus Theorem, Duality Principle.
* Universal Gates: Implementing functions using minimum NAND or NOR gates.
* Canonical Forms: SOP (Sum of Products) minterms, POS (Product of Sums) maxterms.
* Minimization: Karnaugh Maps (K-Maps up to 4 & 5 variables), Prime Implicants, Essential Prime Implicants.

### Module 2: Combinational Circuits
* **Arithmetic Circuits:** Half Adder, Full Adder (using half adders and logic gates), Half Subtractor, Full Subtractor, Carry Lookahead Adder (propagation delay analysis).
* **Multiplexers & Demultiplexers:**
  * Implementing Boolean functions using $2^n \times 1$ and smaller multiplexers.
  * Cascading multiplexers to build larger MUX trees.
* **Encoders & Decoders:** Priority Encoders, $n \times 2^n$ Decoders with enable inputs, ROM/PLA/PAL architectures.
* **Comparators:** Magnitude comparators ($A > B, A = B, A < B$).

### Module 3: Sequential Circuits
* **Latches & Flip-Flops:**
  * SR Latch (NAND / NOR implementation, invalid state).
  * JK Flip-Flop, Race Around Condition and Master-Slave JK Flip-Flop.
  * D Flip-Flop (Delay), T Flip-Flop (Toggle).
  * Characteristic equations and excitation tables for all flip-flops.
* **Registers & Counters:**
  * Shift Registers: SISO, SIPO, PISO, PIPO, Universal Shift Register, Ring Counter, Johnson Counter.
  * Synchronous vs Asynchronous (Ripple) Counters:
    * Modulo-$N$ counters, propagation delay and maximum operating frequency:
      $$f_{max} = \frac{1}{n \times t_{pd}}$$
    * State transition diagram design and lock-out prevention.
