# Computer Organization & Architecture (COA)
## GATE Questions Bank: Lecture 01 Topics, Memory Addressing & Clock Cycle Problems

---

## 📌 Master Formula & Concept Sheet

### 1. Clock Cycle & Timing Formulas
* **Clock Period ($T_{\text{clk}}$)**:
  $$T_{\text{clk}} = \frac{1}{\text{Clock Frequency } (f)}$$
  * $1\text{ GHz} \implies T = 1\text{ ns} = 10^{-9}\text{ s}$
  * $2\text{ GHz} \implies T = 0.5\text{ ns} = 500\text{ ps}$
  * $500\text{ MHz} \implies T = 2\text{ ns}$
* **Number of Clock Cycles for a Memory Access**:
  $$\text{Clock Cycles} = \left\lceil \frac{\text{Memory Access Time}}{T_{\text{clk}}} \right\rceil = \text{Memory Access Time} \times f$$
* **CPU Execution Time**:
  $$\text{CPU Time} = \text{Instruction Count (IC)} \times \text{CPI} \times T_{\text{clk}} = \frac{\text{IC} \times \text{CPI}}{f}$$

---

### 2. Byte vs Word Addressable Memory Formulas
* **Total Memory Capacity**:
  $$\text{Capacity} = (\text{Number of Addressable Units}) \times (\text{Unit Size})$$
* **Address Bits ($k$) required for $N$ locations**:
  $$k = \lceil \log_2(N) \rceil \quad (\text{Width of MAR and Address Bus} = k\text{ bits})$$
* **Data Bus / MDR / MBR Width**:
  $$\text{Width} = \text{Word Size (or minimum readable data unit size in bits)}$$

| Case | Addressable Unit Size | Total Addressable Locations ($N$) for Memory Size $M$ Bytes | Address Bits ($k = \log_2 N$) |
| :--- | :--- | :--- | :--- |
| **Byte-Addressable** | $1\text{ Byte} = 8\text{ bits}$ | $N = \frac{M}{1\text{ Byte}}$ | $k = \log_2(M)$ |
| **Word-Addressable ($W\text{ bytes/word}$)** | $W\text{ Bytes} = 8W\text{ bits}$ | $N = \frac{M}{W\text{ Bytes}}$ | $k = \log_2(M) - \log_2(W)$ |

* **Key Takeaway**: A word-addressable system requires **fewer address bits** than a byte-addressable system of the same total capacity because each address points to a larger block of bits.

---

### 3. Fetch Cycle & Register Micro-operations
$$\begin{aligned}
t_1 &: \text{MAR} \leftarrow \text{PC} \\
t_2 &: \text{MBR} \leftarrow M[\text{MAR}], \quad \text{PC} \leftarrow \text{PC} + I \quad (\text{simultaneous / non-conflicting}) \\
t_3 &: \text{IR} \leftarrow \text{MBR}
\end{aligned}$$
* $I = \text{Instruction size in addressable units}$:
  * Byte-addressable: $I = \text{Instruction size in bytes}$
  * Word-addressable: $I = \text{Instruction size in words}$

---

### 4. Interrupt Handling & Return Address
* Interrupt is sampled **at the end of the execution phase** of the current instruction.
* **Return Address** pushed onto Stack:
  $$\text{Return Address} = \text{Address of NEXT Instruction} = \text{Current Instruction Start Address} + \text{Size of Current Instruction}$$

---
---

# PART 1: Fetch Cycle & Register Micro-operations

### 🔹 Question 1.1 [GATE CSE 2005 / ISRO CS 2017]
**Q:** Which of the following sequences of register transfers correctly describes the instruction fetch cycle in a standard single-bus CPU datapath?
- **(A)** $t_1: \text{MAR} \leftarrow \text{PC}$; $\; t_2: \text{IR} \leftarrow M[\text{MAR}]$; $\; t_3: \text{PC} \leftarrow \text{PC} + 1$
- **(B)** $t_1: \text{MAR} \leftarrow \text{PC}$; $\; t_2: \text{MBR} \leftarrow M[\text{MAR}], \; \text{PC} \leftarrow \text{PC} + 1$; $\; t_3: \text{IR} \leftarrow \text{MBR}$
- **(C)** $t_1: \text{MBR} \leftarrow \text{PC}$; $\; t_2: \text{MAR} \leftarrow \text{MBR}$; $\; t_3: \text{IR} \leftarrow M[\text{MAR}]$
- **(D)** $t_1: \text{PC} \leftarrow \text{MAR}$; $\; t_2: \text{MBR} \leftarrow M[\text{MAR}]$; $\; t_3: \text{IR} \leftarrow \text{MBR}, \; \text{PC} \leftarrow \text{PC} + 1$

**Solution:**
* **Correct Answer:** **(B)**
* **Explanation:**
  1. Memory access is driven via the Memory Address Register: $\text{MAR} \leftarrow \text{PC}$.
  2. The memory read operation loads the instruction word into the data buffer: $\text{MBR} \leftarrow M[\text{MAR}]$. At the same time, the ALU or dedicated incrementer increments the Program Counter: $\text{PC} \leftarrow \text{PC} + 1$ (or appropriate instruction size). These two micro-operations do not conflict on resources and can occur in the same clock phase $t_2$.
  3. The opcode and operands are routed to the instruction register: $\text{IR} \leftarrow \text{MBR}$.
  * Note: Memory cannot directly write into $\text{IR}$ without passing through the bus buffer $\text{MBR/MDR}$, making (A) incorrect.

---

### 🔹 Question 1.2 [GATE CSE 2011]
**Q:** Consider a CPU where instruction fetch takes 3 clock cycles:
- Cycle 1: $\text{MAR} \leftarrow \text{PC}$
- Cycle 2: $\text{MBR} \leftarrow M[\text{MAR}], \; \text{PC} \leftarrow \text{PC} + 4$
- Cycle 3: $\text{IR} \leftarrow \text{MBR}$

If an instruction begins at memory address `0x4000` and has a length of 4 bytes in a byte-addressable system, what are the values of $\text{PC}$ and $\text{MAR}$ at the beginning of the **execute cycle** of this instruction?

**Solution:**
1. At Cycle 1: $\text{MAR} \leftarrow \text{PC} = \text{0x4000}$.
2. At Cycle 2: $\text{PC} \leftarrow \text{PC} + 4 = \text{0x4000} + 4 = \text{0x4004}$.
3. At Cycle 3: The instruction enters $\text{IR}$.
4. At the start of the **Execute Cycle**:
   * $\text{MAR}$ holds: `0x4000` (until overwritten by operand address during execution).
   * $\text{PC}$ holds: `0x4004` (already pointing to the next instruction).
* **Final Answer:** $\text{PC} = \text{0x4004}$, $\text{MAR} = \text{0x4000}$.

---

### 🔹 Question 1.3 [GATE CSE 2001 / ISRO CS 2014]
**Q:** A processor has a 24-bit address bus and a 32-bit data bus. What are the minimum bit-widths of the following registers?
1. Program Counter (PC)
2. Memory Address Register (MAR)
3. Memory Buffer Register (MBR / MDR)
4. Instruction Register (IR) (if instructions are 32-bit fixed size)

**Solution:**
* **Address Bus = 24 bits:**
  * $\text{PC}$ stores memory addresses $\implies \mathbf{24\text{ bits}}$.
  * $\text{MAR}$ is connected directly to the address bus $\implies \mathbf{24\text{ bits}}$.
* **Data Bus = 32 bits:**
  * $\text{MBR / MDR}$ is connected directly to the data bus $\implies \mathbf{32\text{ bits}}$.
  * $\text{IR}$ holds 32-bit instructions $\implies \mathbf{32\text{ bits}}$.

---
---

# PART 2: Interrupt Return Address & Cycle Timing

### 🔹 Question 2.1 [GATE CSE 2004]
**Q:** In a byte-addressable system, an instruction starting at byte address `2040` spans 4 bytes. An external device issues an interrupt request while this instruction is executing. Assuming the interrupt is unmasked and acknowledged, what value is pushed onto the stack as the Program Counter return address?
- **(A)** `2040`
- **(B)** `2043`
- **(C)** `2044`
- **(D)** `2041`

**Solution:**
* **Correct Answer:** **(C) 2044**
* **Reasoning:**
  1. The instruction occupies 4 bytes: `2040`, `2041`, `2042`, and `2043`.
  2. The interrupt is checked at the **end** of the current instruction's execution.
  3. The instruction at `2040` completes execution normally.
  4. The program must resume execution at the **next instruction**, which begins at byte address $2040 + 4 = 2044$.
  5. If the CPU pushed `2040`, the same instruction would execute twice upon return from the ISR, producing duplicate side effects.

---

### 🔹 Question 2.2 [GATE CSE 2017 / ISRO CS 2018]
**Q:** In a 16-bit processor with a byte-addressable memory, the stack pointer ($\text{SP}$) points to memory location `0x3FFE`. When an interrupt occurs:
1. The return address (16 bits) is pushed onto the stack (stack grows downwards towards lower addresses).
2. The Processor Status Register (16 bits) is pushed next.

If the current instruction occupies bytes `0x1020` to `0x1023`, what will be:
- The return address saved?
- The final value of $\text{SP}$ after both pushes?

**Solution:**
1. **Return Address:**
   * Current instruction spans: `0x1020` to `0x1023` (4 bytes).
   * Next instruction begins at: $\text{0x1020} + 4 = \mathbf{\text{0x1024}}$.
2. **Stack Operations:**
   * Initial $\text{SP} = \text{0x3FFE}$.
   * Push 16-bit Return Address (2 bytes): $\text{SP} \leftarrow \text{0x3FFE} - 2 = \text{0x3FFC}$.
   * Push 16-bit Status Register (2 bytes): $\text{SP} \leftarrow \text{0x3FFC} - 2 = \mathbf{\text{0x3FFA}}$.
* **Final Answer:** Return Address = `0x1024`, Final $\text{SP} = \text{0x3FFA}$.

---
---

# PART 3: Byte-Addressable vs. Word-Addressable Memory

### 🔹 Question 3.1 [GATE CSE 2015]
**Q:** A computer system has $4\text{ GB}$ of main memory. Determine the number of address bits required to access memory if:
1. The system is **Byte-Addressable**.
2. The system is **Word-Addressable**, where $1\text{ word} = 32\text{ bits}$.
3. The system is **Word-Addressable**, where $1\text{ word} = 64\text{ bits}$.

**Solution:**
Total Memory Capacity = $4\text{ GB} = 4 \times 2^{30}\text{ Bytes} = 2^2 \times 2^{30} = 2^{32}\text{ Bytes}$.

* **Case 1: Byte-Addressable**
  * Addressable unit = $1\text{ Byte}$.
  * Number of addresses $N = \frac{2^{32}\text{ Bytes}}{1\text{ Byte}} = 2^{32}$.
  * Address bits = $\log_2(2^{32}) = \mathbf{32\text{ bits}}$.

* **Case 2: Word-Addressable ($1\text{ word} = 32\text{ bits} = 4\text{ Bytes}$)**
  * Addressable unit = $4\text{ Bytes} = 2^2\text{ Bytes}$.
  * Number of addresses $N = \frac{2^{32}\text{ Bytes}}{4\text{ Bytes}} = 2^{30}\text{ words}$.
  * Address bits = $\log_2(2^{30}) = \mathbf{30\text{ bits}}$.

* **Case 3: Word-Addressable ($1\text{ word} = 64\text{ bits} = 8\text{ Bytes}$)**
  * Addressable unit = $8\text{ Bytes} = 2^3\text{ Bytes}$.
  * Number of addresses $N = \frac{2^{32}\text{ Bytes}}{8\text{ Bytes}} = 2^{29}\text{ words}$.
  * Address bits = $\log_2(2^{29}) = \mathbf{29\text{ bits}}$.

---

### 🔹 Question 3.2 [GATE CSE 2008 / ISRO CS 2013]
**Q:** A memory system has a capacity of $128\text{ MB}$.
1. If the memory is byte-addressable, what are the sizes of the Address Bus and MAR?
2. If the architecture is changed so that each memory word is $16\text{ bits}$, and the memory is made word-addressable, what are the new sizes of the Address Bus and MAR?

**Solution:**
* **Capacity:** $128\text{ MB} = 128 \times 2^{20}\text{ Bytes} = 2^7 \times 2^{20} = 2^{27}\text{ Bytes}$.
1. **Byte-Addressable:**
   * Number of units = $2^{27}$ bytes.
   * Address bits = $\log_2(2^{27}) = \mathbf{27\text{ bits}}$.
   * Address Bus = 27 lines, $\text{MAR} = 27\text{ bits}$.
2. **Word-Addressable ($1\text{ word} = 16\text{ bits} = 2\text{ Bytes}$):**
   * Number of words = $\frac{2^{27}\text{ Bytes}}{2\text{ Bytes}} = 2^{26}\text{ words}$.
   * Address bits = $\log_2(2^{26}) = \mathbf{26\text{ bits}}$.
   * Address Bus = 26 lines, $\text{MAR} = 26\text{ bits}$.

---

### 🔹 Question 3.3 [GATE CSE 2002 / ISRO CS 2011]
**Q:** A main memory of capacity $64\text{ KB}$ is to be constructed using memory chips of size $16\text{ KB} \times 4\text{ bits}$. Assume byte-addressable organization.
1. How many total chips are required?
2. How many chips form a single memory bank/word?
3. How many address bits are needed to select a chip, and how many to select an address within a chip?

**Solution:**
1. **Total capacity in bits:**
   $$\text{Target Capacity} = 64\text{ KB} = 64 \times 1024 \times 8\text{ bits} = 512\text{ Kbits}$$
   $$\text{One Chip Capacity} = 16\text{ KB} \times 4\text{ bits} = 16 \times 1024 \times 4\text{ bits} = 64\text{ Kbits}$$
   $$\text{Number of Chips} = \frac{512\text{ Kbits}}{64\text{ Kbits}} = \mathbf{8\text{ chips}}$$
2. **Organization:**
   * Target data width = $1\text{ Byte} = 8\text{ bits}$.
   * Chip data width = $4\text{ bits}$.
   * Chips in parallel per bank to provide 8 bits = $\frac{8\text{ bits}}{4\text{ bits}} = \mathbf{2\text{ chips/bank}}$.
   * Number of banks = $\frac{\text{Total Chips}}{\text{Chips per bank}} = \frac{8}{2} = 4\text{ banks}$.
3. **Address Breakdown:**
   * Total memory address bits for $64\text{ KB}$: $\log_2(64 \times 2^{10}) = \log_2(2^{16}) = 16\text{ bits}$.
   * Each chip has $16\text{ K}$ address locations: $\log_2(16 \times 2^{10}) = 14\text{ bits}$ (within-chip address).
   * Bank selection bits: $16 - 14 = \mathbf{2\text{ bits}}$ (used with a $2 \times 4$ decoder to enable the specific pair of chips).

---
---

# PART 4: Instruction Memory Layout & PC Tracking

### 🔹 Question 4.1 [GATE CSE 2006 / 2021 Model]
**Q:** A computer has a 32-bit word length and a memory that is **byte-addressable**. The program counter currently points to address `2000`. A sequence of 4 instructions $I_1, I_2, I_3, I_4$ has the following sizes:
* $I_1$: 1 word
* $I_2$: 3 words
* $I_3$: 2 words
* $I_4$: 1 word

Compute:
1. The memory address range occupied by each instruction.
2. The value of $\text{PC}$ immediately after fetching $I_3$.
3. The value saved if an interrupt is acknowledged during the execution of $I_2$.

**Solution:**
* Word size = $32\text{ bits} = 4\text{ bytes}$.
* Instruction sizes in bytes:
  * $\text{Size}(I_1) = 1 \times 4 = 4\text{ bytes}$
  * $\text{Size}(I_2) = 3 \times 4 = 12\text{ bytes}$
  * $\text{Size}(I_3) = 2 \times 4 = 8\text{ bytes}$
  * $\text{Size}(I_4) = 1 \times 4 = 4\text{ bytes}$

| Instruction | Start Address | End Address | Post-Fetch PC Value |
| :--- | :--- | :--- | :--- |
| **$I_1$** | `2000` | $2000 + 4 - 1 = \mathbf{2003}$ | `2004` |
| **$I_2$** | `2004` | $2004 + 12 - 1 = \mathbf{2015}$ | `2016` |
| **$I_3$** | `2016` | $2016 + 8 - 1 = \mathbf{2023}$ | `2024` |
| **$I_4$** | `2024` | $2024 + 4 - 1 = \mathbf{2027}$ | `2028` |

* **Answers:**
  1. Ranges: $I_1 = [2000, 2003], \; I_2 = [2004, 2015], \; I_3 = [2016, 2023], \; I_4 = [2024, 2027]$.
  2. Post-fetch PC value of $I_3$: **`2024`**.
  3. Interrupt during execution of $I_2$: Return address saved is the start of $I_3$ = **`2016`**.

---

### 🔹 Question 4.2 [GATE CSE 2014 Variant / ISRO CS 2016]
**Q:** Solve Question 4.1 assuming the memory is **Word-Addressable** with the same starting address `2000`.

**Solution:**
In word-addressable memory, 1 word occupies exactly 1 address location:
* $\text{Size}(I_1) = 1\text{ location}$
* $\text{Size}(I_2) = 3\text{ locations}$
* $\text{Size}(I_3) = 2\text{ locations}$
* $\text{Size}(I_4) = 1\text{ location}$

| Instruction | Start Address | End Address | Post-Fetch PC Value |
| :--- | :--- | :--- | :--- |
| **$I_1$** | `2000` | `2000` | `2001` |
| **$I_2$** | `2001` | `2003` | `2004` |
| **$I_3$** | `2004` | `2005` | `2006` |
| **$I_4$** | `2006` | `2006` | `2007` |

* **Answers:**
  1. Ranges: $I_1 = [2000], \; I_2 = [2001, 2003], \; I_3 = [2004, 2005], \; I_4 = [2006]$.
  2. Post-fetch PC value of $I_3$: **`2006`**.
  3. Interrupt during execution of $I_2$: Return address saved = **`2004`**.

---
---

# PART 5: Clock Cycle, Clock Frequency & Execution Time Problems

### 🔹 Question 5.1 [GATE CSE 2019 / 2023 Model]
**Q:** A processor operates at a clock frequency of $2.5\text{ GHz}$. The main memory has an access latency of $12\text{ ns}$. 
1. What is the clock cycle duration ($T_{\text{clk}}$)?
2. How many CPU clock cycles are needed for one memory read operation?
3. If each clock cycle without memory stall takes 1 cycle, how many wait states are inserted?

**Solution:**
1. **Clock Period ($T_{\text{clk}}$):**
   $$T_{\text{clk}} = \frac{1}{f} = \frac{1}{2.5 \times 10^9\text{ Hz}} = 0.4 \times 10^{-9}\text{ s} = \mathbf{0.4\text{ ns}} = 400\text{ ps}$$
2. **Clock Cycles for Memory Access:**
   $$\text{Cycles} = \left\lceil \frac{\text{Access Time}}{T_{\text{clk}}} \right\rceil = \frac{12\text{ ns}}{0.4\text{ ns}} = \mathbf{30\text{ cycles}}$$
3. **Wait States:**
   * If normal bus transaction is designed for 1 cycle:
   $$\text{Wait States} = 30 - 1 = \mathbf{29\text{ cycles}}$$

---

### 🔹 Question 5.2 [GATE CSE 2016]
**Q:** In a byte-addressable system, a processor fetches a 32-bit instruction from memory.
* The CPU data bus width is **16 bits**.
* Each memory read operation takes **4 clock cycles**.
* The processor runs at **800 MHz**.

Calculate:
1. How many memory accesses are required to fetch the instruction?
2. Total clock cycles spent in the fetch cycle.
3. Total time (in nanoseconds) taken to fetch the instruction.

**Solution:**
1. **Number of memory accesses:**
   * Instruction size = $32\text{ bits}$.
   * Data bus width = $16\text{ bits}$.
   * Accesses required = $\frac{32\text{ bits}}{16\text{ bits/access}} = \mathbf{2\text{ memory accesses}}$.
2. **Total clock cycles for fetch:**
   $$\text{Total Cycles} = 2\text{ accesses} \times 4\text{ cycles/access} = \mathbf{8\text{ clock cycles}}$$
3. **Time taken:**
   * Clock frequency $f = 800\text{ MHz} = 800 \times 10^6\text{ Hz}$.
   * Clock period $T_{\text{clk}} = \frac{1}{800 \times 10^6} = 1.25\text{ ns}$.
   $$\text{Total Fetch Time} = 8 \times 1.25\text{ ns} = \mathbf{10\text{ ns}}$$

---

### 🔹 Question 5.3 [GATE CSE 2007 / 2010]
**Q:** A program consisting of $10^6$ instructions is executed on two different processors $P_1$ and $P_2$:
* Processor $P_1$: Clock rate = $2\text{ GHz}$, Average $\text{CPI} = 1.5$
* Processor $P_2$: Clock rate = $3\text{ GHz}$, Average $\text{CPI} = 2.4$

Determine:
1. Execution time of the program on $P_1$ and $P_2$.
2. Which processor is faster, and by what speedup factor?

**Solution:**
$$\text{Execution Time} = \frac{\text{Instruction Count} \times \text{CPI}}{\text{Clock Frequency } f}$$

1. **For $P_1$:**
   $$T_1 = \frac{10^6 \times 1.5}{2 \times 10^9\text{ Hz}} = \frac{1.5 \times 10^6}{2 \times 10^9} = 0.75 \times 10^{-3}\text{ s} = \mathbf{0.75\text{ ms}} = 750\text{ }\mu\text{s}$$
2. **For $P_2$:**
   $$T_2 = \frac{10^6 \times 2.4}{3 \times 10^9\text{ Hz}} = \frac{2.4 \times 10^6}{3 \times 10^9} = 0.80 \times 10^{-3}\text{ s} = \mathbf{0.80\text{ ms}} = 800\text{ }\mu\text{s}$$
3. **Comparison & Speedup:**
   * Since $T_1 < T_2$, **$P_1$ is faster** than $P_2$ despite having a lower clock frequency (because of significantly better CPI).
   $$\text{Speedup} = \frac{T_2}{T_1} = \frac{0.80\text{ ms}}{0.75\text{ ms}} = \frac{80}{75} \approx \mathbf{1.067} \quad (6.67\%\text{ faster})$$

---

### 🔹 Question 5.4 [GATE CSE 2012 / 2018 Model]
**Q:** A non-pipelined processor executes instructions in 4 phases:
1. **Instruction Fetch (IF):** 3 clock cycles
2. **Instruction Decode (ID):** 1 clock cycle
3. **Execute (EX):** 2 clock cycles
4. **Memory Access (MEM):** 4 clock cycles (performed only by `LOAD` and `STORE` instructions)

In a benchmark program:
* $40\%$ of instructions are `LOAD` / `STORE`.
* $60\%$ of instructions are ALU / Branch (do not require the MEM phase).
* Clock frequency = $1\text{ GHz}$.

Calculate:
1. Average CPI of the processor.
2. MIPS (Million Instructions Per Second) rating of the processor.

**Solution:**
1. **Clock cycles per instruction category:**
   * For `LOAD` / `STORE`:
     $$\text{Cycles}_{\text{LD/ST}} = \text{IF} + \text{ID} + \text{EX} + \text{MEM} = 3 + 1 + 2 + 4 = 10\text{ cycles}$$
   * For ALU / Branch:
     $$\text{Cycles}_{\text{ALU}} = \text{IF} + \text{ID} + \text{EX} = 3 + 1 + 2 = 6\text{ cycles}$$
2. **Average CPI:**
   $$\text{Average CPI} = (0.40 \times 10) + (0.60 \times 6) = 4.0 + 3.6 = \mathbf{7.6\text{ cycles/instruction}}$$
3. **MIPS Rating:**
   $$\text{MIPS} = \frac{\text{Clock Frequency}}{CPI \times 10^6} = \frac{10^9}{7.6 \times 10^6} = \frac{1000}{7.6} \approx \mathbf{131.58\text{ MIPS}}$$

---

## 💡 Top 5 GATE Traps & Quick Check Summary

| Trap # | Scenario | Common Error | Correct Method |
| :---: | :--- | :--- | :--- |
| **1** | **Interrupt Return Address** | Saving address of current instruction (`PC - size` or current start) | Always save address of the **next instruction** |
| **2** | **Word-Addressable Address Bits** | Calculating address bits directly as $\log_2(\text{Bytes})$ | First divide total Bytes by Bytes per Word, then take $\log_2$ |
| **3** | **PC Increment in Fetch** | Forgetting word-to-byte conversion in byte-addressable memory | $\Delta \text{PC} = \text{words} \times (\text{word bits} / 8)$ |
| **4** | **Higher Clock != Faster CPU** | Assuming higher GHz always finishes faster | Must consider both $\text{CPI}$ and clock frequency ($T = \text{IC} \times \text{CPI} / f$) |
| **5** | **MBR vs MAR connection** | Confusing which connects to Address bus vs Data bus | $\text{MAR} \leftrightarrow \text{Address Bus}$, $\text{MBR} \leftrightarrow \text{Data Bus}$ |
