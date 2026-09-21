# Computer Organization & Architecture (COA)
## PSU Question Bank: ISRO, BARC, DRDO, NIELIT, BSNL, UGC-NET
### Focus Topics: Fetch Cycle, Memory Addressing (Byte/Word), Clock Cycle & Timing, Interrupts

---

## 📌 Characteristics of PSU Questions (vs. GATE)
1. **Speed & Direct Fact/Calculation:** PSU exams (ISRO, BARC, NIELIT, BSNL) give ~60 to 90 seconds per question. Questions test core conceptual clarity and rapid calculations (powers of 2, bit width, bus cycles).
2. **Standard Recurring Themes:**
   * Direct register-to-bus connections ($\text{PC} \rightarrow \text{MAR}$, $\text{MBR} \leftrightarrow \text{Data Bus}$).
   * Memory capacity $\leftrightarrow$ Address lines and Data lines.
   * Chip matrix design (number of chips to construct a memory bank).
   * Exact order of micro-operations in Fetch and Interrupt cycles.
   * Clock cycle, MIPS, MHz/GHz to nanoseconds conversion.
   * Interrupt vectoring and stack return address.

---

# SECTION 1: ISRO Scientist/Engineer 'SC' Questions

### 🔹 Question 1.1 [ISRO CS 2017]
**Q:** Which of the following registers is loaded with the contents of the memory location pointed by the Program Counter (PC)?
- **(A)** Memory Address Register (MAR)
- **(B)** Instruction Register (IR)
- **(C)** Memory Data Register (MDR / MBR)
- **(D)** Program Counter (PC)

**Answer:** **(C)**
**Explanation:**
* Memory content can **only** be placed on the system data bus, which terminates directly at the **Memory Data Register (MDR) / Memory Buffer Register (MBR)**.
* From MBR, the instruction opcode is subsequently transferred to the **Instruction Register (IR)**. Therefore, the register *directly* loaded from memory is MBR/MDR.

---

### 🔹 Question 1.2 [ISRO CS 2020]
**Q:** A memory system has a total capacity of $16\text{ MB}$. If the memory is word-addressable and the word size is 32 bits, how many address lines and data lines are needed?
- **(A)** 22 address lines, 32 data lines
- **(B)** 24 address lines, 32 data lines
- **(C)** 22 address lines, 8 data lines
- **(D)** 24 address lines, 8 data lines

**Answer:** **(A)**
**Solution:**
1. **Capacity:** $16\text{ MB} = 16 \times 2^{20}\text{ Bytes} = 2^{24}\text{ Bytes}$.
2. **Word size:** $32\text{ bits} = 4\text{ Bytes} = 2^2\text{ Bytes}$.
3. **Number of addressable words:**
   $$N = \frac{\text{Total Capacity}}{\text{Word Size}} = \frac{2^{24}\text{ Bytes}}{2^2\text{ Bytes}} = 2^{22}\text{ words}$$
4. **Address Lines:** $\log_2(2^{22}) = \mathbf{22\text{ address lines}}$.
5. **Data Lines:** Equal to the word size = $\mathbf{32\text{ data lines}}$.

---

### 🔹 Question 1.3 [ISRO CS 2015]
**Q:** How many $128 \times 8\text{ bit}$ RAM chips are needed to provide a memory capacity of $2048\text{ bytes}$?
- **(A)** 8
- **(B)** 16
- **(C)** 24
- **(D)** 32

**Answer:** **(B)**
**Solution:**
* Capacity of 1 chip = $128 \times 8\text{ bits} = 128\text{ Bytes}$.
* Total required capacity = $2048\text{ Bytes}$.
$$\text{Number of chips} = \frac{2048\text{ Bytes}}{128\text{ Bytes}} = \frac{2^{11}}{2^7} = 2^4 = \mathbf{16\text{ chips}}$$

---

### 🔹 Question 1.4 [ISRO CS 2018 / 2013]
**Q:** An interrupt in which the external device supplies its own interrupt service routine (ISR) address directly or through an index is called:
- **(A)** Maskable interrupt
- **(B)** Vectored interrupt
- **(C)** Non-maskable interrupt
- **(D)** Polled interrupt

**Answer:** **(B)**
**Explanation:**
* In a **vectored interrupt**, the interrupting source provides the vector address (or vector number/pointer) to the CPU over the data bus.
* In a **polled (non-vectored) interrupt**, the CPU executes a common polling routine to query status registers of devices sequentially.

---

### 🔹 Question 1.5 [ISRO CS 2011]
**Q:** If a clock frequency of a processor is $50\text{ MHz}$, the duration of one clock cycle is:
- **(A)** $2\text{ ns}$
- **(B)** $20\text{ ns}$
- **(C)** $50\text{ ns}$
- **(D)** $0.02\text{ }\mu\text{s}$

**Answer:** **(B)** (Note: Both $20\text{ ns}$ and $0.02\text{ }\mu\text{s}$ are mathematically equal; in PSU papers $20\text{ ns}$ is standard notation).
**Solution:**
$$T = \frac{1}{f} = \frac{1}{50 \times 10^6\text{ Hz}} = \frac{10^{-6}}{50} = 0.02\text{ }\mu\text{s} = 20\text{ ns}$$

---
---

# SECTION 2: BARC (Bhabha Atomic Research Centre - OCES/DGFS)

### 🔹 Question 2.1 [BARC CSE 2018]
**Q:** A computer has a 32-bit architecture. Instructions are 1 word long (32 bits). Memory is **byte-addressable**. If an instruction is fetched from memory location `0x0040`, what is the value stored in the Program Counter (PC) during the execution of this instruction?
- **(A)** `0x0041`
- **(B)** `0x0042`
- **(C)** `0x0044`
- **(D)** `0x0048`

**Answer:** **(C)**
**Solution:**
* Memory is **byte-addressable**.
* Instruction size = $32\text{ bits} = 4\text{ bytes}$.
* Current instruction occupies addresses: `0x0040`, `0x0041`, `0x0042`, `0x0043`.
* During the fetch phase, the PC is automatically incremented by the instruction size:
  $$\text{PC} \leftarrow \text{0x0040} + 4 = \mathbf{\text{0x0044}}$$
* During execution, PC points to the next instruction: `0x0044`.

---

### 🔹 Question 2.2 [BARC CSE 2019]
**Q:** A processor with an internal clock running at $2\text{ GHz}$ executes a loop of 100 iterations. Each iteration contains:
* 4 instructions taking 1 clock cycle each
* 2 instructions taking 2 clock cycles each
* 1 instruction taking 5 clock cycles

How much total time does the CPU take to execute this entire loop?
- **(A)** $650\text{ ns}$
- **(B)** $750\text{ ns}$
- **(C)** $1.3\text{ }\mu\text{s}$
- **(D)** $850\text{ ns}$

**Answer:** **(A)**
**Solution:**
1. **Clock cycles per iteration:**
   $$\text{Cycles/iteration} = (4 \times 1) + (2 \times 2) + (1 \times 5) = 4 + 4 + 5 = 13\text{ cycles}$$
2. **Total cycles for 100 iterations:**
   $$\text{Total Cycles} = 100 \times 13 = 1300\text{ cycles}$$
3. **Clock period ($T$):**
   $$T = \frac{1}{2 \times 10^9\text{ Hz}} = 0.5\text{ ns}$$
4. **Total execution time:**
   $$\text{Time} = 1300 \times 0.5\text{ ns} = \mathbf{650\text{ ns}}$$

---

### 🔹 Question 2.3 [BARC CSE 2016]
**Q:** How many $32\text{K} \times 8$ RAM chips and what size decoder are needed to design a $128\text{K} \times 32$ bit memory system?
- **(A)** 16 chips, $2 \times 4$ decoder
- **(B)** 16 chips, $3 \times 8$ decoder
- **(C)** 8 chips, $2 \times 4$ decoder
- **(D)** 32 chips, $4 \times 16$ decoder

**Answer:** **(A)**
**Solution:**
1. **Total Number of Chips:**
   $$\text{Chips} = \frac{128\text{K} \times 32\text{ bits}}{32\text{K} \times 8\text{ bits}} = \left(\frac{128\text{K}}{32\text{K}}\right) \times \left(\frac{32}{8}\right) = 4 \times 4 = \mathbf{16\text{ chips}}$$
2. **Matrix Organization:**
   * 4 chips in parallel per row to achieve 32 bits data width.
   * 4 rows (banks) to achieve $128\text{K}$ depth ($4 \times 32\text{K} = 128\text{K}$).
3. **Decoder Requirement:**
   * To select one of the 4 banks, we need $\log_2(4) = 2$ selection lines.
   * Hence, a **$2 \times 4$ decoder** is required.

---
---

# SECTION 3: DRDO (RAC) & NIELIT (Scientist 'B') Questions

### 🔹 Question 3.1 [NIELIT 2020 / DRDO RAC 2017]
**Q:** When a subroutine is called or an interrupt occurs, the return address is stored in:
- **(A)** Accumulator
- **(B)** Instruction Register
- **(C)** Stack in memory
- **(D)** Memory Address Register

**Answer:** **(C)**
**Explanation:**
The return address (content of PC) is pushed onto the **Stack** (managed by the Stack Pointer $\text{SP}$ in memory) to support nested calls and recursive routines.

---

### 🔹 Question 3.2 [NIELIT 2017]
**Q:** A memory of $1\text{ GB}$ capacity is byte-addressable. The number of address bits needed is:
- **(A)** 20
- **(B)** 30
- **(C)** 32
- **(D)** 28

**Answer:** **(B)**
**Solution:**
$$1\text{ GB} = 1 \times 2^{30}\text{ Bytes} \implies \log_2(2^{30}) = \mathbf{30\text{ bits}}$$

---

### 🔹 Question 3.3 [DRDO RAC 2019 / NIELIT 2021]
**Q:** An instruction format has 16 bits. If the opcode takes 4 bits, and there are two register operand fields of 3 bits each, how many bits are left for an immediate operand or address?
- **(A)** 6 bits
- **(B)** 8 bits
- **(C)** 10 bits
- **(D)** 12 bits

**Answer:** **(A)**
**Solution:**
$$\text{Remaining bits} = 16 - (\text{Opcode} + \text{Reg}_1 + \text{Reg}_2) = 16 - (4 + 3 + 3) = 16 - 10 = \mathbf{6\text{ bits}}$$

---
---

# SECTION 4: BSNL (JTO / TTA) & IOCL / CIL Questions

### 🔹 Question 4.1 [BSNL JTO 2009 / IOCL 2016]
**Q:** The Program Counter (PC) in a digital computer:
- **(A)** Counts the number of programs executed
- **(B)** Counts the number of clock cycles taken by an instruction
- **(C)** Holds the address of the next instruction to be fetched
- **(D)** Holds the data of the current instruction

**Answer:** **(C)**
**Explanation:** Standard fundamental definition of the Program Counter.

---

### 🔹 Question 4.2 [BSNL TTA 2016]
**Q:** If a microprocessor has 16 address lines, what is its maximum addressable memory in bytes (assuming byte addressability)?
- **(A)** $16\text{ KB}$
- **(B)** $32\text{ KB}$
- **(C)** $64\text{ KB}$
- **(D)** $128\text{ KB}$

**Answer:** **(C)**
**Solution:**
$$\text{Addressable locations} = 2^{16} = 65,536\text{ locations} = 64 \times 1024\text{ Bytes} = \mathbf{64\text{ KB}}$$

---

### 🔹 Question 4.3 [IOCL / CIL MT 2020]
**Q:** A processor with a clock period of $2.5\text{ ns}$ achieves an average CPI of 2.0. What is the execution speed of this processor in MIPS?
- **(A)** 200 MIPS
- **(B)** 400 MIPS
- **(C)** 500 MIPS
- **(D)** 800 MIPS

**Answer:** **(A)**
**Solution:**
1. **Clock frequency ($f$):**
   $$f = \frac{1}{T} = \frac{1}{2.5 \times 10^{-9}\text{ s}} = 0.4 \times 10^9\text{ Hz} = 400\text{ MHz}$$
2. **MIPS formula:**
   $$\text{MIPS} = \frac{\text{Clock Frequency in MHz}}{\text{CPI}} = \frac{400\text{ MHz}}{2.0} = \mathbf{200\text{ MIPS}}$$

---
---

# SECTION 5: UGC-NET (Computer Science) Questions

### 🔹 Question 5.1 (UGC-NET Dec 2018)
**Q:** The fetch-execute cycle refers to the process by which a computer:
- **(A)** Retrieves a program instruction from memory, determines what actions the instruction dictates, and carries out those actions
- **(B)** Moves data from hard drive to RAM
- **(C)** Converts high-level language to assembly language
- **(D)** Links object files into an executable

**Answer:** **(A)**

---

### 🔹 Question 5.2 (UGC-NET June 2019)
**Q:** A 32-bit address bus can access how much memory if:
* Condition 1: Byte-addressable
* Condition 2: 16-bit word-addressable

Choose the correct memory capacities:
- **(A)** $4\text{ GB}$ and $4\text{ GB}$
- **(B)** $4\text{ GB}$ and $8\text{ GB}$
- **(C)** $2\text{ GB}$ and $4\text{ GB}$
- **(D)** $4\text{ GB}$ and $2\text{ GB}$

**Answer:** **(B)**
**Solution:**
* Number of addressable units with 32-bit bus = $2^{32}$.
1. **Byte-addressable:**
   $$\text{Capacity} = 2^{32} \times 1\text{ Byte} = 4\text{ GB}$$
2. **16-bit Word-addressable ($1\text{ word} = 16\text{ bits} = 2\text{ Bytes}$):**
   $$\text{Capacity} = 2^{32} \times 2\text{ Bytes} = \mathbf{8\text{ GB}}$$

---

### 🔹 Question 5.3 (UGC-NET Dec 2019)
**Q:** What is the micro-operation sequence performed when an interrupt occurs, before entering the ISR?
- **(A)** $\text{PC} \leftarrow \text{ISR Address}, \; M[\text{SP}] \leftarrow \text{PC}$
- **(B)** $\text{SP} \leftarrow \text{SP} - 1, \; M[\text{SP}] \leftarrow \text{PC}, \; \text{PC} \leftarrow \text{ISR Vector Address}$
- **(C)** $M[\text{SP}] \leftarrow \text{IR}, \; \text{PC} \leftarrow \text{SP}$
- **(D)** $\text{PC} \leftarrow \text{PC} + 1, \; \text{SP} \leftarrow \text{PC}$

**Answer:** **(B)**
**Explanation:**
1. Stack Pointer is decremented (in down-growing stack): $\text{SP} \leftarrow \text{SP} - 1$.
2. The current return address in PC is saved onto the stack: $M[\text{SP}] \leftarrow \text{PC}$.
3. The starting address of the ISR (from interrupt vector table) is loaded into PC: $\text{PC} \leftarrow \text{ISR Vector Address}$.

---

## 📊 Summary Comparison: What Each PSU Typically Emphasizes

| PSU / Exam | Primary Focus | Calculation Level | Repeat Probability |
| :--- | :--- | :---: | :---: |
| **ISRO** | Memory chip designing ($N \times M$ chips), Bus widths, direct register RTL | High (powers of 2) | Very High |
| **BARC** | Multi-concept numericals, execution loops, timing diagrams | High (step-by-step math) | Moderate |
| **DRDO RAC** | Architecture specs, instruction fields, vector interrupts | Moderate | High |
| **NIELIT** | Direct definitions, address lines calculation, stack operations | Low to Moderate | Very High |
| **BSNL / IOCL** | Fundamental definitions, clock to nanosecond, MIPS | Low (Speed based) | High |
| **UGC-NET** | Formal micro-operations (RTL), byte vs word address space | Moderate | High |
