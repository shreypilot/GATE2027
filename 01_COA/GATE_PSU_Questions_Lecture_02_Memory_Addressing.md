# Computer Organization & Architecture (COA)
## GATE & PSU Question Bank: Lecture 02 — Memory Organization, Addressing Lines & Address Ranges

---

## 📌 Master Formula & Concept Sheet

### 1. Memory Dimensions & Hardware Matching
* **Memory Spec:** $N \times m = 2^n \times m$
  * $N = 2^n =$ Total number of addressable memory locations / cells.
  * $n =$ Number of **Address Lines** = Address Bus Width = Minimum size of **MAR** (Memory Address Register).
  * $m =$ Number of **Data Lines** = Data Bus Width = Cell Width = Minimum size of **MDR / MBR** (Memory Data Register).
  * **Formula:**
    $$n = \log_2(N) = \log_2\left(\frac{\text{Total Capacity}}{\text{Cell Width}}\right)$$

### 2. Powers of 2 & Rapid Address Line Lookup
* $1\text{ K} = 2^{10} \implies 10\text{ address lines}$
* $1\text{ M} = 2^{20} \implies 20\text{ address lines}$
* $1\text{ G} = 2^{30} \implies 30\text{ address lines}$
* $1\text{ T} = 2^{40} \implies 40\text{ address lines}$
* For any size $2^k \times (\text{Prefix Unit})$:
  $$n = k + \text{Prefix Power}$$
  * Example: $64\text{ MB} = 2^6 \times 2^{20}\text{ Bytes} = 2^{26}\text{ Bytes} \implies n = 26\text{ lines}$.

### 3. Hexadecimal Address Range
* Assuming memory starts at address $0$:
  * **Start Address:** $0$ ($n$ zeros in binary $\to \text{0x00}\dots\text{0}$)
  * **End Address:** $N - 1 = 2^n - 1$ ($n$ ones in binary)
* Group binary bits into nibbles (4 bits each) starting from LSB to MSB:
  * Last 4 bits: $1111_2 = \text{F}_{16}$
  * Leftover $r$ bits on MSB:
    * $1\text{ bit } (1) \implies \mathbf{1}$
    * $2\text{ bits } (11) \implies \mathbf{3}$
    * $3\text{ bits } (111) \implies \mathbf{7}$
    * $4\text{ bits } (1111) \implies \mathbf{F}$

### 4. Memory Span & Chip Capacity
* **Number of Locations ($N$):**
  $$N = (\text{End Address} - \text{Start Address} + 1)_{10}$$
* **Memory Chips Required:**
  $$\text{Total Chips Required} = \frac{\text{Target Memory Size (in bits)}}{\text{Available Chip Size (in bits)}} = \left(\frac{N_{\text{target}}}{N_{\text{chip}}}\right) \times \left(\frac{m_{\text{target}}}{m_{\text{chip}}}\right)$$

---

# PART 1: Address Line ($n$) & Data Line ($m$) Determination

### 🔹 Question 1.1 [Direct Lecture Analogue / Concept Check]
**Q:** A computer memory has a total capacity of $16\text{ MB}$ and is byte-addressable. Determine:
1. The number of address lines required.
2. The number of data lines required.
3. The starting address and ending address in hexadecimal notation (assume the starting address is 0).

**Solution:**
1. **Given:**
   * Total Capacity $= 16\text{ MB} = 2^4 \times 2^{20}\text{ Bytes} = 2^{24}\text{ Bytes}$.
   * Since it is byte-addressable, each cell stores $1\text{ Byte} = 8\text{ bits}$.
   * Therefore, memory is represented as:
     $$2^{24} \times 8\text{ bits}$$
2. **Address Lines ($n$):**
   * $N = 2^{24} \implies \mathbf{n = 24\text{ address lines}}$.
3. **Data Lines ($m$):**
   * Cell width $= 8\text{ bits} \implies \mathbf{m = 8\text{ data lines}}$.
4. **Hexadecimal Range:**
   * Total cells $N = 2^{24}$.
   * Start Address: 24 zeros $\implies \mathbf{\text{0x000000}}$ (6 hex digits).
   * End Address: $2^{24} - 1$, which is 24 ones in binary:
     $$\underbrace{1111}_{\text{F}} \quad \underbrace{1111}_{\text{F}} \quad \underbrace{1111}_{\text{F}} \quad \underbrace{1111}_{\text{F}} \quad \underbrace{1111}_{\text{F}} \quad \underbrace{1111}_{\text{F}} \implies \mathbf{\text{0xFFFFFF}}$$
* **Final Answer:** $n = 24$, $m = 8$, Range = $\text{0x000000}$ to $\text{0xFFFFFF}$.

---

### 🔹 Question 1.2 [Word-Addressable vs. Byte-Addressable]
**Q:** A $128\text{ MB}$ main memory is organized such that each word is $32\text{ bits}$. 
Compare the number of address lines required if the memory is:
1. Byte-addressable
2. Word-addressable

**Solution:**
* **Total Capacity in bits:**
  $$\text{Capacity} = 128\text{ MB} = 128 \times 2^{20} \times 8\text{ bits} = 2^7 \times 2^{20} \times 2^3\text{ bits} = 2^{30}\text{ bits}$$

* **Case 1: Byte-Addressable ($m = 8\text{ bits} = 1\text{ Byte}$)**
  $$N_{\text{byte}} = \frac{128\text{ MB}}{1\text{ Byte}} = 128\text{ M} = 2^7 \times 2^{20} = 2^{27}\text{ cells}$$
  $$\mathbf{n = 27\text{ address lines}}$$

* **Case 2: Word-Addressable ($m = 32\text{ bits} = 4\text{ Bytes}$)**
  $$N_{\text{word}} = \frac{128\text{ MB}}{4\text{ Bytes}} = 32\text{ M} = 2^5 \times 2^{20} = 2^{25}\text{ cells}$$
  $$\mathbf{n = 25\text{ address lines}}$$

> **Key Takeaway:** A word-addressable memory needs **2 fewer address lines** than a byte-addressable memory of the same capacity because each address encompasses 4 bytes ($2^2$ bytes).

---

### 🔹 Question 1.3 [Nibble-Addressable System — ISRO CS]
**Q:** A special-purpose DSP architecture uses a memory of size $512\text{ KB}$. If the memory is **nibble-addressable** ($1\text{ nibble} = 4\text{ bits}$), how many address lines and data lines are required to interface this memory with the CPU?

**Solution:**
1. **Total Capacity in bits:**
   $$\text{Capacity} = 512\text{ KB} = 2^9 \times 2^{10} \times 8\text{ bits} = 2^{19} \times 2^3\text{ bits} = 2^{22}\text{ bits}$$
2. **Cell Size (Nibble Addressable):**
   $$m = 1\text{ nibble} = 4\text{ bits} = 2^2\text{ bits} \implies \mathbf{m = 4\text{ data lines}}$$
3. **Number of Addressable Cells ($N$):**
   $$N = \frac{\text{Total Capacity}}{\text{Cell Size}} = \frac{2^{22}\text{ bits}}{2^2\text{ bits}} = 2^{20}\text{ cells}$$
4. **Number of Address Lines ($n$):**
   $$n = \log_2(2^{20}) = \mathbf{20\text{ address lines}}$$
* **Final Answer:** $20\text{ Address Lines}$, $4\text{ Data Lines}$.

---

# PART 2: Hexadecimal Address Ranges & Span Calculations

### 🔹 Question 2.1 [Hex Address Range Determination — GATE Pattern]
**Q:** A byte-addressable memory system has $19$ address lines. What is the address range in hexadecimal notation?
- **(A)** `0x00000` to `0x3FFFF`
- **(B)** `0x00000` to `0x7FFFF`
- **(C)** `0x00000` to `0xFFFFF`
- **(D)** `0x0000` to `0x7FFF`

**Solution:**
* **Correct Answer:** **(B)**
* **Explanation:**
  1. $n = 19$ address lines $\implies$ Total locations $N = 2^{19}$.
  2. The addresses range from $0$ to $2^{19} - 1$.
  3. Writing $2^{19} - 1$ in binary (19 ones):
     $$\underbrace{111}_{3\text{ bits}} \quad \underbrace{1111}_{4\text{ bits}} \quad \underbrace{1111}_{4\text{ bits}} \quad \underbrace{1111}_{4\text{ bits}} \quad \underbrace{1111}_{4\text{ bits}}$$
  4. Converting each group from right to left:
     * $1111_2 = \text{F}$
     * $1111_2 = \text{F}$
     * $1111_2 = \text{F}$
     * $1111_2 = \text{F}$
     * $111_2 = \mathbf{7}$
  5. The end address is `0x7FFFF`.
  6. The start address is 19 zeros, which is `0x00000`.
  * Thus, the range is `0x00000` to `0x7FFFF`.

---

### 🔹 Question 2.2 [Memory Capacity from Given Address Range]
**Q [ISRO CS 2018 / NIELIT]:** A byte-organized memory chip is allocated an address range from `0x4000` to `0x7FFF`. What is the total storage capacity of this chip in Kilobytes?

**Solution:**
1. **Formula:**
   $$\text{Locations } N = (\text{End Address} - \text{Start Address} + 1)_{10}$$
2. **Hex Subtraction:**
   $$\begin{aligned}
   \text{0x7FFF} - \text{0x4000} &= \text{0x3FFF} \\
   \text{0x3FFF} + 1 &= \text{0x4000}
   \end{aligned}$$
3. **Convert Hex to Decimal:**
   $$\text{0x4000} = 4 \times 16^3 = 4 \times 4096 = 16,384\text{ locations}$$
   $$\text{Or: } 4 \times (2^4)^3 = 2^2 \times 2^{12} = 2^{14}\text{ locations}$$
4. **Capacity:**
   $$\text{Capacity} = 2^{14} \text{ Bytes} = 2^4 \times 2^{10}\text{ Bytes} = \mathbf{16\text{ KB}}$$
* **Final Answer:** **16 KB**.

---

### 🔹 Question 2.3 [Finding End Address from Base Address & Size]
**Q:** A $32\text{ KB}$ byte-addressable RAM chip starts at memory base address `0xA000`. What is the hexadecimal address of the last byte in this RAM chip?

**Solution:**
1. **Size in Bytes:**
   $$32\text{ KB} = 32 \times 1024 = 2^5 \times 2^{10} = 2^{15}\text{ Bytes} = 32,768\text{ Bytes}$$
2. **Convert Size to Hexadecimal:**
   $$32\text{ KB} = 32 \times 1024 = 32768$$
   $$32768 = 8 \times 4096 = 8 \times 16^3 = \text{0x8000}$$
3. **Formula for Last Address:**
   $$\text{Last Address} = \text{Base Address} + \text{Size} - 1$$
4. **Calculation:**
   $$\text{Last Address} = \text{0xA000} + \text{0x8000} - 1$$
   $$\text{0xA000} + \text{0x8000} = \text{0x12000}$$
   $$\text{0x12000} - 1 = \mathbf{\text{0x11FFF}}$$
* **Final Answer:** **`0x11FFF`**.

---

# PART 3: Memory Chips Interconnection & Array Design

### 🔹 Question 3.1 [Memory Chip Interfacing — Classic GATE CSE Pattern]
**Q [GATE CSE 2004 / 2014]:** How many $128\text{K} \times 8$-bit RAM chips are needed to construct a memory system of capacity $1\text{M} \times 32$-bit?
- **(A)** 16
- **(B)** 32
- **(C)** 64
- **(D)** 128

**Solution:**
* **Correct Answer:** **(B) 32**
* **Step-by-Step Derivation:**
  1. **Target Memory Specification:**
     $$N_{\text{target}} = 1\text{M} = 1024\text{K} = 2^{20}$$
     $$m_{\text{target}} = 32\text{ bits}$$
  2. **Available Chip Specification:**
     $$N_{\text{chip}} = 128\text{K} = 2^7 \times 2^{10} = 2^{17}$$
     $$m_{\text{chip}} = 8\text{ bits}$$
  3. **Row Expansion (Increasing number of locations):**
     $$\text{Chips vertically (Rows)} = \frac{N_{\text{target}}}{N_{\text{chip}}} = \frac{1024\text{K}}{128\text{K}} = \mathbf{8}$$
  4. **Column Expansion (Increasing word width):**
     $$\text{Chips horizontally (Columns)} = \frac{m_{\text{target}}}{m_{\text{chip}}} = \frac{32\text{ bits}}{8\text{ bits}} = \mathbf{4}$$
  5. **Total Chips:**
     $$\text{Total Chips} = \text{Rows} \times \text{Columns} = 8 \times 4 = \mathbf{32\text{ chips}}$$
  * Or directly via bit capacity:
     $$\text{Total Chips} = \frac{1\text{M} \times 32\text{ bits}}{128\text{K} \times 8\text{ bits}} = \frac{2^{20} \times 32}{2^{17} \times 8} = 2^3 \times 4 = 8 \times 4 = 32$$

---

### 🔹 Question 3.2 [Decoder Size for Chip Selection]
**Q:** In Question 3.1, how many address lines of the processor are used for chip selection (via a decoder), and how many address lines are connected directly to the internal address inputs of each individual $128\text{K} \times 8$ chip?

**Solution:**
1. **Total Address Lines of Target System ($1\text{M} \times 32$):**
   * Total locations $= 1\text{M} = 2^{20} \implies \mathbf{20\text{ address lines } (A_{19} - A_0)}$.
2. **Internal Address Lines for each $128\text{K} \times 8$ Chip:**
   * Each chip has $128\text{K} = 2^{17}$ locations $\implies \mathbf{17\text{ address lines } (A_{16} - A_0)}$.
3. **Chip Selection Lines (Decoder Inputs):**
   * There are 8 rows of chips.
   * To select 1 out of 8 rows, we need:
     $$\log_2(8) = \mathbf{3\text{ lines } (A_{19}, A_{18}, A_{17})}$$
   * A **$3 \times 8$ Decoder** is required.
* **Verification:** $17\text{ (internal)} + 3\text{ (decoder)} = 20\text{ total lines}$.

---

# PART 4: Previous Year GATE Questions with Detailed Solutions

### 🔹 Question 4.1 [GATE CSE 2021 — NAT 2 Marks]
**Q:** A computer system has a 36-bit virtual address space and a 32-bit physical address space. The system is byte-addressable. What is the maximum size of the physical memory in Gigabytes (GB)?

**Solution:**
1. **Given:**
   * Physical Address $= 32\text{ bits} \implies n = 32$.
   * Byte-addressable $\implies m = 8\text{ bits} = 1\text{ Byte}$.
2. **Maximum Physical Memory Capacity:**
   $$\text{Capacity} = 2^n \times 1\text{ Byte} = 2^{32}\text{ Bytes}$$
3. **Convert to Gigabytes:**
   $$2^{32}\text{ Bytes} = 2^2 \times 2^{30}\text{ Bytes} = 4 \times 1\text{ GB} = \mathbf{4\text{ GB}}$$
* **Final Answer:** **4**.

---

### 🔹 Question 4.2 [GATE CSE 2018 — NAT 1 Mark]
**Q:** Consider a processor with a 32-bit address bus and a 64-bit data bus. The processor is connected to a byte-addressable main memory. What is the maximum size of the main memory that can be addressed by this processor in Gigabytes?

**Solution:**
* **Common Student Trap:** Multiplying $2^{32} \times 64\text{ bits} = 32\text{ GB}$ ❌.
* **Why that is WRONG:** Memory is **byte-addressable**! The size of the address space is determined exclusively by the **Address Bus width** and the **addressable unit (Byte)**.
* **Correct Derivation:**
  * Number of address lines $n = 32$.
  * Number of unique addressable locations $N = 2^{32}$.
  * Since each location stores **1 Byte**:
    $$\text{Max Memory} = 2^{32} \times 1\text{ Byte} = 4\text{ GB}$$
  * The 64-bit data bus simply means that during one read cycle, 8 contiguous bytes (one 64-bit word) can be fetched simultaneously, but it does NOT expand the address space!
* **Final Answer:** **4**.

---

### 🔹 Question 4.3 [GATE CSE 2006]
**Q:** A CPU has 24-bit instructions and a 16-bit address space for data. The memory is word-addressable with a word size of 16 bits. What is the maximum capacity of the data memory in Kilobytes?
- **(A)** 32 KB
- **(B)** 64 KB
- **(C)** 128 KB
- **(D)** 256 KB

**Solution:**
* **Correct Answer:** **(C) 128 KB**
* **Step-by-Step Derivation:**
  1. Address space $= 16\text{ bits} \implies n = 16$.
  2. Number of addressable locations $N = 2^{16} = 65,536\text{ words}$.
  3. Since memory is **word-addressable** and $1\text{ word} = 16\text{ bits} = 2\text{ Bytes}$:
     $$\text{Capacity} = N \times \text{Word Size} = 2^{16} \text{ words} \times 2\text{ Bytes/word} = 2^{17}\text{ Bytes}$$
  4. Convert to Kilobytes:
     $$2^{17}\text{ Bytes} = 2^7 \times 2^{10}\text{ Bytes} = 128\text{ KB}$$

---

# PART 5: PSU Exam Practice Set (ISRO / BARC / NIELIT)

### 🔹 Question 5.1 [BARC CS / ISRO 2020]
**Q:** A memory system is configured with addresses ranging from `0x00000000` to `0x0FFFFFFF`. If the memory is byte-addressable, what is the total capacity of the memory?
- **(A)** 128 MB
- **(B)** 256 MB
- **(C)** 512 MB
- **(D)** 1 GB

**Solution:**
* **Correct Answer:** **(B) 256 MB**
* **Derivation:**
  1. End address $= \text{0x0FFFFFFF}$.
  2. Total locations $N = \text{0x0FFFFFFF} - \text{0x00000000} + 1 = \text{0x10000000}$.
  3. Convert to decimal / powers of 2:
     $$\text{0x10000000} = 1 \times 16^7 = (2^4)^7 = 2^{28}\text{ locations}$$
  4. Since it is byte-addressable:
     $$\text{Capacity} = 2^{28}\text{ Bytes} = 2^8 \times 2^{20}\text{ Bytes} = 256\text{ MB}$$

---

### 🔹 Question 5.2 [NIELIT Scientist 'B' 2022]
**Q:** A processor requires 28 address lines and 16 data lines. Which of the following statements is/are correct?
1. The memory capacity is $512\text{ MB}$ if it is byte-addressable.
2. The width of MAR is 28 bits and MDR is 16 bits.
3. The total number of addressable memory cells is $2^{28}$.

- **(A)** 1 and 2 only
- **(B)** 2 and 3 only
- **(C)** 1 and 3 only
- **(D)** 1, 2, and 3

**Solution:**
* **Correct Answer:** **(D) 1, 2, and 3**
* **Verification:**
  1. With 28 address lines, total cells $N = 2^{28}$.
  2. If each cell is byte-addressable ($1\text{ Byte}$), total capacity $= 2^{28}\text{ Bytes} = 256\text{ MB}$.  
     *Wait!* Look closely at the data lines: Data lines $= 16\text{ bits} = 2\text{ Bytes}$.
     * If the memory cell width $m = 16\text{ bits} = 2\text{ Bytes}$ (Word addressable):  
       $$\text{Capacity} = 2^{28} \times 2\text{ Bytes} = 2^{29}\text{ Bytes} = 512\text{ MB}$$
     * But statement 1 specifies: *"if it is byte-addressable"*. If byte-addressable, $2^{28}$ bytes $= 256\text{ MB}$.
     * Therefore, statement 1 is FALSE if byte-addressable, but TRUE if 16-bit word-addressable!
     * Hence, only statements **2 and 3** are strictly correct under the byte-addressable premise!
* **Correct Choice:** **(B) 2 and 3 only**.
* **Trap Alert:** Always verify whether the capacity refers to $2^n \times 1\text{ Byte}$ or $2^n \times (\text{data bus width / 8})$!

---

### 🔹 Question 5.3 [DRDO CS Exam]
**Q:** An embedded microcontroller has an internal RAM block spanning from address `0x0080` to `0x00FF`. How many bytes of RAM are present in this block?
- **(A)** 127 Bytes
- **(B)** 128 Bytes
- **(C)** 256 Bytes
- **(D)** 64 Bytes

**Solution:**
* **Correct Answer:** **(B) 128 Bytes**
* **Derivation:**
  $$\text{Count} = (\text{0x00FF} - \text{0x0080} + 1)$$
  $$\text{0x00FF} - \text{0x0080} = \text{0x007F} = 7 \times 16 + 15 = 112 + 15 = 127$$
  $$\text{Total Bytes} = 127 + 1 = \mathbf{128\text{ Bytes}}$$
  * Alternatively: $\text{0x7F} + 1 = \text{0x80} = 8 \times 16 = 128\text{ Bytes}$.

---

---

# PART 6: Byte Ordering & Endianness Questions (GATE & PSUs)

### 🔹 Question 6.1 [Direct Lecture Analogue — High Yield]
**Q:** A 32-bit integer `0x1A3D476E` is stored in a byte-addressable memory starting at location `1000`.
1. What byte value is stored at address `1000` and `1003` in a **Little-Endian** system?
2. What byte value is stored at address `1000` and `1003` in a **Big-Endian** system?

**Solution:**
* Given 32-bit data: `0x1A3D476E`
  * $\text{MSB (Big End)} = \text{0x1A}$
  * $\text{Byte 2} = \text{0x3D}$
  * $\text{Byte 1} = \text{0x47}$
  * $\text{LSB (Little End)} = \text{0x6E}$

* **1. Little-Endian System:**
  * Rule: **Least Significant Byte $\to$ Lowest Address (`1000`)**.
  * Address `1000` stores: **`0x6E`**
  * Address `1001` stores: **`0x47`**
  * Address `1002` stores: **`0x3D`**
  * Address `1003` stores: **`0x1A`** (MSB at Highest Address)

* **2. Big-Endian System:**
  * Rule: **Most Significant Byte $\to$ Lowest Address (`1000`)**.
  * Address `1000` stores: **`0x1A`**
  * Address `1001` stores: **`0x3D`**
  * Address `1002` stores: **`0x47`**
  * Address `1003` stores: **`0x6E`** (LSB at Highest Address)

---

### 🔹 Question 6.2 [GATE CSE 2004]
**Q:** Consider a 32-bit integer variable `X` initialized to `0x12345678` stored at base address `0x100` in a byte-addressable memory. In a **Little-Endian** architecture, what is the value stored in the byte at memory location `0x102`?
- **(A)** `0x12`
- **(B)** `0x34`
- **(C)** `0x56`
- **(D)** `0x78`

**Solution:**
* **Correct Answer:** **(B) `0x34`**
* **Step-by-Step Derivation:**
  1. Multi-byte representation of `0x12345678`:
     * Byte 0 (LSB) = `0x78`
     * Byte 1 = `0x56`
     * Byte 2 = `0x34`
     * Byte 3 (MSB) = `0x12`
  2. Memory mapping under **Little-Endian** starting at `0x100`:
     * Location `0x100`: `0x78` (LSB)
     * Location `0x101`: `0x56`
     * Location `0x102`: **`0x34`**
     * Location `0x103`: `0x12` (MSB)
  3. Therefore, memory location `0x102` contains **`0x34`**.

---

### 🔹 Question 6.3 [GATE CSE / ISRO CS — Pointer Typecasting Trap]
**Q:** Consider the following C snippet executed on a 32-bit **Little-Endian** machine where `sizeof(int) = 4` and `sizeof(short) = 2`:

```c
int a = 0xA1B2C3D4;
short *p = (short *)&a;
printf("%X", *(p + 1));
```

What is the printed hexadecimal output?
- **(A)** `C3D4`
- **(B)** `A1B2`
- **(C)** `B2C3`
- **(D)** `D4C3`

**Solution:**
* **Correct Answer:** **(B) `A1B2`**
* **Explanation:**
  1. In Little-Endian format, `int a = 0xA1B2C3D4` is stored across 4 bytes starting at address $A$:
     * Address $A + 0$: `0xD4`
     * Address $A + 1$: `0xC3`
     * Address $A + 2$: `0xB2`
     * Address $A + 3$: `0xA1`
  2. The pointer `p` is of type `short*` ($2\text{ bytes}$).
     * `p` points to address $A$.
     * `*p` reads 2 bytes starting at $A$ (locations $A+0$ and $A+1$).  
       In Little-Endian, LSB is at $A$ (`D4`) and MSB is at $A+1$ (`C3`), so `*p = 0xC3D4`.
  3. Pointer arithmetic `p + 1` increments by `sizeof(short) = 2 bytes`:
     * `p + 1` points to address $A + 2$.
     * `*(p + 1)` reads 2 bytes starting at address $A + 2$ (locations $A+2$ and $A+3$).
     * At $A + 2$: `0xB2` (lower byte / LSB of this 16-bit word).
     * At $A + 3$: `0xA1` (upper byte / MSB of this 16-bit word).
     * Reassembling into a 16-bit value: **`0xA1B2`**.

---

### 🔹 Question 6.4 [Conceptual GATE Trap: Strings vs. Integers]
**Q:** Does the byte-ordering convention (Big-Endian vs Little-Endian) alter the order in which characters of a string (e.g., `char s[] = "GATE"`) are stored in consecutive memory addresses?

**Solution:**
* **Answer:** **NO.**
* **Explanation:**
  * Endianness applies exclusively to **multi-byte atomic numeric data types** (such as 16-bit, 32-bit, or 64-bit integers and floating-point numbers) where a single scalar value spans multiple bytes.
  * A character string is an **array of 1-byte elements** (`char`).
  * In C and computer architecture, array elements are strictly stored in increasing order of index:
    * `s[0] = 'G'` is ALWAYS stored at the base address $A$.
    * `s[1] = 'A'` is ALWAYS stored at address $A + 1$.
    * `s[2] = 'T'` is ALWAYS stored at address $A + 2$.
    * `s[3] = 'E'` is ALWAYS stored at address $A + 3$.
  * Therefore, string layout in memory is completely identical on both Big-Endian and Little-Endian machines!

---

## 💡 Quick Recall Revision Table

| Parameter | Symbol / Formula | Hardware / Concept Unit |
| :--- | :--- | :--- |
| **Number of Locations** | $N = 2^n$ | Memory Chip Height |
| **Number of Address Lines** | $n = \lceil \log_2 N \rceil$ | Address Bus, MAR |
| **Number of Data Lines** | $m =$ bits per cell | Data Bus, MDR |
| **Total Memory Bits** | $2^n \times m$ | Total Transistors / Cells |
| **Byte Addressable Capacity** | $2^n \times 1\text{ Byte}$ | Total Address Space |
| **Hex Ending Address** | $(2^n - 1)_{16}$ | Top of Address Space |
| **Range Span** | $\text{End} - \text{Start} + 1$ | Block Capacity |
| **Little-Endian Rule** | LSB $\longleftrightarrow$ Lowest Address | x86, AMD64, ARM |
| **Big-Endian Rule** | MSB $\longleftrightarrow$ Lowest Address | TCP/IP Networks, Mainframes |
| **String Array Order** | `str[0]` always at Lowest Address | Independent of Endianness |

