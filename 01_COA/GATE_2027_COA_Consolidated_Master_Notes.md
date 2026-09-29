# Computer Organization & Architecture (COA)
## GATE 2027 Consolidated Master Revision Notes

> **Synthesis:** Consolidated from 469 lecture slides & core syllabus coverage for GATE CSE / IT / PSUs.
> **Coverage:** Machine Instructions & Addressing Modes • Memory Organization • Instruction Format & Expanding Opcode • ALU & Data Path • Control Unit Design • Floating-Point (IEEE 754) • CPU Performance • Pipelining & Hazards • Practice MCQs • Formula & Trap Sheet

---

## 📌 Topic Weightage & Priority Matrix (GATE CSE)

| Priority | Topic / Concept | Core Exam Focus | Frequency / Weight |
| :--- | :--- | :--- | :--- |
| ★★★★★ | **Pipelining & Hazards** | Speedup, pipeline cycles, RAW/WAR/WAW, stalls, branch penalty | 2-4 Marks |
| ★★★★★ | **Cache & Memory Addressing** | Byte vs word addressability, address line sizing, capacity math | 2-3 Marks |
| ★★★★★ | **Instruction Format & Opcode** | Expanding opcode numericals, register bit calculation | 1-2 Marks |
| ★★★★★ | **Floating Point (IEEE 754)** | Single/double precision, normalized/subnormal, bias, representations | 1-2 Marks |
| ★★★★☆ | **Addressing Modes** | Effective Address (EA) calculation, memory access count | 1-2 Marks |
| ★★★★☆ | **CPU Performance & Timing** | $\text{CPU Time} = \frac{\text{IC} \times \text{CPI}}{f}$, MIPS, Speedup ratios | 1-2 Marks |
| ★★★☆☆ | **Control Unit Design** | Hardwired vs Microprogrammed, Horizontal vs Vertical encoding | 1 Mark |
| ★★★☆☆ | **Fetch Cycle & Data Path** | Register transfer micro-ops ($\text{PC} \to \text{MAR} \to \text{Memory} \to \text{MBR} \to \text{IR}$) | 1 Mark |

---

## 1. Computer Basics, Memory Notation & System Bus

### 1.1 Bit, Byte, Word & Memory Capacity Formulas

* **Bit:** Fundamental binary unit ($0$ or $1$).
* **Byte:** Standard unit of $8\text{ bits}$.
* **Word:** Natural data transfer size handled by the CPU architecture (e.g., 16-bit, 32-bit, 64-bit).
* **Memory Notation ($N \times M$):**
  * $N =$ Number of distinct addressable locations.
  * $M =$ Number of bits stored per location.
  * Total Capacity $= N \times M\text{ bits} = \frac{N \times M}{8}\text{ bytes}$.

#### Key Formulas:
$$\text{Number of addressable locations } N = 2^n \quad (n = \text{number of address lines})$$

$$\text{Address lines required } n = \lceil \log_2 N \rceil$$

> 🚨 **GATE Trap:** A "byte" is always $8\text{ bits}$, but a "word" is **processor-dependent**. Never assume 1 word = 8 bits unless specified!

---

### 1.2 Byte-Addressable vs. Word-Addressable Memory

| Feature | Byte-Addressable Memory | Word-Addressable Memory ($W\text{ bits/word}$) |
| :--- | :--- | :--- |
| **Location Size** | Each unique address points to $1\text{ byte } (8\text{ bits})$ | Each unique address points to $1\text{ word } (W\text{ bits})$ |
| **Address Increment** | $+1$ moves to next byte | $+1$ moves to next word |
| **Memory of $N$ Words** | Requires $\frac{N \times W}{8}$ byte addresses | Requires $N$ word addresses |
| **Program Counter Increment** | $\text{PC} \leftarrow \text{PC} + \text{Instruction size in bytes}$ | $\text{PC} \leftarrow \text{PC} + \text{Instruction size in words}$ |

⚡ **Exam Shortcut:** If total memory capacity is given in **Bytes** (e.g., $16\text{ MB}$) and the memory is **Byte-Addressable**, the number of distinct addresses is exactly equal to the total number of bytes ($16 \times 2^{20}$).

---

### 1.3 System Bus Architecture

The system bus forms the physical & logical communication highway connecting the CPU, Main Memory, and I/O Devices.

```
       +-------------------------------------------------+
       |                  CONTROL BUS                    | (Bidirectional / Unidirectional)
       +--------+--------------------+-------------------+
                |                    |                   |
                v                    v                   v
        +---------------+    +---------------+   +---------------+
        |               |    |               |   |               |
        |      CPU      |    |  Main Memory  |   |  I/O Devices  |
        | (Bus Master)  |    |               |   |               |
        +-------+-------+    +-------+-------+   +-------+-------+
                |                    |                   |
                +====================+===================+
                |                   DATA BUS             | (Bidirectional)
                |                                        |
                +----------------------------------------+
                |                 ADDRESS BUS            | (Unidirectional: CPU -> Memory/IO)
                +----------------------------------------+
```

1. **Address Bus ($n$ lines):** Unidirectional (driven by CPU/Bus Master). Specifies memory location address. Defines total addressable space $= 2^n$.
2. **Data Bus ($m$ lines):** Bidirectional. Carries data/instructions between CPU and memory/IO. Determines word transfer size per memory cycle.
3. **Control Bus:** Carries read/write signals, memory/IO select, interrupt signals, clock, and bus request/acknowledge.

---

### 1.4 Endianness (Byte Ordering in Memory)

Endianness defines how multi-byte values (e.g., 32-bit integers) are mapped into sequential byte addresses.

* **Big-Endian:** Most Significant Byte (MSB) is stored at the **lowest** memory address.
* **Little-Endian:** Least Significant Byte (LSB) is stored at the **lowest** memory address.

#### Example: Storing `0x12345678` at address `0x1000`

| Byte Value | Byte Description | Big-Endian Address | Little-Endian Address |
| :---: | :---: | :---: | :---: |
| `0x12` | MSB | `0x1000` (Lowest) | `0x1003` (Highest) |
| `0x34` | Byte 2 | `0x1001` | `0x1002` |
| `0x56` | Byte 1 | `0x1002` | `0x1001` |
| `0x78` | LSB | `0x1003` (Highest) | `0x1000` (Lowest) |

⚡ **Shortcut:**
* **BIG-endian** $\to$ **BIG byte (MSB)** at lowest address.
* **LITTLE-endian** $\to$ **LITTLE byte (LSB)** at lowest address.

> 🚨 **GATE Trap:** Endianness reorders **bytes**, NOT individual bits within a byte!

---

## 2. Machine Instructions & Addressing Modes

### 2.1 Effective Address (EA) & Addressing Modes Summary

**Effective Address (EA):** The actual physical memory address of the operand, determined at execution time.

| Addressing Mode | Operand / EA Rule | Memory Accesses (after fetch) | Primary Use Case / Key Advantage |
| :--- | :--- | :---: | :--- |
| **Immediate** | Operand = Constant inside instruction | **0** | Constant initializations (`MOV R1, #5`) |
| **Direct (Absolute)** | $\text{EA} = A$ (Address in instruction) | **1** | Accessing global variables |
| **Indirect** | $\text{EA} = M[A]$ | **2** | Pointer dereferencing (`*ptr`) |
| **Register** | Operand = Contents of Register $R$ | **0** | Fastest operand access; no memory read |
| **Register Indirect** | $\text{EA} = R$ | **1** | Passing pointers via registers |
| **Indexed** | $\text{EA} = A + \text{Index Register}$ | **1** | Array element access ($A[\text{index}]$) |
| **Base Register** | $\text{EA} = \text{Base Register} + \text{Displacement}$ | **1** | Relocatable code, segment offsets |
| **PC-Relative** | $\text{EA} = \text{PC} + \text{Offset}$ | **1** | Branch instructions, position-independent code |
| **Auto-Increment** | $\text{EA} = R$, then $R \leftarrow R + d$ | **1** | Stepping through arrays, popping stack |
| **Auto-Decrement** | $R \leftarrow R - d$, then $\text{EA} = R$ | **1** | Push operation on stack |

> 🚨 **GATE Trap:**
> 1. **Register vs. Register Indirect:** Register mode holds the **operand data itself**. Register Indirect mode holds the **memory address** of the operand.
> 2. **Immediate Mode:** The value in the instruction field is the actual value, **not** an address.

---

### 2.2 Instruction Address Count Types

Instruction formats are classified by how many explicit memory/register addresses they specify:

```text
3-Address:  ADD R1, R2, R3    -->  R1 <- R2 + R3          (Shortest code, longest instruction word)
2-Address:  ADD R1, R2        -->  R1 <- R1 + R2          (Balanced length & instruction count)
1-Address:  ADD X             -->  AC <- AC + M[X]        (Implied Accumulator architecture)
0-Address:  ADD               -->  TOS <- TOS + TOS_below (Implied Stack Machine)
```

---

## 3. Instruction Format & Expanding Opcode

### 3.1 Field Breakdown & Bits Calculation

An instruction word consists of distinct bit fields:

```text
+-------------------+-----------------------+------------------------+
|   Opcode Field    | Addressing Mode Field | Operand / Address Field |
+-------------------+-----------------------+------------------------+
```

#### Bit Rules:
* To support $k$ distinct primary opcodes $\implies \text{Opcode bits } b = \lceil \log_2 k \rceil$.
* To address $r$ CPU registers $\implies \text{Register selector bits } = \lceil \log_2 r \rceil$.
* For memory address space of $N$ locations $\implies \text{Memory address bits } = \lceil \log_2 N \rceil$.

---

### 3.2 Fixed vs. Variable Length Encoding

| Metric | Fixed-Length Instructions | Variable-Length Instructions |
| :--- | :--- | :--- |
| **Decoding Speed** | Fast & simple (fixed field positions) | Slower & complex (requires multi-stage decoding) |
| **Pipeline Alignment** | High pipeline efficiency; predictable fetch | Complex instruction fetch unit (IFU) split |
| **Code Density** | Lower density; wastes bits on short operations | High density; compact representation |
| **Typical Usage** | RISC Architectures (ARM, MIPS, RISC-V) | CISC Architectures (x86) |

---

### 3.3 The Expanding Opcode Technique

Used in fixed-length instruction formats to accommodate instructions with different numbers of address fields.

#### Core Principle:
Free bits from unused operand fields in short-address formats are added to the opcode field to expand the number of available operation codes.

```
Long-Address Format (e.g. 2-address):
+----------------+----------------+----------------+
| Opcode (4 bits)| Address 1 (6b) | Address 2 (6b) |  Total = 16 bits
+----------------+----------------+----------------+

Expanded Short-Address Format (e.g. 1-address):
+---------------------------------+----------------+
|     Expanded Opcode (10 bits)   | Address 1 (6b) |  Total = 16 bits
+---------------------------------+----------------+
```

#### Step-by-Step Solving Rule for Expanding Opcode Numericals:
1. Identify total instruction length $I$ bits and operand field size $A$ bits.
2. Determine available combinations for $k$-address format: $2^{\text{Opcode bits}}$.
3. If $m$ patterns are consumed by $k$-address instructions, remaining unused prefix patterns $= 2^{\text{Opcode bits}} - m$.
4. Multiply unused patterns by $2^A$ to get available opcodes for $(k-1)$-address instructions.

> 🚨 **GATE Trap:** You CANNOT simply add $2^{\text{bits}}$ independently across different formats! Codes used by long-address formats act as reserved prefixes and are **unavailable** to shorter-address formats.

---

## 4. Instruction Cycle, Registers & Micro-operations

### 4.1 Key CPU Registers & Functions

```
+---------------+-----------------------------------------------------------------------+
| Register      | Description & Function                                                |
+---------------+-----------------------------------------------------------------------+
| PC            | Program Counter: Holds memory address of the NEXT instruction         |
| MAR           | Memory Address Register: Holds address currently being read/written   |
| MBR / MDR     | Memory Buffer/Data Register: Holds data read from or written to memory|
| IR            | Instruction Register: Holds the CURRENTLY EXECUTING instruction opcode |
| AC            | Accumulator: Stores intermediate ALU results in 1-operand systems     |
+---------------+-----------------------------------------------------------------------+
```

---

### 4.2 Complete Instruction Cycle Flow

```
Fetch Instruction  ──>  Decode & Evaluate EA  ──>  Execute Operation  ──>  Check Interrupt
        ^                                                                       │
        └────────────────────────── No Interrupt ───────────────────────────────┘
                                        │
                                   Yes (Pending)
                                        │
                                        v
                            Service Interrupt (ISR)
                            1. Push Return Address to Stack
                            2. Save PSW (Flags)
                            3. Load ISR Address into PC
```

---

### 4.3 Canonical Fetch Phase Micro-operations

```text
T1: MAR  <- PC
T2: MDR  <- M[MAR],  PC <- PC + d   (d = instruction byte length)
T3: IR   <- MDR
```

> 🚨 **GATE Trap:** Transfers that share a common data/address bus cannot occur in the same clock cycle step $T_i$. If MAR receives PC in $T_1$, reading memory over the data bus into MDR must wait until $T_2$.

---

## 5. Data Path & Control Unit Architecture

### 5.1 Hardwired Control vs. Microprogrammed Control

```
Hardwired CU:                Combinational Logic Gates / FSM  ──> Control Signals (Fast)
Microprogrammed CU:          Control Memory (ROM/PROM)        ──> Microinstructions (Flexible)
```

| Property | Hardwired Control Unit | Microprogrammed Control Unit |
| :--- | :--- | :--- |
| **Implementation** | Flip-flops, decoders, combinational logic | Control Memory (ROM) storing microprograms |
| **Speed** | Extremely fast (gate delays only) | Slower (requires ROM access cycles) |
| **Flexibility** | Rigid; hard to modify (requires redesign) | Flexible; easily updated by updating ROM |
| **Control Signal Source** | Generated directly by logic circuits | Decoded from microinstruction fields |
| **Architecture** | RISC Processors | CISC Processors |

---

### 5.2 Horizontal vs. Vertical Microprogramming

```
Horizontal Microinstruction:
+-----------+-----------+-----------+--- ... ---+---------------+
| Control 1 | Control 2 | Control 3 |           | Branch / Next |  Wide control word (Unencoded)
+-----------+-----------+-----------+--- ... ---+---------------+

Vertical Microinstruction:
+---------------------+---------------------+---------------+
| Encoded Field 1 (3b)| Encoded Field 2 (4b)| Next Address  |  Narrow control word (Encoded)
+---------------------+---------------------+---------------+
         │                     │
         v                     v
     [Decoder]             [Decoder]
         │                     │
         v                     v
   Control Signals       Control Signals
```

| Feature | Horizontal Microprogramming | Vertical Microprogramming |
| :--- | :--- | :--- |
| **Control Word Width** | Wide (many bits) | Narrow (few bits) |
| **Decoding Needed** | Minimal to None | Requires decoders ($n \to 2^n$) |
| **Parallelism** | High parallel micro-ops per cycle | Low parallelism per cycle |
| **Execution Speed** | Faster | Slower (decoder propagation delay) |
| **Control Memory Size** | Larger total memory footprint | Smaller, compact control memory |

#### Encoding Mutually Exclusive Signals Formula:
If a group has $g$ mutually exclusive control signals:
* If "No Signal Active" is a valid state $\implies \text{Bits needed } = \lceil \log_2 (g + 1) \rceil$.
* If exactly one signal is ALWAYS active $\implies \text{Bits needed } = \lceil \log_2 g \rceil$.

---

## 6. Floating-Point Representation (IEEE 754 Standard)

### 6.1 Bit Breakdown & Formulas

```
Single Precision (32-bit):
 1 bit       8 bits                  23 bits
+------+----------------+-----------------------------------------------+
| Sign | Exponent (E)   | Fraction / Mantissa (F)                       |
+------+----------------+-----------------------------------------------+
 Bit 31   Bit 30...23     Bit 22...0

Double Precision (64-bit):
 1 bit       11 bits                 52 bits
+------+-------------------+--------------------------------------------+
| Sign | Exponent (E)      | Fraction / Mantissa (F)                    |
+------+-------------------+--------------------------------------------+
 Bit 63   Bit 62...52        Bit 51...0
```

| Precision | Total Bits | Sign ($S$) | Exponent ($E$) | Fraction ($F$) | Exponent Bias |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Single Precision** | 32 | 1 bit | 8 bits | 23 bits | **127** ($2^{8-1} - 1$) |
| **Double Precision** | 64 | 1 bit | 11 bits | 52 bits | **1023** ($2^{11-1} - 1$) |

#### Normalized Number Value Equation:
$$V = (-1)^S \times (1.F)_2 \times 2^{E - \text{Bias}} \quad \text{for } 0 < E < \text{all-ones}$$

#### Subnormal / Denormalized Number Value Equation ($E = 0, F \neq 0$):
$$V = (-1)^S \times (0.F)_2 \times 2^{1 - \text{Bias}}$$

---

### 6.2 IEEE 754 Special Values Table

| Exponent ($E$) | Fraction ($F$) | Value Represented |
| :---: | :---: | :--- |
| $\text{All } 0\text{s} \; (0)$ | $\text{All } 0\text{s}$ | Signed Zero ($\pm 0$) |
| $\text{All } 0\text{s} \; (0)$ | Non-zero | Subnormal / Denormalized Number |
| $1 \le E \le 254$ | Any pattern | Normalized Finite Real Number |
| $\text{All } 1\text{s} \; (255)$ | $\text{All } 0\text{s}$ | Signed Infinity ($\pm \infty$) |
| $\text{All } 1\text{s} \; (255)$ | Non-zero | Not a Number ($\text{NaN}$) |

> 🚨 **GATE Traps:**
> 1. IEEE 754 mantissa uses **sign-magnitude format**, NOT Two's Complement!
> 2. Subnormal numbers ($E=0$) use exponent $1 - \text{Bias}$ (i.e. $-126$ in single precision), NOT $0 - \text{Bias}$.

---

## 7. CPU Performance & Timing

### 7.1 Core Equations

$$\text{CPU Execution Time} = \text{Instruction Count (IC)} \times \text{Average CPI} \times \text{Clock Cycle Time } t$$

$$\text{CPU Execution Time} = \frac{\text{IC} \times \text{CPI}}{\text{Clock Rate } f}$$

$$\text{Average CPI} = \sum_{i} (\text{Fraction}_i \times \text{CPI}_i)$$

$$\text{MIPS Rate} = \frac{\text{Clock Rate } f}{\text{Average CPI} \times 10^6}$$

$$\text{Speedup } S = \frac{\text{Execution Time of Unoptimized / Old System } (T_{\text{old}})}{\text{Execution Time of Optimized / New System } (T_{\text{new}})}$$

---

## 8. Pipelining Fundamentals

### 8.1 Ideal Uniform Stage Pipelining

```text
Non-Pipelined (Sequential):
Instr 1: [S1][S2][S3][S4]
Instr 2:             [S1][S2][S3][S4]
Instr 3:                         [S1][S2][S3][S4]

Pipelined (Ideal k=4):
Instr 1: [S1][S2][S3][S4]
Instr 2:     [S1][S2][S3][S4]
Instr 3:         [S1][S2][S3][S4]
```

For $k$ stages, $n$ instructions, uniform stage delay $\tau$:

$$\text{Total Pipelined Time } T_{\text{pipe}} = (k + n - 1) \cdot \tau$$

$$\text{Total Non-Pipelined Time } T_{\text{non-pipe}} = n \cdot k \cdot \tau$$

$$\text{Speedup } S = \frac{T_{\text{non-pipe}}}{T_{\text{pipe}}} = \frac{n \cdot k \cdot \tau}{(k + n - 1) \cdot \tau} = \frac{n \cdot k}{k + n - 1}$$

$$\lim_{n \to \infty} S = k \quad (\text{Maximum theoretical speedup equals number of stages } k)$$

$$\text{Throughput } TP = \frac{n}{(k + n - 1) \cdot \tau} \xrightarrow{n \to \infty} \frac{1}{\tau}$$

$$\text{Efficiency } E = \frac{\text{Speedup } S}{k} = \frac{n}{k + n - 1}$$

---

### 8.2 Non-Uniform Stage Delays & Latch Overhead

When pipeline stage delays are unequal ($t_1, t_2, \dots, t_k$) and register latch overhead is $d$:

$$\text{Pipeline Clock Period } \tau = \max(t_1, t_2, \dots, t_k) + d$$

$$\text{Non-Pipelined Clock Period } \tau_{\text{non-pipe}} = \left(\sum_{i=1}^k t_i\right) + d_{\text{non-pipe}}$$

> 🚨 **GATE Trap:** The pipeline clock period is governed by the **SLOWEST stage delay**, NOT the average stage delay!

---

## 9. Pipeline Hazards & Data Dependencies

### 9.1 Classification of Pipeline Hazards

```
                                +-------------------+
                                |  Pipeline Hazards |
                                +---------+---------+
                                          |
         ┌────────────────────────────────┼────────────────────────────────┐
         v                                v                                v
+------------------+             +------------------+             +------------------+
| Structural Hazard|             |   Data Hazard    |             |  Control Hazard  |
| Resource Conflict|             | Data Dependency  |             |  Branch / Jump   |
+------------------+             +------------------+             +------------------+
```

1. **Structural Hazards (Resource Conflicts):** Occur when two stages require the same physical hardware unit simultaneously (e.g., single memory port accessed for instruction fetch & data load).
   * *Remedy:* Hardware duplication (separate instruction & data caches/Harvard architecture) or inserting stalls.
2. **Data Hazards:** Occur when an instruction depends on the result of a previous instruction that is still in the pipeline.
   * *Remedy:* Operand Forwarding / Bypassing, instruction reordering (code scheduling), or stall insertion (bubbles).
3. **Control Hazards (Branch Hazards):** Occur when conditional branch decisions alter the PC value late in the pipeline.
   * *Remedy:* Branch prediction (static/dynamic), delayed branching, early target calculation, flushing wrong-path instructions.

---

### 9.2 Register Data Dependencies (RAW, WAR, WAW)

| Dependency | Category | Description | Pipeline Occurrence |
| :--- | :--- | :--- | :--- |
| **RAW (Read After Write)** | **True Dependence** | $I_2$ tries to read operand before $I_1$ writes it | **Occurs in standard in-order RISC pipelines** |
| **WAR (Write After Read)** | **Anti-Dependence** | $I_2$ tries to write operand before $I_1$ reads it | Occurs only in out-of-order execution engines |
| **WAW (Write After Write)**| **Output Dependence**| $I_2$ tries to write operand before $I_1$ writes it | Occurs only in out-of-order execution engines |

⚡ **Shortcut:** RAW is the ONLY true data dependency. WAR and WAW are false/name dependencies caused by register resource limitations (resolved by register renaming).

---

### 9.3 Operand Forwarding & Stalls

```text
Without Forwarding (Stalls required):
I1: ADD R1, R2, R3   [IF][ID][EX][MEM][WB] (R1 written in WB)
I2: SUB R4, R1, R5               [IF] [ID]  [ID]  [ID]  [EX][MEM][WB] (Stalls until WB)

With Operand Forwarding:
I1: ADD R1, R2, R3   [IF][ID][EX]──┐[MEM][WB]
                              │    │ (Forwarded directly from EX/MEM buffer)
                              v    v
I2: SUB R4, R1, R5       [IF][ID] [EX][MEM][WB]
```

> 🚨 **GATE Load-Use Trap:** Even with operand forwarding enabled, a **LOAD instruction followed immediately by a dependent instruction** requires **1 STALL cycle** in a 5-stage RISC pipeline because memory data is available only after the `MEM` stage.

---

## 10. GATE-Style Practice MCQs with Detailed Solutions

### Q1. Memory Address Line Calculation
**Question:** A computer system has $1\text{ MB}$ byte-addressable main memory. How many address lines are required?  
A) $10$  
B) $16$  
C) $20$  
D) $24$  

> **Solution:** Option C.  
> $1\text{ MB} = 2^{20}\text{ bytes}$. Since the memory is byte-addressable, there are $2^{20}$ addressable locations.  
> Number of address lines $n = \log_2(2^{20}) = 20\text{ lines}$.

---

### Q2. Endianness Byte Storage
**Question:** The 32-bit hex value `0x12345678` is stored in a little-endian memory starting at address `0x2000`. What byte is stored at address `0x2000`?  
A) `0x12`  
B) `0x34`  
C) `0x56`  
D) `0x78`  

> **Solution:** Option D.  
> In little-endian, the Least Significant Byte (LSB = `0x78`) is placed at the lowest address (`0x2000`).

---

### Q3. Addressing Mode Identification
**Question:** Which addressing mode allows the operand to be directly specified inside the instruction word itself without any memory access?  
A) Direct Addressing  
B) Immediate Addressing  
C) Register Indirect  
D) Indirect Addressing  

> **Solution:** Option B.  
> Immediate addressing embeds the constant operand directly in the instruction field.

---

### Q4. Microprogrammed vs. Hardwired CU
**Question:** Which of the following statements is TRUE regarding Control Unit design?  
A) Microprogrammed control is faster than hardwired control.  
B) Hardwired control is easier to modify than microprogrammed control.  
C) Hardwired control relies on combinational logic gates, whereas microprogrammed control uses control memory.  
D) RISC processors exclusively use vertical microprogramming.  

> **Solution:** Option C.  
> Hardwired CU uses hardcoded logic gates/FSMs (faster), while Microprogrammed CU uses software-like microprograms stored in control ROM.

---

### Q5. IEEE 754 Floating-Point Exponent Bias
**Question:** In IEEE 754 single-precision 32-bit floating-point format, what is the bias added to the actual exponent?  
A) 63  
B) 127  
C) 255  
D) 1023  

> **Solution:** Option B.  
> Bias $= 2^{k-1} - 1 = 2^{8-1} - 1 = 127$.

---

### Q6. Pipeline Execution Cycles Calculation
**Question:** An ideal 5-stage pipeline executes 20 instructions. Assuming no hazards or stalls, how many clock cycles are required to complete execution?  
A) 20  
B) 24  
C) 25  
D) 100  

> **Solution:** Option B.  
> $\text{Cycles} = k + n - 1 = 5 + 20 - 1 = 24\text{ cycles}$.

---

### Q7. Non-Uniform Pipeline Clock Selection
**Question:** A 4-stage pipeline has stage delays of $2\text{ ns}$, $5\text{ ns}$, $3\text{ ns}$, and $4\text{ ns}$. Latch delay is negligible. What is the clock period of the pipeline?  
A) $2\text{ ns}$  
B) $3.5\text{ ns}$  
C) $5\text{ ns}$  
D) $14\text{ ns}$  

> **Solution:** Option C.  
> Pipeline clock period $= \max(t_i) = \max(2, 5, 3, 4) = 5\text{ ns}$.

---

### Q8. IEEE 754 Special Value Identification
**Question:** In single-precision IEEE 754 format, if Exponent $E = 255$ ($\text{all } 1\text{s}$) and Fraction $F = 0$, the representation corresponds to:  
A) Zero  
B) Infinity ($\pm \infty$)  
C) NaN (Not a Number)  
D) Subnormal number  

> **Solution:** Option B.  
> Exponent all 1s with zero fraction represents $\pm \infty$. Non-zero fraction represents NaN.

---

## 11. Final Exam Quick Revision Sheet

### ⚡ Must-Memorize Formulas

1. **Addresses Space:** $\text{Locations } N = 2^n \iff n = \lceil \log_2 N \rceil$
2. **Register Bit Selector:** $\text{Bits} = \lceil \log_2 (\text{Number of registers}) \rceil$
3. **CPU Execution Time:** $\text{CPU Time} = \frac{\text{IC} \times \text{CPI}}{f} = \text{IC} \times \text{CPI} \times \tau$
4. **MIPS Rating:** $\text{MIPS} = \frac{f}{\text{CPI} \times 10^6}$
5. **Ideal Pipeline Cycles:** $\text{Cycles} = k + n - 1$
6. **Pipeline Speedup:** $S = \frac{n \cdot k}{k + n - 1} \xrightarrow{n \to \infty} k$
7. **Non-Uniform Pipeline Clock:** $\tau_{\text{pipe}} = \max(t_1, t_2, \dots, t_k) + d$
8. **IEEE Single Precision (32-bit):** Sign: 1b, Exponent: 8b, Fraction: 23b, Bias: 127
9. **IEEE Double Precision (64-bit):** Sign: 1b, Exponent: 11b, Fraction: 52b, Bias: 1023
10. **Normalized Real Value:** $(-1)^S \times 1.F \times 2^{E - \text{Bias}}$

---

### ⚠️ Must-Memorize Exam Traps

* **Trap 1 (Byte vs. Word):** Memory address lines depend on *addressable units*. If memory is word-addressable, divide total bytes by word size before applying $\log_2$.
* **Trap 2 (Endianness):** Endianness only affects multi-byte ordering across memory addresses, NOT bit order inside a single byte.
* **Trap 3 (Expanding Opcode):** Opcodes allocated to $k$-address formats consume prefix bit patterns that CANNOT be reused by $(k-1)$-address formats.
* **Trap 4 (Microprogrammed vs. Horizontal):** Microprogrammed is a CU design strategy; Horizontal/Vertical are microinstruction encoding styles inside control memory.
* **Trap 5 (IEEE 754 Significand Format):** IEEE 754 uses Sign-Magnitude representation for mantissa, NEVER 2's complement.
* **Trap 6 (Pipeline Clock):** Pipeline clock rate is governed by the SLOWEST stage + register delay, never the average stage delay.
* **Trap 7 (Throughput vs. Latency):** Pipelining increases instruction throughput, NOT the latency/execution time of an individual instruction.
* **Trap 8 (RISC Data Hazards):** In standard in-order 5-stage RISC pipelines, WAR and WAW hazards do not occur. RAW is the primary data hazard.
