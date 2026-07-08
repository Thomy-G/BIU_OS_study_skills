# Exam 2: Supplementary Practice Guide Solutions

## Part 1: Architecture Math & Systems Calculations

### Solution 1: Asymmetric Index Table Address Sizing
* **Parameters**:
  * Block size = $8\text{ KB} = 8192\text{ bytes}$
  * Pointer size = $8\text{ bytes}$
  * Pointers per block = $\frac{8192}{8} = 1024\text{ pointers}$
* **Capacity Sizing**:
  * Direct pointers: $10 \times 8\text{ KB} = 80\text{ KB}$
  * Single Indirect: $1024 \times 8\text{ KB} = 8192\text{ KB} = 8\text{ MB}$
  * Double Indirect: $1024^2 \times 8\text{ KB} = 1,048,576 \times 8\text{ KB} = 8192\text{ MB} = 8\text{ GB}$
  * Triple Indirect: $1024^3 \times 8\text{ KB} = 1,073,741,824 \times 8\text{ KB} = 8,589,934,592\text{ KB} = 8192\text{ GB} = 8\text{ TB}$
* **Maximum File Size**:
  * Total blocks = $10 + 1024 + 1024^2 + 1024^3 = 1,074,791,434\text{ blocks}$
  * Max size = $1,074,791,434 \times 8192\text{ bytes} = \mathbf{8,804,692,426,752\text{ bytes}} \approx \mathbf{8.008\text{ TB}}$

---

### Solution 2: Memory Space Sub-Division
1. **Address Bit Partitioning**:
   * Page size = $8\text{ KB} = 2^{13}\text{ bytes} \rightarrow$ **Offset = 13 bits**.
   * Outer Page Directory index = **10 bits** (given).
   * Inner Page Table index = $32 - 13 - 10 = \mathbf{9\text{ bits}}$.
2. **EAT Optimization**:
   * TLB Hit Time = $10\text{ ns} + 100\text{ ns} = 110\text{ ns}$ (TLB lookup + 1 physical memory read).
   * TLB Miss Time = $10\text{ ns} + 3 \times 100\text{ ns} = 310\text{ ns}$ (TLB lookup + 2 page directory lookups + 1 physical memory read).
   * $\text{EAT} = \alpha(110) + (1-\alpha)(310) = 310 - 200\alpha$
   * Set $\text{EAT} \le 125\text{ ns}$:
     $$310 - 200\alpha \le 125 \rightarrow 200\alpha \ge 185 \rightarrow \alpha \ge \frac{185}{200} = \mathbf{92.5\%}$$

---

### Solution 3: Disk I/O Track Operations
* **Parameters**: Current position = 120, moving upwards.
* Sorted Queue: $[15, 45, 80, 185, 250, 290]$
* **C-SCAN path**:
  $$120 \rightarrow 185 \rightarrow 250 \rightarrow 290 \rightarrow 299 \rightarrow 0 \text{ (wrap)} \rightarrow 15 \rightarrow 45 \rightarrow 80$$
* **Seek Calculations**:
  * Step 1 (Upward): $299 - 120 = 179\text{ cylinders}$
  * Step 2 (Wrap): $0\text{ cylinders}$ (per specification)
  * Step 3 (Post-Wrap): $80 - 0 = 80\text{ cylinders}$
  * **Total Seek Distance** = $179 + 80 = \mathbf{259\text{ cylinders}}$.

---

## Part 4: Virtual Memory Management & Frame Replacements

### Solution: Page Reference Strings & Anomalies
1. **FIFO Evaluation**:
   * **3 Frames FIFO**:
     * $1 \rightarrow [1]$ (Fault)
     * $2 \rightarrow [1, 2]$ (Fault)
     * $3 \rightarrow [1, 2, 3]$ (Fault)
     * $4 \rightarrow [2, 3, 4]$ (Fault, replaces 1)
     * $1 \rightarrow [3, 4, 1]$ (Fault, replaces 2)
     * $2 \rightarrow [4, 1, 2]$ (Fault, replaces 3)
     * $5 \rightarrow [1, 2, 5]$ (Fault, replaces 4)
     * $1 \rightarrow [1, 2, 5]$ (Hit)
     * $2 \rightarrow [1, 2, 5]$ (Hit)
     * $3 \rightarrow [2, 5, 3]$ (Fault, replaces 1)
     * $4 \rightarrow [5, 3, 4]$ (Fault, replaces 2)
     * $5 \rightarrow [5, 3, 4]$ (Hit)
     * **Total Faults = 9**.
   * **4 Frames FIFO**:
     * $1 \rightarrow [1]$ (Fault)
     * $2 \rightarrow [1, 2]$ (Fault)
     * $3 \rightarrow [1, 2, 3]$ (Fault)
     * $4 \rightarrow [1, 2, 3, 4]$ (Fault)
     * $1 \rightarrow [1, 2, 3, 4]$ (Hit)
     * $2 \rightarrow [1, 2, 3, 4]$ (Hit)
     * $5 \rightarrow [2, 3, 4, 5]$ (Fault, replaces 1)
     * $1 \rightarrow [3, 4, 5, 1]$ (Fault, replaces 2)
     * $2 \rightarrow [4, 5, 1, 2]$ (Fault, replaces 3)
     * $3 \rightarrow [5, 1, 2, 3]$ (Fault, replaces 4)
     * $4 \rightarrow [1, 2, 3, 4]$ (Fault, replaces 5)
     * $5 \rightarrow [2, 3, 4, 5]$ (Fault, replaces 1)
     * **Total Faults = 10**.
   * **Belady's Anomaly Analysis**: **Yes.** The fault count increased from 9 to 10 when the page frame count increased from 3 to 4. This is a classic demonstration of Belady's Anomaly under the FIFO page replacement policy.
2. **LRU 3-Frame Evaluation**:
   * Trace:
     * $1 \rightarrow [1]$ (Fault)
     * $2 \rightarrow [1, 2]$ (Fault)
     * $3 \rightarrow [1, 2, 3]$ (Fault)
     * $4 \rightarrow [2, 3, 4]$ (Fault, replaces 1)
     * $1 \rightarrow [3, 4, 1]$ (Fault, replaces 2)
     * $2 \rightarrow [4, 1, 2]$ (Fault, replaces 3)
     * $5 \rightarrow [1, 2, 5]$ (Fault, replaces 4)
     * $1 \rightarrow [2, 5, 1]$ (Hit)
     * $2 \rightarrow [5, 1, 2]$ (Hit)
     * $3 \rightarrow [1, 2, 3]$ (Fault, replaces 5)
     * $4 \rightarrow [2, 3, 4]$ (Fault, replaces 1)
     * $5 \rightarrow [3, 4, 5]$ (Fault, replaces 2)
   * **Total LRU Faults = 10**.
   * **Comparison**: LRU (10 faults) performed slightly worse than FIFO (9 faults) on this specific reference string because the repeating cyclic access patterns ($1, 2, 3, 4...$) penalized LRU's recent-use tracking.
