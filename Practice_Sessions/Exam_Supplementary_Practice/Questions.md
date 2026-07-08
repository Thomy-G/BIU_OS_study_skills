# Exam 2: Supplementary Practice Guide (Asymmetric Architecture Focus)

*Compiled from the core multi-year structural exam repositories across Semester B options.*

---

## Part 1: Architecture Math & Systems Calculations

### Question 1: Asymmetric Index Table Address Sizing (10 Points)

An asymmetric inode-based Unix filesystem uses physical storage data blocks of size **8 KB**. Address pointers stored inside these structural indexing tables consume **8 bytes** per slot. An individual file's mapping inode contains the following allocation pointers:

* 10 Direct Allocation Pointers
* 1 Single Indirect Pointer Table
* 1 Double Indirect Pointer Table
* 1 Triple Indirect Pointer Table

**Task:** Calculate the absolute **Maximum addressable File Size** (express your answer clearly in bytes, Megabytes, or Gigabytes).

---

### Question 2: Memory Space Sub-Division (10 Points)

A structural computer system implements a conventional **Two-Level Paging Architecture** running across a standard 32-bit linear virtual memory space. The designated allocation page size is configured to be **8 KB**.
The Outer Page Directory indexing field is designed to use exactly **10 bits** of address space.

* The system hardware TLB lookup overhead latency is **10 ns**.
* The primary physical main memory access latency is **100 ns**.

#### Tasks:

1. **Address Bit Partitioning:** Map out the exact allocation of address space bits: how many bits are assigned to the Outer Page Index, how many to the Inner Page Index, and how many map out the localized Page Offset?
2. **Effective Access Time ($EAT$ Optimization):** If the computer system must be optimized to ensure its calculated Effective Access Time ($EAT$) remains **at most 125 ns**, what is the absolute minimum acceptable hardware **TLB Hit Ratio ($\alpha$)** required to sustain this constraint? *(Assume all missing page directory table lookups must occur via conventional memory cycles).*

---

### Question 3: Disk I/O Track Operations (10 Points)

A hard disk layout consists of tracking addresses starting from 0 extending up to index 299. The mechanical read/write head assembly is currently positioned at track address **120** and was tracked as moving upwards towards higher numeric tracks. The current kernel queue contains pending I/O read/write track operations at:

$$\text{Queue: } [45, 250, 15, 185, 290, 80]$$

**Task:** Calculate the complete total disk seek distance (measured explicitly in total tracks traversed) when utilizing the **C-SCAN (Circular SCAN)** scheduling algorithm. *(Assume that jumping back from the absolute outer track edge 299 back to baseline track 0 is handled via custom high-speed resets that add exactly zero cost to the seek track distance calculation).*

---

## Part 4: Virtual Memory Management & Frame Replacements

### Question: Page Reference Strings & Anomalies (20 Points)

Consider the following incoming chronological memory page request reference sequence:

$$\text{Reference String: } 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5$$

#### Tasks:

1. **FIFO Allocation Evaluation (12 Points):** Explicitly trace out every frame transition map and calculate the precise number of triggered **Page Faults** when implementing a conventional **First-In, First-Out (FIFO)** replacement policy using:
   * **3 Physical Frames** allocated to the program workspace.
   * **4 Physical Frames** allocated to the program workspace.
   * *Analysis Question:* Does this specific sequence exhibit **Belady's Anomaly**? Define the anomaly and explain your finding based on your numerical results.
2. **LRU Allocation Mapping (8 Points):** Trace the memory frame allocation map using a **Least Recently Used (LRU)** replacement policy operating over **3 Physical Frames**. Compare your final page fault metrics directly against your 3-frame FIFO calculation results.

---