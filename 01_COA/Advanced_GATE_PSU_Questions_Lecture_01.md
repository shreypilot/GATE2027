# Computer Organization & Architecture (COA)
## Advanced & Edge-Case Questions: Lecture 01 Foundations
### High-Yield GATE & PSU Topics: Branch PC Update, Endianness, Interrupt Latency, Alignment, & Partial Decoding

---

## 📌 Master Concept Map of Advanced Lecture 01 Variations

| Topic Area | Classic Exam Trap / Concept | Core Formula / Rule |
| :--- | :--- | :--- |
| **PC Relative Branching** | PC is **already incremented** before offset is added | $\text{Target Address} = \text{Updated PC} + \text{Displacement}$ |
| **Endianness (Byte Ordering)** | Byte placement of Multi-byte words | **Little-Endian:** LSB at Lower Address<br>**Big-Endian:** MSB at Lower Address |
| **Interrupt Latency** | Worst-case delay before ISR execution begins | $T_{\text{latency}} = T_{\text{longest\_instr\_remaining}} + T_{\text{interrupt\_cycle}}$ |
| **Trap/Fault vs Interrupt** | Which instruction address is saved? | **Interrupt / Trap:** Address of **Next** instruction<br>**Fault (Page fault, Divide by 0):** Address of **Current** instruction (to re-execute) |
| **Memory Alignment** | Accessing unaligned 32-bit word across memory cycles | Aligned: 1 memory access<br>Unaligned: 2 memory accesses |
| **Partial Address Decoding** | Unused address lines create duplicate memory addresses | Number of alias/shadow locations = $2^{\text{unused address lines}}$ |

---

# PART 1: PC-Relative Addressing & Branch Instructions

### 🔹 Question 1.1 [GATE CSE 2004 / 2016 Trap]
**Q:** A computer has a 16-bit Program Counter (PC) and byte-addressable memory. The current PC value is `0x2000`. At this location, a 2-byte conditional branch instruction `BR offset` is stored, where the offset is an 8-bit signed 2's complement displacement.
If the 8-bit offset field contains `0xF8` ($-8$ in decimal), to what address will the processor branch if the condition is satisfied?
- **(A)** `0x1FF8`
- **(B)** `0x1FFA`
- **(C)** `0x2008`
- **(D)** `0x1FF2`

**Answer:** **(B) 0x1FFA**

**Detailed Step-by-Step Solution:**
1. **The Golden Rule of PC-Relative Branching:**  
   The PC is **always incremented during the fetch phase**, BEFORE the branch displacement is evaluated and added in the execution phase.
2. **Instruction Fetch & PC Update:**
   * Starting address = `0x2000`
   * Instruction size = 2 bytes
   * Updated $\text{PC} = \text{0x2000} + 2 = \mathbf{\text{0x2002}}$
3. **Displacement Conversion:**
   * Offset = `0xF8` in 8-bit 2's complement:
     * Sign bit is 1 (negative).
     * Magnitude = $- (2^8 - \text{0xF8}) = - (256 - 248) = -8\text{ bytes}$.
4. **Target Address Calculation:**
   $$\text{Target Address} = \text{Updated PC} + \text{Offset} = \text{0x2002} + (-8) = \mathbf{\text{0x1FFA}}$$
* *Common Trap:* Adding $-8$ directly to `0x2000` gives `0x1FF8` ❌ (Wrong!).

---

# PART 2: Byte Ordering — Big-Endian vs. Little-Endian

### 🔹 Question 2.1 [GATE CSE 2012 / ISRO CS 2015]
**Q:** A 32-bit integer `0x4A3B2C1D` is stored in byte-addressable memory starting at memory location `1000`.
What byte value is stored at memory address `1002` in:
1. A **Big-Endian** computer?
2. A **Little-Endian** computer?

**Solution:**
The 32-bit word is broken into 4 bytes:
* **Byte 3 (MSB):** `0x4A`
* **Byte 2:** `0x3B`
* **Byte 1:** `0x2C`
* **Byte 0 (LSB):** `0x1D`

#### 1. Big-Endian (MSB stored at lowest address):
| Address | 1000 | 1001 | **1002** | 1003 |
| :---: | :---: | :---: | :---: | :---: |
| **Byte** | `0x4A` (MSB) | `0x3B` | **`0x2C`** | `0x1D` (LSB) |

* Byte at address `1002` in Big-Endian = **`0x2C`**.

#### 2. Little-Endian (LSB stored at lowest address):
| Address | 1000 | 1001 | **1002** | 1003 |
| :---: | :---: | :---: | :---: | :---: |
| **Byte** | `0x1D` (LSB) | `0x2C` | **`0x3B`** | `0x4A` (MSB) |

* Byte at address `1002` in Little-Endian = **`0x3B`**.

---

### 🔹 Question 2.2 [GATE CSE 2014 / BARC 2021]
**Q:** An array of two 16-bit integers is stored in a Little-Endian system starting at address `0x0500`:
* Memory at `0x0500`: `0x34`
* Memory at `0x0501`: `0x12`
* Memory at `0x0502`: `0x78`
* Memory at `0x0503`: `0x56`

If this memory block is transmitted over a network and read as a single **32-bit unsigned integer** on a **Big-Endian** system, what is the resulting 32-bit value in hexadecimal?

**Solution:**
1. In Big-Endian, the byte at the lowest address (`0x0500`) is treated as the **MSB**, and the byte at the highest address (`0x0503`) is treated as the **LSB**.
2. Mapping bytes sequentially from address `0x0500` to `0x0503`:
   * Address `0x0500` (MSB) = `0x34`
   * Address `0x0501` = `0x12`
   * Address `0x0502` = `0x78`
   * Address `0x0503` (LSB) = `0x56`
3. Concatenating from MSB to LSB:
   $$\mathbf{\text{0x34127856}}$$

---

# PART 3: Interrupt Latency & Response Time

### 🔹 Question 3.1 [GATE CSE 2010 / BARC 2017]
**Q:** A processor runs at a clock frequency of $500\text{ MHz}$. 
* The instruction set execution times vary:
  * Shortest instruction takes **2 clock cycles**.
  * Longest instruction (multi-cycle division) takes **38 clock cycles**.
* When an interrupt occurs:
  * The interrupt detection and saving of PC and PSW on the stack takes **6 clock cycles**.
  * Transferring the first instruction of the ISR into the CPU takes **4 clock cycles**.

What is the **worst-case interrupt latency** (the maximum delay from the arrival of an interrupt request to the start of execution of the first ISR instruction)?

**Solution:**
1. **Clock Period ($T_{\text{clk}}$):**
   $$T_{\text{clk}} = \frac{1}{500 \times 10^6\text{ Hz}} = 2\text{ ns}$$
2. **Worst-Case Timing Scenario:**
   * The interrupt arrives just after the processor has begun executing the longest possible instruction (takes 38 cycles to complete).
   * CPU completes the current instruction = **38 cycles**.
   * CPU executes the interrupt cycle (save PC, PSW) = **6 cycles**.
   * CPU fetches the first instruction of the ISR = **4 cycles**.
3. **Total Worst-Case Clock Cycles:**
   $$\text{Total Cycles} = 38 + 6 + 4 = \mathbf{48\text{ clock cycles}}$$
4. **Worst-Case Interrupt Latency:**
   $$\text{Latency} = 48 \times 2\text{ ns} = \mathbf{96\text{ ns}}$$

---

# PART 4: Return Address: Interrupt vs. Trap vs. Fault

### 🔹 Question 4.1 [GATE CSE 2008 / 2022 Concept]
**Q:** Which of the following events causes the processor to push the address of the **CURRENT (faulting) instruction** onto the stack, rather than the address of the **NEXT instruction**?
- **(A)** Hardware timer interrupt
- **(B)** I/O device data transfer interrupt
- **(C)** Page fault exception
- **(D)** System call (`TRAP` / `SVC` instruction)

**Answer:** **(C) Page fault exception**

**Crucial GATE Principle Table:**

| Event Type | Cause | Example | Return Address Saved on Stack |
| :--- | :--- | :--- | :--- |
| **Interrupt** | Asynchronous hardware signal | Timer, Keyboard, NIC | Address of **NEXT** instruction |
| **Trap** | Synchronous intentional call | System Call (`read()`, `fork()`), Breakpoint | Address of **NEXT** instruction |
| **Fault** | Synchronous recoverable error | **Page Fault**, TLB miss | Address of **CURRENT** instruction (must re-execute after page is loaded) |
| **Abort** | Synchronous unrecoverable error | Hardware parity failure, Machine check | Program terminates (No return) |

---

# PART 5: Memory Alignment & Bus Access Penalties

### 🔹 Question 5.1 [GATE CSE 2005 / ISRO CS 2020]
**Q:** A 32-bit microprocessor has a 32-bit external data bus and byte-addressable memory. In this architecture, an aligned 32-bit word must start at a byte address that is a multiple of 4 (i.e., address ending in `00` in binary).
Each memory bus read cycle takes **2 clock cycles**.

How many memory bus cycles and clock cycles are required to read a 32-bit integer stored starting at:
1. Address `0x1000`?
2. Address `0x1002`?

**Solution:**
1. **At Address `0x1000` (Aligned):**
   * `0x1000` is divisible by 4 (aligned on a 4-byte boundary).
   * The full 32-bit word (`0x1000` to `0x1003`) is transferred over the 32-bit data bus in a **single memory bus cycle**.
   * Bus cycles = **1 cycle**, Clock cycles = **2 cycles**.

2. **At Address `0x1002` (Unaligned):**
   * The word occupies bytes `0x1002`, `0x1003`, `0x1004`, and `0x1005`.
   * These bytes span across two distinct 4-byte memory words:
     * Word 1: addresses `0x1000` – `0x1003` (contains bytes `0x1002` and `0x1003`).
     * Word 2: addresses `0x1004` – `0x1007` (contains bytes `0x1004` and `0x1005`).
   * The CPU must execute **two separate memory bus read cycles** and internally align/stitch the bytes into a 32-bit register.
   * Bus cycles = **2 cycles**, Clock cycles = $2 \times 2 = \mathbf{4\text{ cycles}}$ ($100\%$ penalty!).

---

# PART 6: Partial Address Decoding & Foldback (Shadow) Memory

### 🔹 Question 6.1 [GATE CSE 2003 / ISRO CS 2016]
**Q:** A microprocessor has an 8-bit address bus ($A_7 - A_0$), allowing 256 unique addresses (`0x00` to `0xFF`).
A $32\text{-byte}$ RAM chip is interfaced with the processor:
* Address lines $A_4, A_3, A_2, A_1, A_0$ are connected to the address inputs of the RAM chip.
* Address line $A_7$ is connected to the active-low Chip Select ($\overline{\text{CS}}$) input of the RAM chip.
* Address lines $A_6$ and $A_5$ are left **unconnected** (unused).

Determine:
1. How many distinct addresses map to each physical location in the RAM (Foldback/Alias count)?
2. What are the address ranges that activate this RAM chip?

**Solution:**
1. **Unconnected Address Lines:**
   * Lines $A_6$ and $A_5$ are "don't cares" ($X$).
   * Number of alias/shadow copies for each memory cell = $2^{\text{unused lines}} = 2^2 = \mathbf{4\text{ duplicate addresses}}$.
2. **Decoding Condition:**
   * $\overline{\text{CS}} = 0 \implies A_7 = 0$.
   * $A_6, A_5$ can take any of the 4 combinations: `00`, `01`, `10`, `11`.
   * $A_4 - A_0$ selects one of the 32 bytes (`00000` to `11111`):

| $A_7$ | $A_6$ | $A_5$ | $A_4 - A_0$ | Hex Range | Meaning |
| :---: | :---: | :---: | :---: | :---: | :--- |
| `0` | `0` | `0` | `00000` to `11111` | `0x00` – `0x1F` | Primary Range |
| `0` | `0` | `1` | `00000` to `11111` | `0x20` – `0x3F` | Mirror / Shadow 1 |
| `0` | `1` | `0` | `00000` to `11111` | `0x40` – `0x5F` | Mirror / Shadow 2 |
| `0` | `1` | `1` | `00000` to `11111` | `0x60` – `0x7F` | Mirror / Shadow 3 |

* Total apparent memory space consumed = $4 \times 32 = 128\text{ bytes}$ (`0x00` to `0x7F`), even though the physical RAM is only $32\text{ bytes}$.

---

# PART 7: Internal Datapath Control Signals during Fetch

### 🔹 Question 7.1 [GATE CSE 2011 / 2017 Model]
**Q:** In a single-bus CPU datapath, registers are connected to the internal bus through tri-state input enable ($\text{in}$) and output enable ($\text{out}$) control lines.
Which set of control signals is asserted in each clock cycle of the instruction fetch phase?

**Solution:**
* Let:
  * $\text{PC}_{\text{out}}$: place PC onto internal bus
  * $\text{MAR}_{\text{in}}$: load MAR from internal bus
  * $\text{Read}$: assert memory read control signal
  * $\text{MDR}_{\text{inE}}$: load MDR from external memory data bus
  * $\text{MDR}_{\text{out}}$: place MDR onto internal bus
  * $\text{IR}_{\text{in}}$: load IR from internal bus
  * $\text{IncPC}$: increment PC register

$$\begin{aligned}
\mathbf{\text{Cycle 1 (} t_1 \text{)}} &: \text{PC}_{\text{out}}, \; \text{MAR}_{\text{in}}, \; \text{Read} \\
\mathbf{\text{Cycle 2 (} t_2 \text{)}} &: \text{MDR}_{\text{inE}}, \; \text{IncPC} \\
\mathbf{\text{Cycle 3 (} t_3 \text{)}} &: \text{MDR}_{\text{out}}, \; \text{IR}_{\text{in}}
\end{aligned}$$

---

## 🎯 Quick Self-Check Matrix

| If Question Asks... | Watch Out For... | Formula |
| :--- | :--- | :--- |
| **PC value after fetch of branch** | Did the question ask for target address or post-fetch PC? | Post-fetch $\text{PC} = \text{Start} + \text{Size}$<br>Target $= \text{Post-fetch PC} + \text{offset}$ |
| **Address of Byte in Big-Endian** | Storing from left-to-right | MSB at lowest address ($A$), LSB at highest ($A + 3$) |
| **Address of Byte in Little-Endian** | Storing from right-to-left | LSB at lowest address ($A$), MSB at highest ($A + 3$) |
| **Unaligned 32-bit Word Access** | Requires 2 bus cycles if crossing 4-byte boundary | Accesses = 2 if Address mod $4 \neq 0$ |
| **Page Fault Return Address** | Re-executing vs Continuing | Saves address of **CURRENT** instruction |
| **Shadow / Mirror Addresses** | $k$ unconnected address lines | $2^k$ duplicate address ranges |
