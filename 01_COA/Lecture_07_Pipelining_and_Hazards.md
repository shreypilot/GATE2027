# Computer Organization & Architecture (COA)
## Lecture 07–08: CPU Time, Pipeline Design, Performance & Hazards

**Source:** GfG GATE CS Crash Course (Vijay Sir) — CPU Time / PIPELINE Design / PIPELINING Hazards class screenshots

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **$ET_{pipe}=[k+(n-1)]t_p$** | Fill + remaining instructions |
| ★★★★★ | **Speedup $S = ET_{NP}/ET_{pipe}$** | Uniform vs non-uniform stage delay |
| ★★★★★ | **Clock $t_p$** | $t_p = \max(\text{stage delay}) + t_{latch}$ |
| ★★★★★ | **Hazards: structural / data / control** | Single-ALU GATE-2003; RAW; branch |
| ★★★★★ | **Operand forwarding** | Bypass / short-circuit; stall count |
| ★★★★☆ | **CPI mixing** | Weighted average CPI; speedup after CPI=1 |
| ★★★★☆ | **Throughput** | $n/[(k+n-1)t_p] \rightarrow 1/t_p$ |
| ★★★☆☆ | **RISC 5-stage names** | IF, ID, EX, MA, WB |

---

## 1. Cycle Time vs Clock Frequency

$$\text{Cycle time} \propto \frac{1}{\text{Clock frequency}}$$

**Example:** $1\,\text{GHz}$ processor

$$T = \frac{1}{10^{9}} = 10^{-9}\,\text{s} = 1\,\text{ns}$$

CPU time for a program:

$$\text{CPU time} = IC \times CPI \times T_{clk}$$

### Weighted CPI

| Type | Frequency | CPI |
| :--- | :---: | :---: |
| Load & Store | $40\%$ | $4$ |
| ALU | $40\%$ | $6$ |
| Branch | $20\%$ | $2$ |

$$CPI_{avg} = 0.40\times 4 + 0.40\times 6 + 0.20\times 2 = 1.6+2.4+0.4 = \mathbf{4.4}$$

(If $T_{clk}=1\,\text{ns}$, average instruction time $= 4.4\,\text{ns}$.)

### Enhancement / speedup numerical (class)

Old CPU: $T_{clk}=2.3\,\text{ns}$; Load/Store $9$ cycles $40\%$; ALU $7$ cycles $40\%$; Branch $3$ cycles $20\%$.

$$CPI_{old}=0.4\times 9 + 0.4\times 7 + 0.2\times 3 = 7$$

$$ET_{old} = 7 \times 2.3 = 16.1\,\text{ns/instr}$$

New design: average **CPI $=1$**, but cycle time **$+40\%$**: $T_{new}=2.3\times 1.4=3.22\,\text{ns}$.

$$ET_{new}=1\times 3.22=3.22\,\text{ns}$$

$$S = \frac{16.1}{3.22} = \mathbf{5}$$

---

## 2. What Is a Pipeline?

To build an **$N$-stage** pipeline, the CPU is split into **$N$ independent functional units**.  
**Independent** means: in the **same clock**, stage $i$ does its job while stage $j$ does another instruction’s job.

Example, cycle $3$ of a 3-stage pipe:

* $I_1$ in **EX**
* $I_2$ in **ID**
* $I_3$ in **IF (Fetch)**

### RISC 5-stage pipeline

1. **IF** — Instruction Fetch  
2. **ID** — Instruction Decode  
3. **EX** — Execute  
4. **MA** — Memory Access  
5. **WB** — Write Back  

(Some diagrams use **CM / MEM** for memory, **ALU** for EX, **DM** for data memory.)

---

## 3. Pipeline Hardware: Stages + Latches

A 4-stage linear pipeline:

```text
→ R1 → S2 → R2 → S3 → R3 → S4 → R4 → output
     combinational stages Si, edge-triggered pipeline registers Ri
```

* **$k$ stages** need **$k$ pipeline registers** if there is a latch **before the first and after the last** (GATE wording: “between each stage **and at the end of the last stage**”).
* Clock period is set by the **slowest** stage **plus** latch delay.

---

## 4. Space-Time Diagrams

### Non-pipeline ($n=4$ instructions, $k=4$ stages)

Each instruction occupies **all** stages sequentially; the next instruction starts only after the previous **finishes**.

$$ET_{NP} = n \times k = 4\times 4 = \mathbf{16}\text{ cycles}$$

(If each stage is 1 cycle.)

### Pipeline (ideal, no stalls)

```text
     c1  c2  c3  c4  c5  c6  c7
S1   I1  I2  I3  I4
S2       I1  I2  I3  I4
S3           I1  I2  I3  I4
S4               I1  I2  I3  I4
```

First result at cycle $k=4$; last result at cycle $k+(n-1)=4+3=\mathbf{7}$.

$$\mathbf{ET_{pipe} = [k + (n-1)] \text{ cycles}}$$

---

## 5. Uniform vs Non-Uniform Delay

Let $t_n$ = time of **one instruction** on a non-pipelined datapath (sum of combinational delays).  
Let $t_p$ = **pipeline clock**.

### Uniform stage delay

Every stage $= d$, so $t_n = k\cdot d$ and $t_p = d$ (ignore latch).

Example: $k=4$, each stage $2\,\text{ns}$:

* Non-pipe, $n=1$: $t_n=2+2+2+2=8\,\text{ns}$, $ET_{NP}=8\,\text{ns}$
* Pipe, $n=1$: $ET_{pipe}=[4+(1-1)]\times 2=8\,\text{ns}$ (no gain on a **single** instruction)

### Non-uniform stage delay

$$t_p = \max(\text{stage delays})$$

Example stages $2,4,8,2\,\text{ns}$, $n=1$, $k=4$:

$$t_p=\max(2,4,8,2)=8\,\text{ns}$$

$$ET_{pipe}=[4+(1-1)]\times 8 = \mathbf{32}\,\text{ns}$$

**Pipelining a single instruction with unequal stages can be slower** than non-pipe ($t_n=2+4+8+2=16\,\text{ns}$) because every cycle waits for the slowest stage.

### GATE Q4 (four combinational stages + 1 ns registers)

Delays: $S1=5$, $S2=6$, $S3=11$, $S4=8$ ns; each pipeline register $=1$ ns.

$$t_p = \max(5,6,11,8)+1 = 11+1 = \mathbf{12}\,\text{ns}$$

Non-pipe (combinational path, no latches as in class): $5+6+11+8=\mathbf{30}\,\text{ns}$

$$S = \frac{30}{12} = \mathbf{2.5}$$

(For large $n$, speedup $\rightarrow t_n / t_p$.)

---

## 6. Performance Metrics

$$\text{Speedup } S = \frac{ET_{NP}}{ET_{pipe}} = \frac{n\, t_n}{[k+(n-1)] t_p}$$

As $n\to\infty$: $S \to t_n / t_p$ (and $\le k$ if $t_n = k t_p$).

$$\text{Throughput} = \frac{n}{[k+(n-1)]t_p}$$

Ideal asymptotic throughput:

$$\mathbf{\text{Throughput} = \dfrac{1}{t_p}}$$

(rate of **output** results once the pipe is full.)

Efficiency $\eta = S / k$.

---

## 7. Pipeline Hazards

A **hazard** exists when the pipeline (or part of it) **must stall** because conditions do not allow continued overlapped execution.

Three types:

1. **Resource / Structural**
2. **Data**
3. **Control**

### 7.1 Structural (resource) hazard

Two stages need the **same hardware** in the same cycle.

**Split I-cache / D-cache (or separate CM and DM):** IF uses instruction memory while MEM uses data memory $\Rightarrow$ **no stall**.

**Unified memory:** IF of $I_4$ and MEM of $I_1$ collide $\Rightarrow$ **stalls / bubbles**. Waiting **inserts NOP cycles**.

**Single ALU:** two instructions that both need the ALU in the same cycle $\Rightarrow$ structural hazard (GATE-2003 statement 3).

### 7.2 Data hazard (dependency)

Created when the **next** instruction needs a result **not yet written** by a previous instruction.

```text
ADD  r1, r2, r3;    r1 ← r2 + r3
MUL  r4, r1, r5;    r4 ← r1 * r5     ← RAW on r1
```

**RAW** (Read After Write) is the usual pipeline data hazard.  
(WAR / WAW appear mainly with out-of-order / multi-issue.)

**Solution (class names — same hardware idea):**

> **Operand forwarding**  |  **Bypassing**  |  **Short-circuiting**

Result is taken from the ALU/MEM output **before WB**, and fed to the next instruction’s ALU inputs.

### 7.3 Control hazard

A **conditional jump / branch** is not resolved until EX (or later). Instructions already fetched from the sequential path may be wrong $\Rightarrow$ flush / stall / predict.

---

## 8. GATE 2003 (1 mark) — Single ALU Pipeline

For a pipelined CPU with a **single ALU**, which of the following can cause a hazard?

1. The $(j+1)^{\text{st}}$ instruction uses the result of the $j^{\text{th}}$ as an operand. **(Data / RAW)**  
2. Execution of a **conditional jump**. **(Control)**  
3. The $j^{\text{th}}$ and $(j+1)^{\text{st}}$ both need the **ALU at the same time**. **(Structural)**

**Answer: all three** (option D).  
Options like “2 and 3 only” are traps if you forget that RAW still exists even with one ALU.

---

## 9. GATE 2007-style — ADD / MUL / SUB + Extra Cycles

```text
I1: ADD R2, R1, R0;   R2 ← R1 + R0     (1 extra-cycle class mark)
I2: MUL R4, R3, R2;   R4 ← R3 * R2     (3 extra / 2 stalls)
I3: SUB R6, R5, R4;   R6 ← R5 − R4     (1 extra)
```

Ideal 4-stage pipe, $n=3$:

$$ET = k+(n-1)=4+2=6\text{ cycles}$$

With the extra stall cycles shown on the board ($0+2+0$):

$$\text{Total} = 6+2 = \mathbf{8}\text{ cycles}$$

With **operand forwarding**, MUL can start using $R2$ as soon as ADD’s EX completes, so the long stall chain shrinks (draw the reservation table; do not blindly reuse $8$).

---

## 10. GATE Formula Sheet

1. $T_{clk}=1/f$
2. $CPI_{avg}=\sum f_i\,CPI_i$
3. $CPU_{time}=IC\times CPI\times T_{clk}$
4. $ET_{pipe}=[k+(n-1)]t_p$
5. $ET_{NP}=n\,t_n$ (non-pipe)
6. $t_p=\max(d_i)+t_{reg}$ (non-uniform + latches)
7. $S=ET_{NP}/ET_{pipe}$; large $n$: $S\to t_n/t_p$
8. Throughput $\to 1/t_p$
9. Hazards: structural (same unit), data (RAW), control (branch)
10. Fix RAW: forwarding / bypass / stall/NOP
11. Independent stages $\Rightarrow$ different instructions in different stages **in the same cycle**
