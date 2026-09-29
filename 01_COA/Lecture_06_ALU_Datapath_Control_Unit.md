# Computer Organization & Architecture (COA)
## Lecture 06: ALU Datapath, Micro-operations & Control Unit

**Source:** GfG GATE CS Crash Course (Vijay Sir) — ALU Data Path & Control Unit class screenshots

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **Fetch / Execute / Interrupt RTL** | `PC→MAR→Mem→MBR→IR` and variants |
| ★★★★★ | **Hardwired control equations** | $A_{in}, B_{out}$ as $\sum$ (instruction $\times$ timing) |
| ★★★★★ | **Microprogrammed CU** | Control memory size, **CAR** width |
| ★★★★★ | **Horizontal vs Vertical μ-programming** | Decoder, parallelism, CISC default |
| ★★★★☆ | **Single-bus add sequence** | Temp registers + ALU + AC |
| ★★★☆☆ | **Micro-operation definition** | Atomic register-transfer steps |

---

## 1. Control Unit — Role

The **Control Unit (CU)** is the supervisor: it issues the **sequence of control signals** that make the datapath execute each **micro-operation**.

* A **micro-operation** is an **atomic** hardware step (register transfer) such as $PC \rightarrow MAR$.
* A machine instruction = **instruction cycle** = several **sub-cycles** (fetch, indirect, execute, interrupt), each made of one or more micro-operations.
* Control signals are applied to **base hardware** (gates, buses, ALU) to produce the desired transfer.

Buses on the datapath:
* **AL** = Address lines (from MAR)
* **DL** = Data lines (to/from MBR)
* **CL** = Control lines (`RD`, `WR`, …)

---

## 2. Fetch Cycle (Hardware Design)

Purpose: move the instruction at **PC** into **IR**.

```text
T1:  PC  → MAR          (instruction address)
T2:  Memory[MAR] → MBR  (assert RD on CL; data on DL)
T3:  MBR → IR
```

Slide path:

```text
PC --T1--> MAR --AL, RD--> Memory --DL--> MBR --T3--> IR
                \______________ T2 ______________/
```

**Canonical chain (memorize):**

$$\mathbf{PC \rightarrow MAR \rightarrow Memory \rightarrow MBR \rightarrow IR}$$

Simultaneously, $PC$ is incremented so it points to the **next** instruction.

CPU block (class diagram): ALU circuit + registers $R_1\ldots R_n$, **AC**, **PC**, **MAR**, **MBR**, **IR**, control circuit; memory holds instructions & data; I/O sits beside memory.

---

## 3. Execute Cycle — Direct Addressing

Operand address is already in the **address field of IR**.

```text
T1:  IR[AF] → MAR      (EA)
T2:  M[MAR] → MBR      (RD)
T3:  MBR → AC / ALU
```

RTL as written:

$$\text{IR(AF)} \rightarrow MAR \rightarrow Mem \rightarrow MBR \rightarrow ALU \mid AC_{in}$$

Micro-ops with control names:

```text
T1:  IR_out, MAR_in
T2:  MAR_out, MBR_in, RD
T3:  MBR_out, AC/ALU_in
```

---

## 4. Interrupt Cycle

Serviced **after the current instruction finishes** (not in the middle of execute, in the basic model).

1. Push **PC** (already the **return / next-instruction** address) onto the stack at **TOS**.
2. Load **PC** with the **ISR** (interrupt service routine / vector) address.

RTL sketch:

$$\text{PC} \rightarrow \text{MBR},\quad SP[\text{TOS}] \rightarrow \text{MAR},\quad \text{MBR} \rightarrow M[\text{MAR}]$$

$$\text{ISR} \rightarrow \text{PC}$$

```text
        PC value
           ↓  push
     +-----------+
TOS  | return @  |
     +-----------+  STACK
```

---

## 5. ALU Datapath Example: $R_0 \leftarrow R_1 + R_2$

On a **single shared ALU / bus**, addition needs **temporary** input registers:

```text
T1:  Temp1 ← R1     (R1_out, Temp1_in)
T2:  Temp2 ← R2     (R2_out, Temp2_in)
T3:  AC ← Temp1 + Temp2   (Temp1_out, Temp2_out, ALU ADD, AC_in)
```

```text
  Input1          Input2
    |               |
  Temp1           Temp2
    \               /
           ALU
            |
            AC
```

---

## 6. Datapath + Control Signals (conceptual)

Typical single-bus CPU: **Memory** $\leftrightarrow$ **MBR** $\leftrightarrow$ bus; **MAR** to memory address; **PC**, **IR**, **AC**, **ALU**; **CU** driven by **IR** opcode + **flags** + **clock**. Each arrow is gated by a control signal $C_i$ (`PC_out`, `MAR_in`, `MBR_in`, `IR_in`, `AC_in`, ALU function, …).

---

## 7. Hardwired Control Unit

Combinational (or clocked combinational) logic computes each control signal from:

* **Decoded instruction** $I_1,I_2,\ldots$
* **Timing / T-states** $T_1,T_2,\ldots$
* Flags (optional)

### GATE-style table (3 registers A,B,C; 3 instructions)

|  | $I_1$ | $I_2$ | $I_3$ |
| :---: | :--- | :--- | :--- |
| $T_1$ | $A_{in}, B_{out}$ | $A_{in}, C_{in}, B_{out}$ | $B_{in}, B_{out}$ |
| $T_2$ | $B_{in}, C_{in}, A_{out}$ | $A_{in}, A_{out}$ | $A_{in}, B_{in}, C_{out}$ |
| $T_3$ | $B_{in}, B_{out}$ | $B_{in}, B_{out}$ | $B_{in}, B_{out}$ |
| $T_4$ | $C_{in}, A_{out}$ | $B_{in}, A_{out}$ | $A_{in}, A_{out}$ |
| $T_5$ | End | End | End |

A signal is **ON** in every cell where it is listed. Boolean form = **OR of all (instruction $\land$ time) pairs**.

$$
\begin{aligned}
A_{in} &= I_1 T_1 + I_2(T_1+T_2) + I_3(T_2+T_4)\\
B_{out} &= (I_1+I_2+I_3)(T_1+T_3)
\end{aligned}
$$

(Verify by scanning the table; this is the standard GATE method.)

**Hardwired CU:** fast, **inflexible** (changing ISA means rewiring), used in many **RISC** designs.

---

## 8. Microprogrammed Control Unit

Control words live in **Control Memory (ROM)**. Opcode selects a starting address; **CAR** (Control Address Register) and **CDR/CDB** (Control Data / Buffer) walk through μ-instructions. Each control word generates the signals for one micro-operation (or a packed group).

### Size of control memory & CAR

If there are $140$ machine instructions and each needs $7$ μ-operations (control words):

$$\text{Control Memory} = 140 \times 7 = 980 \text{ CW}$$

$$\text{CAR} = \lceil \log_2 980 \rceil = 10 \text{ bits}$$

---

## 9. Horizontal vs Vertical Microprogramming

| | **Horizontal** | **Vertical** |
| :--- | :--- | :--- |
| Control-word width | **Wide** (often 1 bit per signal) | **Narrow** (encoded fields) |
| External decoder | **Not needed** (bits drive signals directly) | **Needed** to expand fields into signals |
| Parallelism | **High** (many signals in one CW: none / more than one) | **Low** (typically none / one resource class) |
| Flexibility vs hardwired | More flexible than hardwired | **More flexible** than horizontal (easier to add μ-routines) |
| Memory size | Large ROM (wide words) | Smaller ROM (encoded) |
| Speed | Faster (no decode) | Slower (decode delay) |

**Class note:** default **vertical** μ-programmed CU is associated with **CISC**.

---

## 10. GATE Formula Sheet

1. Fetch: $PC \rightarrow MAR \rightarrow Mem \rightarrow MBR \rightarrow IR$
2. Direct execute: $IR[AF] \rightarrow MAR \rightarrow Mem \rightarrow MBR \rightarrow AC$
3. Interrupt after **complete** current instruction; save **next PC**
4. $\#CW = (\#\text{instructions})\times(\mu\text{-ops per instruction})$
5. $\text{CAR bits} = \lceil \log_2(\#CW) \rceil$
6. Hardwired signal = $\sum I_j T_k$ over table cells where the signal is listed
7. Horizontal = wide, no decoder, high parallelism; Vertical = encoded, decoder, CISC
