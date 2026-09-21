# Computer Organization & Architecture (COA)
## Lecture 01: Fetch Cycle, Instruction Cycle, Memory Organization & Interrupt Handling

---

## 📌 Topic Priority Matrix (GATE CSE)

Based on recent GATE patterns and lecture analysis, prioritize topics as follows:

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **Fetch Cycle** | Register transfers: `PC → MAR → Memory → MBR/MDR → IR` and PC update logic |
| ★★★★★ | **PC Calculation Numericals** | Finding PC value post-fetch across variable instruction lengths |
| ★★★★★ | **Byte vs Word Addressable Memory** | Memory address capacity, word conversion, byte boundaries |
| ★★★★★ | **Instruction Size / Word Size Conversion** | Formulas connecting bits, bytes, words, and memory locations |
| ★★★★★ | **Interrupt Return Address** | Saving address of **next instruction** on the stack (GATE trap) |
| ★★★★☆ | **CPU Registers (PC, MAR, MBR, IR)** | Individual register functions, address bus vs data bus connection |
| ★★★★☆ | **Instruction Cycle with Interrupt** | Flow: Fetch → Execute → Check Interrupt → Service / Resume |
| ★★★☆☆ | **Basic Memory Concepts** | Memory hierarchy, read/write control signals |

---

## 1. Fetch Cycle & Register Transfer Flow

### Purpose
The sole purpose of the **Fetch Cycle** is to retrieve the instruction currently pointed to by the **Program Counter (PC)** from main memory and place it into the **Instruction Register (IR)** inside the processor.

### The Canonical Register Path

```text
PC → MAR → Memory → MBR/MDR → IR
```

### Step-by-Step Register Execution

```text
PC
 ↓
[Holds address of next instruction to fetch]
 ↓
MAR (Memory Address Register)
 ↓
[Connected to Address Bus; receives address from PC]
 ↓
Main Memory
 ↓
[Read control signal asserted; instruction data placed on Data Bus]
 ↓
MBR / MDR (Memory Buffer Register / Memory Data Register)
 ↓
[Receives instruction opcode & operands from Data Bus]
 ↓
IR (Instruction Register)
 ↓
[Holds fetched instruction for decoding & execution]
```

### Critical Register Distinction
* **PC (Program Counter):** Holds the **address of the instruction to be fetched next**.
* **IR (Instruction Register):** Holds the **currently fetched instruction** that is actively being decoded and executed.

### PC Increment Timing
Immediately after an instruction (or word) is fetched from memory into the CPU:

$$\text{PC} \leftarrow \text{PC} + \text{Size of current instruction (or instruction word)}$$

> **Important:** The PC is incremented during the fetch phase so that it automatically points to the next sequential instruction.

---

## 2. Complete Instruction Cycle with Interrupt Handling

In a standard von Neumann execution model, the CPU evaluates interrupts at a specific boundary in the cycle.

### Instruction Cycle Flowchart

```text
       +-----------------------+
       |      Fetch Cycle      |
       +-----------+-----------+
                   |
                   v
       +-----------------------+
       |     Execute Cycle     |
       +-----------+-----------+
                   |
                   v
       +-----------------------+
       |   Check Interrupt?    |
       +-----------+-----------+
                  / \
            No   /   \   Yes
                /     \
               v       v
+------------------+  +--------------------------------+
| Fetch Next       |  | Service Interrupt:             |
| Instruction      |  | 1. Save return address on stack|
+------------------+  | 2. Save processor status (PSW) |
                      | 3. Jump to ISR vector address  |
                      +---------------+----------------+
                                      |
                                      v
                      +--------------------------------+
                      | Resume Main Program            |
                      +--------------------------------+
```

### Core GATE Principle
> **Interrupt Evaluation Timing:**  
> In standard pipeline and uniprocessor models, the interrupt signal is sampled and checked **at the end of the execution cycle of the current instruction**, not in the middle of instruction execution.

* **If No Interrupt:** Execution continues sequentially; CPU fetches the next instruction.
* **If Interrupt Occurs:**
  1. CPU completes the execution of the current instruction.
  2. The **Return Information** (Address of the *next* instruction + Processor Status Word / Flags) is pushed onto the stack.
  3. Control transfers to the **Interrupt Service Routine (ISR)**.
  4. Upon ISR completion (`IRET`), the return address is popped back into the PC, resuming execution.

---

## 3. Byte-Addressable vs. Word-Addressable Memory

Understanding memory addressability is fundamental for solving numerical questions in GATE.

### A. Byte-Addressable Memory
* Each unique physical memory address references **1 Byte (8 bits)** of data.
* If word size = $32\text{ bits}$, then:
  $$\text{Word Size} = \frac{32}{8} = 4\text{ Bytes}$$
* Therefore, **1 Word occupies 4 distinct addressable memory locations**.

#### Example:
* Instruction size = $2\text{ words}$
* Word size = $4\text{ bytes}$
* Memory occupied in bytes:
  $$2\text{ words} \times 4\text{ bytes/word} = 8\text{ bytes}$$
* If starting address = `1000`:
  * Memory addresses occupied: **1000 through 1007** (8 bytes: 1000, 1001, 1002, 1003, 1004, 1005, 1006, 1007).
  * Next instruction begins at address **1008**.

---

### B. Word-Addressable Memory
* Each unique physical memory address references **1 Word** of data (irrespective of how many bits comprise a word).
* If an instruction is $2\text{ words}$:
  * Memory occupied = **2 addresses**.
* If starting address = `1000`:
  * Memory addresses occupied: **1000 and 1001**.
  * Next instruction begins at address **1002**.

---

### Summary Comparison Table

| Feature | Byte-Addressable Memory | Word-Addressable Memory |
| :--- | :--- | :--- |
| **Unit per Address** | 1 Byte (8 bits) | 1 Word ($n$ bits) |
| **1 Word (32 bits) occupies** | 4 address locations ($32/8$) | 1 address location |
| **PC Increment Unit** | Number of **bytes** | Number of **words** |
| **Formula for PC step** | $\text{Size in words} \times \frac{\text{Word bits}}{8}$ | $\text{Size in words}$ |

---

## 4. Universal GATE Formulas

### 1. Word Size in Bytes
$$\text{Word Size (Bytes)} = \frac{\text{Word Size (Bits)}}{8}$$

### 2. Instruction Size in Bytes
$$\text{Instruction Size (Bytes)} = \text{Instruction Size (Words)} \times \text{Word Size (Bytes)}$$

### 3. PC Increment in Byte-Addressable Memory
$$\Delta \text{PC} = \text{Instruction Size in Words} \times \left(\frac{\text{Word Size in Bits}}{8}\right)$$

### 4. PC Increment in Word-Addressable Memory
$$\Delta \text{PC} = \text{Instruction Size in Words}$$

---

## 5. Comprehensive Worked Lecture Numerical

### Problem Statement
A computer system has the following specifications:
* **Word size** = $32\text{ bits} = 4\text{ bytes}$
* **Program Starting Address** = `1000`

Instruction stream:
* **I1:** 2 words
* **I2:** 1 word
* **I3:** 1 word
* **I4:** 3 words
* **I5:** 1 word
* **I6:** 2 words

---

### Case 1: Memory is Byte-Addressable

Each word corresponds to $4\text{ bytes}$.

| Instruction | Size (Words) | Size (Bytes) | Addresses Occupied | Post-Fetch PC Value |
| :--- | :---: | :---: | :---: | :---: |
| **I1** | 2 | $2 \times 4 = 8$ | **1000 – 1007** | **1008** |
| **I2** | 1 | $1 \times 4 = 4$ | **1008 – 1011** | **1012** |
| **I3** | 1 | $1 \times 4 = 4$ | **1012 – 1015** | **1016** |
| **I4** | 3 | $3 \times 4 = 12$ | **1016 – 1027** | **1028** |
| **I5** | 1 | $1 \times 4 = 4$ | **1028 – 1031** | **1032** |
| **I6** | 2 | $2 \times 4 = 8$ | **1032 – 1039** | **1040** |

#### Key Lecture Questions:
1. **During execution of I6, what address does PC hold?**
   * After the fetch of I6 is completed, the PC is incremented to point to the next instruction:
   $$\text{PC} = 1040$$

---

### Case 2: Memory is Word-Addressable

Each address directly holds $1\text{ word}$.

| Instruction | Size (Words) | Addresses Occupied | Post-Fetch PC Value |
| :--- | :---: | :---: | :---: |
| **I1** | 2 | **1000 – 1001** | **1002** |
| **I2** | 1 | **1002** | **1003** |
| **I3** | 1 | **1003** | **1004** |
| **I4** | 3 | **1004 – 1006** | **1007** |
| **I5** | 1 | **1007** | **1008** |
| **I6** | 2 | **1008 – 1009** | **1010** |

#### Key Lecture Questions:
1. **During the execution of I5, what is the value of PC?**
   * After fetching I5, the PC points to the start of I6:
   $$\text{PC} = 1008$$

---

## 6. Interrupt Return Address — Critical GATE Trap 🚨

### Scenario
An `ADD` instruction occupies byte addresses **1012 through 1015**.  
An interrupt occurs while the processor is executing this `ADD` instruction.

### What is saved as the Return Address on the stack?
* **Common Student Trap:** Saving `1012` (start address of `ADD`) ❌
* **Correct Answer:** **`1016`** (starting address of the **NEXT** instruction) ✅

### Explanation & Justification
1. The CPU does not service the interrupt mid-instruction. It completes the execution of `ADD`.
2. Because `ADD` has finished, repeating `ADD` upon returning from the ISR would corrupt registers/memory (e.g., doubling an addition).
3. The processor must resume from the **next sequential instruction**, which begins at address `1016`.
4. Therefore, the return address pushed onto the stack is **1016**.

```text
      ADD Instruction (1012 - 1015)
                 ↓
      Execution completes
                 ↓
      Interrupt Serviced
                 ↓
      Stack Return Address = 1016  <-- Next Instruction Address
```

---

## 7. High-Yield Practice Questions

### Q1 (PC Calculation)
In a byte-addressable computer with 16-bit word size, a program starts at address 2048. If the first three instructions have sizes of 1 word, 2 words, and 4 words respectively, what is the PC value after fetching the third instruction?
* **Word size** = $16\text{ bits} = 2\text{ bytes}$
* Sizes in bytes:
  * $I_1 = 1 \times 2 = 2\text{ bytes}$ (2048 – 2049, next PC = 2050)
  * $I_2 = 2 \times 2 = 4\text{ bytes}$ (2050 – 2053, next PC = 2054)
  * $I_3 = 4 \times 2 = 8\text{ bytes}$ (2054 – 2061, next PC = 2062)
* **Answer:** $\mathbf{2062}$

### Q2 (Register Roles)
Which register is directly connected to the Address Bus and which register is connected to the Data Bus during the instruction fetch cycle?
* **Address Bus:** MAR (Memory Address Register)
* **Data Bus:** MBR / MDR (Memory Buffer / Data Register)
