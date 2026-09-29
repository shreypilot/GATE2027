# Computer Organization & Architecture (COA)
## Lecture 11: Disk Memory, Hard Disk Architecture & I/O Interface

**Source:** GfG GATE CS Crash Course (Vijay Sir) — COA Lecture 11: Disk & I/O Interface

---

## 📌 Topic Priority Matrix (GATE CSE)

| Priority | Topic | Key Focus Area |
| :--- | :--- | :--- |
| ★★★★★ | **Hard Disk Access Time Math** | $\text{Access Time} = \text{Seek Time} + \text{Rotational Latency} + \text{Transfer Time} + \text{Controller Overhead}$ |
| ★★★★★ | **Rotational Latency** | Average Rotational Latency $= \frac{1}{2} \times \text{Time for 1 full rotation}$ |
| ★★★★★ | **Transfer Rate Calculation** | $\text{Transfer Rate} = \text{Tracks Capacity} \times \text{Rotational Speed}$ |
| ★★★★☆ | **I/O Data Transfer Modes** | Programmed I/O vs Interrupt-driven I/O vs DMA |
| ★★★★☆ | **DMA Cycle Stealing vs Burst Mode** | Bus grant, cycle stealing, block transfer |
| ★★★☆☆ | **Disk Capacity Sizing** | $\text{Capacity} = \text{Platters} \times \text{Surfaces/Platter} \times \text{Tracks/Surface} \times \text{Sectors/Track} \times \text{Sector Size}$ |

---

## 1. Hard Disk Structure & Geometry

A magnetic hard disk consists of a stack of rotating **platters** coated with magnetic material, read/write heads mounted on an actuator arm assembly, and a central spindle motor.

```text
       +------------------------------------+
       |          Spindle Motor             |
       +-----------------+------------------+
                         |
       ==================|==================  Platter 1 (Surface 0 & Surface 1)
           [R/W Head]    |    [R/W Head]
       ==================|==================  Platter 2 (Surface 2 & Surface 3)
           [R/W Head]    |    [R/W Head]
                         |
                 Actuator Arm
```

### Key Parameters:
* **Platters:** Individual physical disks mounted on the spindle.
* **Surfaces:** Each platter has 1 or 2 magnetic recording surfaces ($N_{\text{surfaces}}$).
* **Tracks:** Concentric circular rings on a surface ($N_{\text{tracks}}$).
* **Cylinder:** The collection of identical track tracks across all recording surfaces at a given arm position.
* **Sectors:** Each track is divided into fixed-size arcs called sectors ($N_{\text{sectors}}$), typically $512\text{ Bytes}$ or $4\text{ KB}$.

$$\text{Total Disk Capacity} = N_{\text{surfaces}} \times N_{\text{tracks/surface}} \times N_{\text{sectors/track}} \times \text{Sector Size}$$

---

## 2. Hard Disk Access Time Components

The total time required to read or write a block of data on disk is:

$$\mathbf{\text{Total Access Time} = \text{Seek Time} + \text{Rotational Latency} + \text{Transfer Time} + \text{Controller Overhead}}$$

### 2.1 Seek Time ($T_{\text{seek}}$)
The time required for the read/write head assembly to move position radially over the target track/cylinder.
* Usually specified directly in numericals (or computed from average tracks crossed $\times$ time per track).

### 2.2 Rotational Latency ($T_{\text{rot}}$)
The time required for the target sector to rotate underneath the read/write head once the head is on the track.
* **Maximum Rotational Latency:** Time taken for 1 complete 360° rotation ($T_{\text{rot\_max}}$).
  $$T_{\text{rot\_max}} = \frac{60}{\text{RPM}}\text{ seconds}$$
* **Average Rotational Latency ($T_{\text{rot\_avg}}$):** Assumed to be half of one full rotation:
  $$\mathbf{T_{\text{rot\_avg}} = \frac{1}{2} \times T_{\text{rot\_max}} = \frac{1}{2} \times \left(\frac{60}{\text{RPM}}\right)}$$

### 2.3 Transfer Time ($T_{\text{trans}}$)
The time taken to actually transfer the data bytes under the head.
$$\text{Transfer Time} = \frac{\text{Data Size to Transfer}}{\text{Data Transfer Rate (DTR)}}$$

#### Data Transfer Rate (DTR) Formula:
$$\mathbf{\text{DTR} = \text{Capacity of 1 Track} \times \text{Rotational Frequency } (RPS)}$$

---

## 3. Input/Output (I/O) Transfer Modes

```text
                            +--------------------+
                            |  I/O Data Transfer |
                            +---------+----------+
                                      |
         ┌────────────────────────────┼────────────────────────────┐
         v                            v                            v
+------------------+         +------------------+         +------------------+
|  Programmed I/O  |         | Interrupt-Driven |         | Direct Memory    |
| (Polling / Busy) |         |      I/O         |         |  Access (DMA)    |
+------------------+         +------------------+         +------------------+
```

| Metric | Programmed I/O | Interrupt-Driven I/O | Direct Memory Access (DMA) |
| :--- | :--- | :--- | :--- |
| **CPU Busy Waiting?** | Yes (Busy Polling loop) | No (CPU does other work until IRQ) | No (CPU unblocked during block transfer) |
| **Data Path** | Device $\to$ CPU $\to$ Memory | Device $\to$ CPU $\to$ Memory | Device $\to$ Direct to Memory (via Bus) |
| **Transfer Unit** | 1 Word at a time | 1 Word at a time | Entire Block of Data |
| **Hardware Required** | Minimal | Interrupt Controller | DMA Controller (DMAC) |
| **Best For** | Very slow devices / simple systems | Low-rate bursty devices (keyboard, mouse) | High-speed devices (Disks, NICs) |

---

## 4. Direct Memory Access (DMA) Modes

The **DMA Controller (DMAC)** takes over the system bus from the CPU to perform high-speed memory transfers directly between I/O interfaces and Main Memory.

### 4.1 DMA Transfer Modes
1. **Burst Mode (Block Mode):**
   * DMAC requests bus mastership, transfers the ENTIRE data block without interruption, then releases the bus back to the CPU.
   * *Advantage:* Maximum transfer throughput.
   * *Drawback:* CPU is locked out of memory for long durations.

2. **Cycle Stealing Mode:**
   * DMAC acquires the bus for ONE bus cycle to transfer ONE word, then immediately releases the bus back to the CPU.
   * *Advantage:* CPU is not locked out of memory for long intervals.
   * *Drawback:* Slightly higher bus arbitration overhead.

3. **Transparent Mode (Interleaved DMA):**
   * DMAC transfers data ONLY during CPU cycles when the CPU is not utilizing the system bus (e.g., during internal decoding or ALU operation).
   * *Advantage:* Zero CPU slowdown.

---

## 5. GATE Numerical Formulas & Quick Reference

1. **Rotational Speed Conversion:** $R \text{ RPM} \implies \frac{R}{60} \text{ Rotations/sec (RPS)}$.
2. **Time per Rotation:** $T_{\text{rot\_max}} = \frac{60}{R} \text{ seconds}$.
3. **Average Rotational Latency:** $T_{\text{rot\_avg}} = \frac{30}{R} \text{ seconds}$.
4. **Track Capacity:** $\text{Sectors per Track} \times \text{Sector Size}$.
5. **Transfer Rate:** $\text{Track Capacity} \times \text{RPS}$.
6. **DMA CPU Fraction Blocked:**
   $$\text{CPU Stalled Fraction} = \frac{\text{Time DMAC uses bus}}{\text{Total Time}}$$
