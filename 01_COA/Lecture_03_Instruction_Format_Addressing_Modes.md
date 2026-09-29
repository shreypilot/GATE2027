# Computer Organization & Architecture (COA)
## Lecture 03–04: Instruction Formats, Addressing Modes, Expanding Opcode & Encoding

**Source:** GfG GATE CS Crash Course (Vijay Sir) — class screenshots (Machine Instruction & Addressing Modes – 1 / 2, Addressing Mode Part-II)

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **Expanding Opcode** | Remaining encodings after $n$-address instructions are allocated |
| ★★★★★ | **Instruction Encoding / Immediate Width** | Opcode + register fields + leftover immediate bits |
| ★★★★★ | **Effective Address (EA)** | Direct, Register, Register-Indirect, Auto Inc/Dec |
| ★★★★★ | **0 / 1 / 2 / 3 address formats** | Stack vs Accumulator vs General-register machines |
| ★★★★☆ | **Why addressing modes exist** | Same address-field bits can mean memory / register / constant |
| ★★★★☆ | **Instruction length in memory** | $\lceil \text{bits}/8 \rceil$ bytes; word-count layout of a program |
| ★★★☆☆ | **Operand forwarding vs memory copies** | GATE-2004 data-forwarding sequence |

---

## 1. Data vs Instruction

* **Data** is a binary sequence **bound to a value**. Example: `1101` $\Rightarrow$ decimal $13$.
* An **instruction** is a binary sequence **bound to an operation** (opcode) plus optional **address fields (AF)** that name operands.

---

## 2. Instruction Formats (0 / 1 / 2 / 3 Address)

Fixed-length or variable-length instructions are classified by **how many address fields** they carry.

| Format | Layout | Typical machine | Example |
| :--- | :--- | :--- | :--- |
| **3AF / 3AI** | `OPCODE \| AF1 \| AF2 \| AF3` | General-register | `ADD R1, R2, R3` $\Rightarrow$ $R1 \leftarrow R2 + R3$ |
| **2AF / 2AI** | `OPCODE \| AF1 \| AF2` | General-register | `ADD R1, R2` $\Rightarrow$ $R1 \leftarrow R1 + R2$ (dest = first src) |
| **1AF / 1AI** | `OPCODE \| AF1` | **Accumulator** | `ADD M[4000]` $\Rightarrow$ $AC \leftarrow AC + M[4000]$ |
| **0AF / 0AI** | `OPCODE` only | **Stack** | `ADD` pops two TOS values, pushes the sum |

**GATE mapping (as taught):**
* 3-address and 2-address $\rightarrow$ **General Register** organization
* 1-address $\rightarrow$ **Accumulator** organization
* 0-address $\rightarrow$ **Stack** organization

### Instruction stored as whole bytes

If an instruction is $67$ bits long on a byte-addressable machine:

$$\text{Bytes occupied} = \left\lceil \frac{67}{8} \right\rceil = 9 \text{ Bytes}$$

Never store a “fraction of a byte”; always **ceil** to the next byte.

---

## 3. Why Addressing Modes Are Used

High-level languages need **constants, variables, and pointers**. Assembly implements those features with **different addressing modes**.

The **same bit pattern in the address field** can mean three different things depending on the mode:

```text
Instruction (example 9 bits):  OPCODE (5) | AF (4)
AF bits = 0110 = 6

  (i) Direct / memory  →  location M[6]
 (ii) Register         →  register R6
(iii) Immediate        →  constant value 6
```

Without a mode field (or an opcode that implies a mode), the CPU cannot decide **memory vs register vs literal**.

---

## 4. Addressing Modes and Effective Address

**Effective Address (EA)** = address of the **operand** (not of the instruction).

### 4.1 Register Addressing

Same idea as direct addressing, except the address field names a **register**, not a memory cell.

$$\mathbf{EA = R}$$

Operand is inside the **register file**. Fastest after implied/accumulator.

### 4.2 Register Indirect Addressing

Analogous to memory-indirect: the address field names a register that **holds a memory address**.

$$\mathbf{EA = (R)} \quad \text{i.e. } EA = \text{contents of } R$$

```text
OPCODE | AF = R2
R2 contains 62  →  EA = 62  →  DATA is in Memory[62]
```

### 4.3 Auto-Increment & Auto-Decrement

Same as register-indirect, **plus** the register is updated:

| Mode | When the register changes | Typical use |
| :--- | :--- | :--- |
| **Auto-decrement** | **Pre-decrement** (`--R`, then use $EA=(R)$) | Push / stack grow-down |
| **Auto-increment** | **Post-increment** (use $EA=(R)$, then $R{+}{+}$) | Pop / string / array walk |

**GATE trap:** decrement is **pre**; increment is **post** (as taught in class). If a paper states the opposite convention, follow the paper.

### 4.4 Immediate / Direct / Indirect (recall)

| Mode | EA / operand |
| :--- | :--- |
| Immediate | Operand = AF bits themselves (no memory) |
| Direct | $EA = AF$ |
| Memory indirect | $EA = M[AF]$ |
| PC-relative | $EA = PC + AF$ (signed displacement) |
| Indexed / based | $EA = R_{\text{index/base}} + AF$ |

---

## 5. Expanding Opcode Technique

Used in **fixed-length** instruction sets that must support **several formats** (3-addr, 2-addr, 1-addr, 0-addr) without wasting encodings.

> Opcode length **grows** as the number of address fields **shrinks**. Leftover bit patterns that are unused as “short opcodes” become **longer opcodes** for instructions with fewer addresses.

### Worked GATE-style example

> Processor supports **6-bit** instructions and a **4-bit** address field. There exist **2 one-address** instructions. How many **0-address** instructions can be formulated?

* Total encodings $= 2^{6} = 64$.
* One-address format: opcode leftover $= 6-4 = 2$ bits, but each 1-address instruction still consumes **all $2^{4}=16$** patterns of the address field.
* Two 1-address instructions use $2 \times 16 = 32$ encodings.
* Remaining encodings for 0-address (opcode uses the full 6 bits):

$$64 - 32 = \mathbf{32}$$

**General recipe:**

$$\text{Remaining 0-address} = 2^{L} - \sum_{i} N_{i}\,2^{A_{i}}$$

where $L$ = instruction length, $N_i$ = number of instructions of a format, $A_i$ = bits in that format’s address field(s).

---

## 6. Instruction Encoding — Immediate Field Width

> **GATE:** A machine has a **32-bit** architecture, **1-word** instructions, **64** registers (each 32 bits). It must support **45** instructions that have an **immediate** plus **two register** operands. Immediate is an **unsigned** integer. Maximum immediate value = ?

Layout of the 32-bit word:

```text
| OPCODE | Reg1 | Reg2 | Immediate |
|  6 bit | 6 b  | 6 b  |  14 bit   |
```

* $64$ registers $\Rightarrow$ $\log_2 64 = 6$ bits per register field.
* $45$ opcodes $\Rightarrow$ need $\lceil \log_2 45 \rceil = 6$ opcode bits.
* Immediate bits $= 32 - 6 - 6 - 6 = 14$.
* Max unsigned value $= 2^{14} - 1 = \mathbf{16383}$.

---

## 7. Program Layout, Word Size, Interrupt Return Address

Classic layout question (byte-addressable memory, **word = 32 bits = 4 bytes**, program loaded at **1000**):

| Instruction | Size (words) | Addresses occupied |
| :--- | :---: | :--- |
| `MOV R1, 5000` | 2 | $1000$ – $1007$ |
| `MOV R2, (R1)` | 1 | $1008$ – $1011$ |
| `ADD R2, R3` | 1 | $1012$ – $1015$ |
| `MOV 6000, R2` | 2 | $1016$ – $1023$ |
| `HALT` | 1 | $1024$ – $1027$ |

If an interrupt occurs **during execution** of `MOV 6000, R2`, the **return address** pushed is the address of the **next instruction** = **$1024$** (start of `HALT`).

**Never** save the address of the instruction currently executing.

### Branch / JMP and PC after fetch

Memory:

```text
1000  I1
1001  I2
1002  I3 = JMP 1051
1003  I4
...
1051  target
```

* After **fetch** of $I_1$: $PC = 1001$
* After fetch of $I_2$: $PC = 1002$
* After fetch of $I_3$: $PC = 1003$ (PC already points to sequential next)
* When $I_3$ is **decoded/executed**, $PC \leftarrow 1051$ (branch target)

**Control-hazard idea:** instructions already fetched after the branch ($I_4$ at $1003$) may be wrong and must be flushed unless predicted correctly.

---

## 8. Data Forwarding vs Memory Copies (GATE 2004)

Initial idea: $R1=10,\; R2=20,\; R3=30,\; M[100]=40$. After the block:

$$R1 \rightarrow M[100];\quad M[100] \rightarrow R2;\quad M[100] \rightarrow R3$$

all of $R1,R2,R3,M[100]$ become $10$.

With **internal data forwarding**, copies through memory can be replaced by register-to-register moves from the **producer** $R1$:

```text
R1 → R2
R1 → R3
R1 → M[100]
```

(The lecture marked this replacement as the correct option.)

---

## 9. Extra Endianness Note (arrays)

Endianness applies to **each multi-byte data item**, not to a whole array as one blob.

* Array of **1-byte** elements: Little-Endian and Big-Endian layouts of `D1, D2` look the **same** (each element is one byte).
* Array of **2-byte** elements: each `D1`, `D2` is internally swapped (LE) or not (BE).

---

## 10. GATE Formula Sheet

1. Bytes for an instruction: $\lceil L_{\text{bits}}/8 \rceil$
2. Register-field width: $\lceil \log_2(\#\text{registers}) \rceil$
3. Opcode width: $\lceil \log_2(\#\text{opcodes}) \rceil$ (or more if expanding opcode)
4. Expanding opcode leftover encodings: $2^{L} - \sum N_i 2^{A_i}$
5. Register: $EA=R$; Register-indirect: $EA=(R)$
6. Auto-dec = **pre**; auto-inc = **post**
7. Interrupt / CALL return address = address of **next** instruction
8. After fetch, $PC$ already holds sequential next; branch overwrites $PC$ only in execute
