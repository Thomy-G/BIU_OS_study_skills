# Operating Systems (89-231) — Comprehensive Practice Session
## Official Solution Manual and Grading Blueprints

---

### Solution 1: Shell Command Types & Permissions

1. **Shell Execution Mechanics (5 Points):**
   - External commands (e.g., `/bin/ls`, `/bin/grep`) are executed by spawning a child process via the `fork()` system call, and then replacing that child's memory image via the `exec()` system call.
   - If the change directory command (`cd`) were executed as an external command, it would run inside a child process. The child process would successfully modify its own current working directory (CWD) via the `chdir()` system call and then terminate. However, a child's environment cannot modify the parent process's environment. The parent shell process would remain in its original CWD.
   - Therefore, to alter the CWD of the shell itself, the shell process must execute `chdir()` directly. This requires `cd` to be implemented internally as a built-in shell command.

2. **Access Control List Permissions (5 Points):**
   - The command `chmod 751 analyzer.sh` sets the permission bits to octal `751`.
   - **Binary Conversion:** `7` (Owner) = `111` ($rwx$), `5` (Group) = `101` ($r-x$), `1` (Others) = `001` ($--x$).
   - **Symbolic Representation:** `-rwxr-x--x` (or `rwxr-x--x`).
   - **Actions Allowed:**
     - **Owner:** Full privileges. Can read, write/modify, and execute the script.
     - **Group:** Can read the contents of the file and execute it, but cannot write/modify it.
     - **Others:** Can only execute the script (run it), but are blocked from reading its source code or modifying it.

---

### Solution 2: Process Lifecycles & POSIX Fork Trees

1. **Process Tree Topology (10 Points):**
   - Let $P_{\text{main}}$ be the initial main process.
   - $P_{\text{main}}$ prints `"Base"`.
   - $P_{\text{main}}$ forks $P_{\text{child1}}$ (`pid1 == 0`).
   - **Inside $P_{\text{child1}}$:**
     - Prints `"Alpha"`.
     - Forks $P_{\text{child2}}$ (`pid2 == 0`).
     - In $P_{\text{child1}}$ (`pid2 > 0`), it enters the wait-block `if (pid2 > 0)`. It calls `wait(NULL)`, blocking until $P_{\text{child2}}$ terminates. Once unblocked, it prints `"Beta"` and calls `exit(0)`.
   - **Inside $P_{\text{child2}}$:**
     - Bypasses the wait-block (`pid2 == 0`). Prints `"Gamma"`.
     - Exits the outer conditional structure and prints `"Omega"`, then terminates.
   - **Inside $P_{\text{main}}$:**
     - Executes the `else` branch of `pid1`. It calls `wait(NULL)`, blocking until $P_{\text{child1}}$ terminates.
     - Once unblocked, it prints `"Delta"`, then prints `"Omega"`, and terminates.

   **Tree Diagram:**
   ```
        P_main (Prints "Base", waits for child1, prints "Delta", prints "Omega")
          |
       P_child1 (Prints "Alpha", forks child2, waits, prints "Beta", exits)
          |
       P_child2 (Bypasses wait, prints "Gamma", prints "Omega", exits)
   ```

2. **Terminal Output Sequence (10 Points):**
   Due to the explicit `wait` calls enforcing execution order, the output is deterministic with respect to the parent-child relationships:
   - $P_{\text{child1}}$ must wait for $P_{\text{child2}}$ to exit. Therefore, $P_{\text{child2}}$'s outputs (`"Gamma"` and `"Omega"`) must appear before $P_{\text{child1}}$'s `"Beta"`.
   - $P_{\text{main}}$ must wait for $P_{\text{child1}}$ to exit. Therefore, $P_{\text{child1}}$'s `"Beta"` must appear before $P_{\text{main}}$'s `"Delta"` and `"Omega"`.
   
   **Exact Output Sequence:**
   ```text
   Base
   Alpha
   Gamma
   Omega
   Beta
   Delta
   Omega
   ```

---

### Solution 3: Thread Model Resource Sharing & Blocking

1. **Resource Sharing Behavior (7 Points):**
   
| Resource / Context | Shared or Unique? | Description |
| :--- | :--- | :--- |
| **PC & CPU Registers** | **Unique per-thread** | Tracks the active execution instruction and local calculations of the thread. |
| **User Stack** | **Unique per-thread** | Stores thread-specific local variables and function frame call histories. |
| **Heap Memory** | **Shared across process** | Dynamically allocated memory (`malloc`/`new`) is visible to all threads. |
| **Static/Global Variables**| **Shared across process** | Variables in the data/BSS segments are accessible globally. |
| **Open File Descriptors** | **Shared across process** | File descriptor tables are process-wide; sharing offsets is standard. |

2. **Blocking Operations in Many-to-One ($M:1$) Models (8 Points):**
   - **Many-to-One ($M:1$) Model:** In this model, the operating system kernel is only aware of a single execution stream (the process itself) and schedules a single kernel thread. Thread scheduling is handled in user-space by a thread library. If a user thread invokes a blocking system call, the kernel blocks the entire process because it is unaware of other user-level threads. Thus, **all threads in the process are blocked**, halting execution.
   - **One-to-One ($1:1$) Model:** Here, every user-space thread maps directly to a distinct kernel thread. If a user thread performs a blocking system call, the kernel only blocks its associated kernel thread. The kernel is free to schedule the process's other threads to run on other CPU cores, preventing process-wide starvation.

---

### Solution 4: CPU Scheduling & Context Switches

1. **Gantt Chart & Waiting Math (15 Points):**

   - **First-Come, First-Served (FCFS) Sequence:**
     - $P_1$ runs: $t = 0 \to t = 8$.
     - Context Switch: $t = 8 \to t = 9$ (1 ms overhead).
     - $P_2$ runs: $t = 9 \to t = 13$ (4 ms burst).
     - Context Switch: $t = 13 \to t = 14$ (1 ms overhead).
     - $P_3$ runs: $t = 14 \to t = 17$ (3 ms burst).
     - **Gantt Representation:** `[ P1 (0-8) ][ CS (8-9) ][ P2 (9-13) ][ CS (13-14) ][ P3 (14-17) ]`
     - **Turnaround Times ($T_{\text{completion}} - T_{\text{arrival}}$):**
       - $P_1 = 8 - 0 = 8\text{ ms}$
       - $P_2 = 13 - 0 = 13\text{ ms}$
       - $P_3 = 17 - 0 = 17\text{ ms}$
       - **ATT** $= \frac{8 + 13 + 17}{3} = \frac{38}{3} = \mathbf{12.67\text{ ms}}$
     - **Waiting Times ($T_{\text{turnaround}} - T_{\text{burst}}$):**
       - $P_1 = 8 - 8 = 0\text{ ms}$
       - $P_2 = 13 - 4 = 9\text{ ms}$
       - $P_3 = 17 - 3 = 14\text{ ms}$
       - **AWT** $= \frac{0 + 9 + 14}{3} = \frac{23}{3} = \mathbf{7.67\text{ ms}}$

   - **Round Robin (RR, $q=3\text{ ms}$) Sequence:**
     - At $t=0$, Queue: $P_1(8), P_2(4), P_3(3)$.
     - $P_1$ executes: $t = 0 \to 3$ (Remaining burst: 5). Context Switch: $t = 3 \to 4$.
     - $P_2$ executes: $t = 4 \to 7$ (Remaining burst: 1). Context Switch: $t = 7 \to 8$.
     - $P_3$ executes: $t = 8 \to 11$ (Remaining burst: 0 - Terminated). Context Switch: $t = 11 \to 12$.
     - $P_1$ executes: $t = 12 \to 15$ (Remaining burst: 2). Context Switch: $t = 15 \to 16$.
     - $P_2$ executes: $t = 16 \to 17$ (Remaining burst: 0 - Terminated). Context Switch: $t = 17 \to 18$.
     - $P_1$ executes: $t = 18 \to 20$ (Remaining burst: 0 - Terminated).
     - **Gantt Representation:** `[ P1 (0-3) ][ CS (3-4) ][ P2 (4-7) ][ CS (7-8) ][ P3 (8-11) ][ CS (11-12) ][ P1 (12-15) ][ CS (15-16) ][ P2 (16-17) ][ CS (17-18) ][ P1 (18-20) ]`
     - **Turnaround Times:**
       - $P_3 = 11 - 0 = 11\text{ ms}$
       - $P_2 = 17 - 0 = 17\text{ ms}$
       - $P_1 = 20 - 0 = 20\text{ ms}$
       - **ATT** $= \frac{11 + 17 + 20}{3} = \frac{48}{3} = \mathbf{16.0\text{ ms}}$
     - **Waiting Times:**
       - $P_3 = 11 - 3 = 8\text{ ms}$
       - $P_2 = 17 - 4 = 13\text{ ms}$
       - $P_1 = 20 - 8 = 12\text{ ms}$
       - **AWT** $= \frac{8 + 13 + 12}{3} = \frac{33}{3} = \mathbf{11.0\text{ ms}}$

2. **System Overhead Trade-offs (5 Points):**
   - If the quantum $q$ is set too small, the system will spend a large percentage of CPU time performing context switches. This results in heavy overhead, reducing overall CPU utilization for actual processing.
   - If the quantum $q$ is set too large, the scheduling degrades towards FCFS, which increases response times for short, interactive tasks and leads to poor responsiveness for the user.
   - A balanced $q$ should be chosen such that it is significantly larger than a single context-switch time (e.g., $100\times$ larger) to minimize overhead, yet small enough to maintain a fluid user experience.

---

### Solution 5: Deadlocks

1. **Need Matrix Calculation (5 Points):**
   - $\textbf{Need} = \textbf{Max} - \textbf{Allocation}$
     $$\textbf{Need} = \begin{pmatrix} P_0 & 0 & 2 & 0 \\ P_1 & 1 & 2 & 2 \\ P_2 & 6 & 0 & 0 \\ P_3 & 0 & 1 & 1 \end{pmatrix}$$

2. **Safety State Evaluation (10 Points):**
   - Sum of Allocations: $\sum \textbf{Allocation} = [7, 2, 3]$.
   - Initial Available: $\textbf{Available} = \textbf{Total} - \sum \textbf{Allocation} = [8, 5, 7] - [7, 2, 3] = \mathbf{[1, 3, 4]}$.
   - **Safety Search Trace:**
     - $\textbf{Available} = [1, 3, 4]$.
     - $P_0$ is checkable: $\textbf{Need}_{P_0} = [0, 2, 0] \le \textbf{Available}$. Run $P_0$.
       - $\textbf{Available}_{\text{new}} = [1, 3, 4] + [0, 1, 0] = \mathbf{[1, 4, 4]}$.
     - $P_3$ is checkable: $\textbf{Need}_{P_3} = [0, 1, 1] \le \textbf{Available}$. Run $P_3$.
       - $\textbf{Available}_{\text{new}} = [1, 4, 4] + [2, 1, 1] = \mathbf{[3, 5, 5]}$.
     - $P_1$ is checkable: $\textbf{Need}_{P_1} = [1, 2, 2] \le \textbf{Available}$. Run $P_1$.
       - $\textbf{Available}_{\text{new}} = [3, 5, 5] + [2, 0, 0] = \mathbf{[5, 5, 5]}$.
     - At this point, only $P_2$ remains. Check $P_2$: $\textbf{Need}_{P_2} = [6, 0, 0]$.
       - Comparison: $[6, 0, 0] \not\le [5, 5, 5]$.
   - **Conclusion:** The system is **NOT in a Safe State**.
   - **Proof:** $P_2$ requires 6 units of Resource A. Even if all other processes ($P_0, P_1, P_3$) execute to completion and release their allocations, the maximum amount of resource A that can ever become available is $1 \text{ (initially available)} + 0 (P_0) + 2 (P_1) + 2 (P_3) = 5$ units. This is strictly less than $P_2$'s remaining requirement of 6 units. Therefore, if $P_2$ requests its maximum declared resources, the system will deadlock.

3. **Dynamic Request Tracking (5 Points):**
   - The OS **must deny** the request immediately.
   - **Proof:** Speculatively executing the request would transition the available vector to $\textbf{Available} = [1, 3, 4] - [1, 0, 1] = [0, 3, 3]$. Running the safety algorithm on this speculative state reveals that while $P_0, P_3,$ and $P_1$ can still finish (yielding a maximum available vector of $[5,5,5]$), $P_2$ remains unrunnable because its need $[6,0,0]$ exceeds available resources. Since the resulting state is unsafe, the OS must block $P_1$ to prevent potential deadlock.

---

### Solution 6: Two-Level Paging & Effective Access Time

1. **Address Bit Partitioning (10 Points):**
   - Page Size $= 8\text{ KB} = 2^{13}\text{ bytes} \implies \mathbf{13\text{ bits}}$ for the **Page Offset**.
   - Outer Page Table Index is explicitly bounded to $\mathbf{10\text{ bits}}$.
   - Inner Page Table Index $= 32 - 10\text{ (Outer)} - 13\text{ (Offset)} = \mathbf{9\text{ bits}}$.
   - **Address Division:** 10 bits (Outer Directory), 9 bits (Inner Directory), 13 bits (Page Offset).

2. **EAT Calculation (10 Points):**
   - On a TLB Hit, memory translation takes: $T_{\text{hit}} = \text{TLB lookup} + \text{physical memory access} = 10 + 100 = 110\text{ ns}$.
   - On a TLB Miss, memory translation requires looking up the outer page table, the inner page table, and then performing the physical memory access, plus the initial TLB lookup: $T_{\text{miss}} = 10 + 3 \times 100 = 310\text{ ns}$.
   - **EAT Formula:**
     $$\text{EAT} = \alpha(T_{\text{hit}}) + (1-\alpha)(T_{\text{miss}}) \le 125$$
     $$110\alpha + 310(1-\alpha) \le 125$$
     $$310 - 200\alpha \le 125$$
     $$200\alpha \ge 185 \implies \alpha \ge \frac{185}{200} = \mathbf{92.5\%}$$
   - **Answer:** The minimum acceptable TLB Hit Ratio is **92.5%**.

---

### Solution 7: Virtual Memory

1. **FIFO Trace & Anomaly Check (12 Points):**
   
   - **FIFO with 3 Frames:**
     - Ref string: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5.
     - Frame Trace:
       1. Ref 1 $\to$ **[1, -, -]** (Fault 1)
       2. Ref 2 $\to$ **[1, 2, -]** (Fault 2)
       3. Ref 3 $\to$ **[1, 2, 3]** (Fault 3)
       4. Ref 4 $\to$ **[4, 2, 3]** (Fault 4 - replaces 1)
       5. Ref 1 $\to$ **[4, 1, 3]** (Fault 5 - replaces 2)
       6. Ref 2 $\to$ **[4, 1, 2]** (Fault 6 - replaces 3)
       7. Ref 5 $\to$ **[5, 1, 2]** (Fault 7 - replaces 4)
       8. Ref 1 $\to$ [5, 1, 2] (Hit)
       9. Ref 2 $\to$ [5, 1, 2] (Hit)
       10. Ref 3 $\to$ **[5, 3, 2]** (Fault 8 - replaces 1)
       11. Ref 4 $\to$ **[5, 3, 4]** (Fault 9 - replaces 2)
       12. Ref 5 $\to$ [5, 3, 4] (Hit)
     - **Total Page Faults = 9**.

   - **FIFO with 4 Frames:**
     - Frame Trace:
       1. Ref 1 $\to$ **[1, -, -, -]** (Fault 1)
       2. Ref 2 $\to$ **[1, 2, -, -]** (Fault 2)
       3. Ref 3 $\to$ **[1, 2, 3, -]** (Fault 3)
       4. Ref 4 $\to$ **[1, 2, 3, 4]** (Fault 4)
       5. Ref 1 $\to$ [1, 2, 3, 4] (Hit)
       6. Ref 2 $\to$ [1, 2, 3, 4] (Hit)
       7. Ref 5 $\to$ **[5, 2, 3, 4]** (Fault 5 - replaces 1)
       8. Ref 1 $\to$ **[5, 1, 3, 4]** (Fault 6 - replaces 2)
       9. Ref 2 $\to$ **[5, 1, 2, 4]** (Fault 7 - replaces 3)
       10. Ref 3 $\to$ **[5, 1, 2, 3]** (Fault 8 - replaces 4)
       11. Ref 4 $\to$ **[4, 1, 2, 3]** (Fault 9 - replaces 5)
       12. Ref 5 $\to$ **[4, 5, 2, 3]** (Fault 10 - replaces 1)
     - **Total Page Faults = 10**.
   - **Belady's Anomaly Status:** **Yes.** Increasing physical frame allocation from 3 to 4 frames caused the number of page faults to increase from 9 to 10. This is an instance of Belady's Anomaly.

2. **LRU Frame Mapping (8 Points):**
   - **LRU with 3 Frames:**
     - Trace (tracking least recently used, with queue state):
       1. Ref 1 $\to$ **[1, -, -]** (Fault 1)
       2. Ref 2 $\to$ **[1, 2, -]** (Fault 2)
       3. Ref 3 $\to$ **[1, 2, 3]** (Fault 3)
       4. Ref 4 $\to$ **[4, 2, 3]** (Fault 4 - replaces 1, queue: [2, 3, 4])
       5. Ref 1 $\to$ **[4, 1, 3]** (Fault 5 - replaces 2, queue: [3, 4, 1])
       6. Ref 2 $\to$ **[4, 1, 2]** (Fault 6 - replaces 3, queue: [4, 1, 2])
       7. Ref 5 $\to$ **[5, 1, 2]** (Fault 7 - replaces 4, queue: [1, 2, 5])
       8. Ref 1 $\to$ [5, 1, 2] (Hit, queue: [2, 5, 1])
       9. Ref 2 $\to$ [5, 1, 2] (Hit, queue: [5, 1, 2])
       10. Ref 3 $\to$ **[3, 1, 2]** (Fault 8 - replaces 5, queue: [1, 2, 3])
       11. Ref 4 $\to$ **[3, 4, 2]** (Fault 9 - replaces 1, queue: [2, 3, 4])
       12. Ref 5 $\to$ **[3, 4, 5]** (Fault 10 - replaces 2, queue: [4, 5, 3])
     - **Total Page Faults = 10**.
     - **Comparison:** For this specific reference string, LRU with 3 frames (10 faults) performed slightly worse than FIFO with 3 frames (9 faults) due to cyclic referencing patterns.

---

### Solution 8: File System Interface & Implementation

1. **Asymmetric Inode Sizing (10 Points):**
   - Block Size $= 8\text{ KB} = 8192\text{ bytes}$.
   - Pointer Size $= 8\text{ bytes}$.
   - Pointers per index block $= \frac{8192}{8} = 1024$ pointers.
   - **Blocks Sizing Tiers:**
     - Direct Blocks $= 10$
     - Single Indirect Blocks $= 1024$
     - Double Indirect Blocks $= 1024^2 = 1,048,576$
     - Triple Indirect Blocks $= 1024^3 = 1,073,741,824$
   - **Total Blocks** $= 10 + 1024 + 1,048,576 + 1,073,741,824 = 1,074,791,434$ blocks.
   - **Max File Size** $= 1,074,791,434 \times 8192\text{ bytes} = 8,804,692,416,512\text{ bytes} \approx \mathbf{8.80\text{ TB}}$ (or $8199.6\text{ GB}$).

2. **Disk Scheduling Calculations (10 Points):**
   - Disk tracks: $0 \to 299$. Initial position: $120$, moving upward.
   - Queue: $45, 250, 15, 185, 290, 80$.
   - **C-SCAN Trace:**
     - Moves upward servicing higher tracks: $120 \to 185 \to 250 \to 290 \to 299$ (end of disk).
     - Wraps down to track $0$ at zero cost.
     - Moves upward servicing remaining tracks: $0 \to 15 \to 45 \to 80$.
   - **Seek Computations:**
     - $120 \to 185$: $185 - 120 = 65$ tracks
     - $185 \to 250$: $250 - 185 = 65$ tracks
     - $250 \to 290$: $290 - 250 = 40$ tracks
     - $290 \to 299$: $299 - 290 = 9$ tracks
     - $299 \to 0$: $0$ tracks (wrap cost is zero)
     - $0 \to 15$: $15 - 0 = 15$ tracks
     - $15 \to 45$: $45 - 15 = 30$ tracks
     - $45 \to 80$: $80 - 45 = 35$ tracks
   - **Total Seek Distance** $= 65 + 65 + 40 + 9 + 0 + 15 + 30 + 35 = \mathbf{259\text{ tracks}}$.
