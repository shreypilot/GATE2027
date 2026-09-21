# Operating Systems — Complete GATE Syllabus & Overview

**Typical GATE Weightage:** ~8 – 10 Marks

---

## 📌 Core Modules & Key High-Yield Topics

### Module 1: Processes, Threads & CPU Scheduling
* **Process Management:** Process Control Block (PCB), States, Context Switching.
* **Threads:** User-level vs. Kernel-level threads, Multithreading models.
* **CPU Scheduling Algorithms:**
  * FCFS (First-Come First-Served), Convoy effect
  * SJF (Shortest Job First) & SRTF (Shortest Remaining Time First)
  * Round Robin (Time quantum selection & responsiveness)
  * Priority Scheduling (Preemptive & Non-preemptive, Aging)
  * Multilevel Queue and Feedback Queue Scheduling
  * Metrics: Turnaround Time, Waiting Time, Response Time, Throughput

### Module 2: Process Synchronization & Concurrency
* **Critical Section Problem:** Mutual Exclusion, Progress, Bounded Waiting.
* **Software Solutions:** Peterson's Algorithm, Dekker's Algorithm.
* **Hardware Solutions:** Test-and-Set, Swap / Compare-and-Swap instructions.
* **Semaphores & Mutex:** Counting vs. Binary semaphores, Wait/Signal primitives.
* **Classical IPC Problems:**
  * Producer-Consumer (Bounded-Buffer) Problem
  * Readers-Writers Problem
  * Dining Philosophers Problem

### Module 3: Deadlocks
* **Four Necessary Conditions:** Mutual Exclusion, Hold & Wait, No Preemption, Circular Wait.
* **Resource Allocation Graphs (RAG):** Cycles and deadlock identification.
* **Handling Strategies:**
  * Deadlock Prevention (Eliminating one of the four conditions)
  * Deadlock Avoidance: Banker's Safety Algorithm, Resource-Request Algorithm
  * Deadlock Detection & Recovery (Wait-for graphs, Process termination, Resource preemption)

### Module 4: Memory Management & Virtual Memory
* **Contiguous Allocation:** Fixed vs Dynamic Partitioning, First Fit, Best Fit, Worst Fit, Internal & External Fragmentation.
* **Non-Contiguous Allocation (Paging):**
  * Page Table structure, Frame size = Page size
  * Multi-level paging address translation
  * Inverted Page Tables
* **Virtual Memory & Demand Paging:**
  * Page Fault handling
  * Page Replacement Algorithms: FIFO, Optimal (Belady's Anomaly), LRU, Clock / Second-Chance
  * Thrashing and Working Set Model

### Module 5: Storage & File Systems
* **Disk Scheduling:** FCFS, SSTF, SCAN (Elevator), C-SCAN, LOOK, C-LOOK.
* **File Allocation Methods:** Contiguous, Linked list, Indexed (UNIX Inode multi-level index calculations).
