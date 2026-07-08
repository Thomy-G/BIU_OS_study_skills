## Part B: Official Solution Manual
### Solution 1: CPU Scheduling

1. **Gantt Charts & AWT Calculations:**
   * **FCFS Sequence:** $A(0-10) \rightarrow B(10-14) \rightarrow C(14-16) \rightarrow D(16-22) \rightarrow E(22-30)$.
     * Waiting Times: $A=0, B=10, C=14, D=16, E=22$.
     * $\text{AWT} = \frac{0 + 10 + 14 + 16 + 22}{5} = \frac{62}{5} = \mathbf{12.4\text{ ms}}$.
   * **SJF Sequence (Sorted Bursts):** $C(0-2) \rightarrow B(2-6) \rightarrow D(6-12) \rightarrow E(12-20) \rightarrow A(20-30)$.
     * Waiting Times: $C=0, B=2, D=6, E=12, A=20$.
     * $\text{AWT} = \frac{0 + 2 + 6 + 12 + 20}{5} = \frac{40}{5} = \mathbf{8.0\text{ ms}}$.
   * **Round Robin ($q=3$) Execution Trace:**
     * Queue progression steps: $A(3) \rightarrow B(3) \rightarrow C(2) \rightarrow D(3) \rightarrow E(3) \rightarrow A(3) \rightarrow B(1) \rightarrow D(3) \rightarrow E(3) \rightarrow A(3) \rightarrow E(2) \rightarrow A(1)$.
     * Turnaround Times: $C=8, B=15, D=21, E=29, A=30$.
     * Waiting Times ($\text{Turnaround} - \text{Burst}$): $A=30-10=20$; $B=15-4=11$; $C=8-2=6$; $D=21-6=15$; $E=29-8=21$.
     * $\text{AWT} = \frac{20 + 11 + 6 + 15 + 21}{5} = \frac{73}{5} = \mathbf{14.6\text{ ms}}$.

2. **Algorithmic Evaluation:**
   To minimize waiting time variance (guaranteeing fair predictability), **Round Robin** or **FCFS** is selected over SJF. While SJF minimizes the mathematical mean waiting time, it maximizes variance because short tasks jump ahead immediately while long tasks encounter maximum delay variance, creating severe starvation risks.

### Solution 2: Process Synchronization

1. **Starvation Proof:**
   **Yes.** If a steady stream of Readers arrives continuously, `reader_count` will remain strictly greater than `0`. As a result, the Readers will never execute the final branch that releases `sem_post(&database_sem)`. The waiting Writers trapped at `sem_wait(&database_sem)` will experience indefinite starvation.
2. **Synchronization Correction:**
   Introduce a `writer_waiting_sem` initialized to `1`. Writers acquire it before locking the database; new Readers must also clear it briefly during their entry protocol.
   ```c
   // Reader Entry
   sem_wait(&writer_waiting_sem);
   pthread_mutex_lock(&mock_mutex);
   reader_count++;
   if (reader_count == 1) sem_wait(&database_sem);
   pthread_mutex_unlock(&mock_mutex);
   sem_post(&writer_waiting_sem);
   ```

### Solution 3: Memory Management

1. **Address Bit Allocation:**
   * $\text{Page Size} = 4\text{ KB} = 2^{12}\text{ bytes} \rightarrow \mathbf{12\text{ bits}}$ for Offset.
   * $\text{Outer Page Table Entries} = 2^{10} \rightarrow \mathbf{10\text{ bits}}$ for Outer Directory Index.
   * $\text{Inner Page Table Index} = 32 - 12 - 10 = \mathbf{10\text{ bits}}$.
2. **EAT Algebraic Analysis:**
   On a TLB Hit, time $= \epsilon + m$. On a TLB Miss, time $= \epsilon + 3m$ (2 page table lookups + 1 frame access).
   $$\text{EAT} = \alpha(\epsilon + m) + (1-\alpha)(\epsilon + 3m)$$
   $$145 = \alpha(15 + 120) + (1-\alpha)(15 + 360)$$
   $$145 = 135\alpha + 375 - 375\alpha \rightarrow 240\alpha = 230 \rightarrow \alpha = \frac{230}{240} \approx \mathbf{95.83\%}$$

### Solution 4: Linux Systems Programming

1. **Process Tree Topology:**
   Exactly **3 processes** are initialized:
   * Main Process $\rightarrow$ Forks Child 1.
   * Child 1 $\rightarrow$ Forks Child-Child.
2. **Terminal Output Trace:**
   Due to `wait(NULL)` controls forcing children to finish first, execution moves from the youngest process upward:
   ```text
   Child-Child: val = 35
   Child-Parent: val = 15
   Main-Parent: val = 10
   ```

### Solution 5: File Systems

1. **File Capacity Sizing:**
   * $\text{Pointers per block} = \frac{4096}{4} = 1024\text{ pointers}$.
   * $\text{Total Blocks Accessible} = 12 + 1024 + 1024^2 = 12 + 1024 + 1,048,576 = 1,049,612\text{ blocks}$.
   * $\text{Max Bytes} = 1,049,612 \times 4096 = \mathbf{4,299,210,752\text{ bytes}} \approx \mathbf{4\text{ GB}}$.
2. **Disk I/O Boundary Verification:**
   * Direct Limit: $12 \times 4096 = 49,152\text{ bytes}$.
   * Single Indirect Limit: $49,152 + (1024 \times 4096) = 4,243,456\text{ bytes} \approx 4.24\text{ MB}$.
   Since $10\text{ MB} > 4,243,456\text{ bytes}$, the target data block falls inside the **Double Indirect Pointer tier**.
   * **Total Disk Block Reads = 3** (1st level index block + 2nd level index block + 1 data block payload).