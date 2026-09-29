# Computer Organization & Architecture (COA)
## Lecture 09–10: Cache Memory Organization & Mapping Techniques

**Source:** GfG GATE CS Crash Course (Vijay Sir) — COA Lecture 9 & 10: Cache Memory & Mapping

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **Cache Hit Ratio & Average Memory Access Time (AMAT)** | Hierarchical vs Simultaneous access formulas |
| ★★★★★ | **Direct Mapping** | Address breakdown: Tag + Line Offset + Word Offset |
| ★★★★★ | **Associative Mapping** | Address breakdown: Tag + Word Offset; Comparator count |
| ★★★★★ | **Set-Associative Mapping** | Address breakdown: Tag + Set Offset + Word Offset |
| ★★★★☆ | **Cache Replacement Policies** | LRU, FIFO, Optimal, Bit implementation |
| ★★★★☆ | **Cache Write Policies** | Write-Through + Allocate vs Write-Back + Dirty Bit |
| ★★★☆☆ | **Cache Memory Sizing Numericals** | Tag directory size calculation ($N_{\text{lines}} \times (\text{Tag bits} + \text{Dirty} + \text{Valid})$) |

---

## 1. Cache Memory Fundamentals & Memory Hierarchy

Cache memory is a small, fast memory placed between the CPU and Main Memory (RAM) to reduce the average time to access memory by exploiting the **Locality of Reference**.

```text
+---------+         +----------+         +-------------+         +---------------+
|   CPU   | <-----> | L1 Cache | <-----> | Main Memory | <-----> | Secondary Disk|
| Registers|        | (SRAM)   |         | (DRAM)      |         | (SSD / HDD)   |
+---------+         +----------+         +-------------+         +---------------+
 Fast, Small                                                          Slow, Large
```

### 1.1 Locality of Reference
1. **Temporal Locality:** If a memory location is referenced once, it is likely to be referenced again in the near future (e.g., loops, variables).
2. **Spatial Locality:** If a memory location is referenced, nearby memory locations are likely to be referenced soon (e.g., sequential array accesses, instruction sequences).

---

## 2. Average Memory Access Time (AMAT) Formulas

Let:
* $H_1 = \text{Cache hit ratio}$ ($0 \le H_1 \le 1$), $(1 - H_1) = \text{Cache miss ratio}$
* $T_1 = \text{Cache access time}$
* $T_2 = \text{Main memory access time}$

### 2.1 Hierarchical Access Model (CPU checks Cache first, then Main Memory on miss)
$$\text{AMAT} = T_1 + (1 - H_1) \cdot T_2$$

### 2.2 Simultaneous Access Model (CPU accesses Cache and Main Memory in parallel)
$$\text{AMAT} = H_1 \cdot T_1 + (1 - H_1) \cdot T_2$$

---

## 3. Cache Mapping Techniques

Main memory is divided into **Blocks** (or Lines) of size $B$ bytes.  
Cache memory is divided into **Lines** (or Frames) of size $B$ bytes.

Let:
* Main Memory Capacity $= 2^n \text{ Bytes} \implies n\text{-bit Main Memory Address}$.
* Cache Capacity $= C \text{ Bytes}$.
* Block / Line Size $= B = 2^w \text{ Bytes} \implies w\text{ bits for Word/Byte Offset}$.
* Total Main Memory Blocks $= M = \frac{2^n}{B} = 2^{n-w}$.
* Total Cache Lines $= N = \frac{C}{B} = 2^L \implies L\text{ bits for Line Offset}$.

---

### 3.1 Direct Mapping

Each main memory block maps to **exactly ONE fixed cache line**:
$$\text{Cache Line Number} = (\text{Main Memory Block Number}) \bmod N$$

#### Address Split (Main Memory Address = $n$ bits):
```text
+------------------------+-----------------------+------------------------+
|       Tag Field        |   Line Offset Field   |   Word Offset Field    |
+------------------------+-----------------------+------------------------+
   n - L - w bits                L bits                  w bits
```

* **Line Offset Bits ($L$):** $L = \log_2(\text{Number of Cache Lines } N)$.
* **Word Offset Bits ($w$):** $w = \log_2(\text{Block Size } B)$.
* **Tag Bits:** $n - L - w$.
* **Hardware Needed:** $1$ Comparator of size $\text{Tag Bits}$.
* **Conflict Misses:** High (thrashing occurs when multiple active blocks map to the same line).

---

### 3.2 Fully Associative Mapping

A main memory block can be placed in **ANY available cache line**.

#### Address Split (Main Memory Address = $n$ bits):
```text
+------------------------------------------------+------------------------+
|                   Tag Field                    |   Word Offset Field    |
+------------------------------------------------+------------------------+
                   n - w bits                             w bits
```

* **Tag Bits:** $n - w$.
* **Line Offset Bits:** $0$ (no fixed line requirement).
* **Hardware Needed:** $N$ Comparators operating in parallel (expensive hardware).
* **Conflict Misses:** Zero (conflict misses eliminated).

---

### 3.3 $K$-Way Set Associative Mapping

Cache lines are grouped into **Sets**, where each set contains $K$ lines.  
A main memory block maps to a **specific set**, but can be placed in **ANY of the $K$ lines** within that set:
$$\text{Set Number} = (\text{Main Memory Block Number}) \bmod (\text{Number of Sets } S)$$

Let:
* Total Sets $S = \frac{\text{Total Cache Lines } N}{K} = 2^s \implies s\text{ bits for Set Offset}$.

#### Address Split (Main Memory Address = $n$ bits):
```text
+------------------------+-----------------------+------------------------+
|       Tag Field        |    Set Offset Field   |   Word Offset Field    |
+------------------------+-----------------------+------------------------+
   n - s - w bits                s bits                  w bits
```

* **Set Offset Bits ($s$):** $s = \log_2 S = \log_2(N / K)$.
* **Word Offset Bits ($w$):** $w = \log_2 B$.
* **Tag Bits:** $n - s - w$.
* **Hardware Needed:** $K$ Comparators of size $\text{Tag Bits}$.

---

## 4. Cache Replacement & Write Policies

### 4.1 Replacement Policies (Used when Cache Set is Full)
1. **LRU (Least Recently Used):** Replaces the block that has not been accessed for the longest time. (Optimal hardware implementation uses $K(K-1)/2$ bits per set).
2. **FIFO (First In First Out):** Replaces the block that entered the set earliest.
3. **Random:** Selects a random block to replace.

### 4.2 Write Policies
* **Write-Through:** Updates both Cache and Main Memory on every write operation. (Simple, keeps memory consistent; requires Write Buffer to avoid stalls).
* **Write-Back:** Updates ONLY Cache on write; sets a **Dirty Bit (Modified Bit)**. Main memory is updated only when the dirty block is evicted from cache. (Fast, saves memory bandwidth).
* **Write Allocate (used with Write-Back):** On write miss, loads the block from main memory into cache then writes.
* **No-Write Allocate (used with Write-Through):** On write miss, updates main memory directly without loading block into cache.

---

## 5. Summary of Mapping Formulas

| Feature | Direct Mapping | $K$-Way Set Associative | Fully Associative |
| :--- | :--- | :--- | :--- |
| **Set Count ($S$)** | $N$ (Number of lines) | $N / K$ | $1$ |
| **Tag Bits** | $n - \log_2 N - \log_2 B$ | $n - \log_2(N/K) - \log_2 B$ | $n - \log_2 B$ |
| **Comparators Required** | $1$ | $K$ | $N$ |
| **Hardware Complexity** | Lowest | Medium | Highest |
| **Conflict Misses** | Highest | Low | Zero |
