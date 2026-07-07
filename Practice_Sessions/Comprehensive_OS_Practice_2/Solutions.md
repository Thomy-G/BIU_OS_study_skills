# Operating Systems (89-231) — Comprehensive Practice Session 2
## Official Solution Manual and Grading Blueprints

---

### Solution 1: Linux Systems Programming — Signals, SUID & POSIX C Code

1. **Signal Masking & Execution Trace (10 Points)**:
   - **Signal Mask Inheritance (3 Points)**: During a `fork()` system call, the child process inherits a copy of the parent process's signal mask (blocked signal set) and pending signal set. Thus, since `SIGUSR1` was blocked in the parent before the `fork()`, it is also blocked in the child when the child begins execution.
   - **Terminal Output Sequence (4 Points)**: The output is concurrent but has structured constraints due to process synchronization:
     - The child registers a handler, prints `"Child Ready\n"`, and unblocks `SIGUSR1`.
     - The parent prints `"Parent Signaling\n"`, sends `SIGUSR1` using `kill()`, and blocks on `wait(NULL)`.
     - Because `SIGUSR1` is blocked initially, even if the parent sends `SIGUSR1` *before* the child unblocks it, the signal remains pending in the child's context. Once the child calls `sigprocmask` to unblock it, the kernel delivers the pending signal immediately, causing the child to run `handler`, print `"Handler\n"`, and exit.
     - Once the child terminates, the parent's `wait(NULL)` returns, and it prints `"Parent Done\n"`.
     - **Possible Output Sequences**:
       - *Sequence A*:
         ```text
         Parent Signaling
         Child Ready
         Handler
         Parent Done
         ```
       - *Sequence B*:
         ```text
         Child Ready
         Parent Signaling
         Handler
         Parent Done
         ```
       - *Note*: `"Handler"` must appear after both `"Child Ready"` and `"Parent Signaling"`. `"Parent Done"` must be the final line.
   - **Race Conditions & Scheduling (3 Points)**: There is no race condition regarding signal delivery because the signal is blocked initially. If the parent signals "too fast," the signal is queued as pending in the child. As soon as the child unblocks it, it is handled.

2. **Privilege Separation & SUID (10 Points)**:
   - **Symbolic Representation (3 Points)**: Octal `4751` is parsed as:
     - Set-UID bit ($4$) is set.
     - Owner permission `7` ($rwx$) becomes $rws$ (because Set-UID is set and execution is allowed).
     - Group permission `5` ($r-x$) remains $r-x$.
     - Others permission `1` ($--x$) remains $--x$.
     - **Symbolic format**: `-rwsr-x--x` (or `rwsr-x--x`).
   - **RUID & EUID Values (4 Points)**:
     - **Real UID (RUID)** $= 1001$ (identifies the actual user who spawned the process).
     - **Effective UID (EUID)** $= 0$ (identifies root, the owner of the executable file, because the SUID bit causes the process to run with the permissions of the file owner).
   - **Purpose of Privilege Separation (3 Points)**: SUID allows normal unprivileged users to execute specific tasks that require administrative permissions (e.g., editing the system password database via `/usr/bin/passwd` or updating directories) in a controlled way, without giving them full administrator access or revealing the root password.

---

### Solution 2: Thread Concurrency & Synchronization — Dining Philosophers & Race Conditions

1. **Dining Philosophers Gatekeeper (10 Points)**:
   - **Range of $k$ (5 Points)**: The system is guaranteed to be deadlock-free for **$1 \le k \le 4$**.
     - *Proof*: In a system with 5 chopsticks, a deadlock can only occur if all 5 philosophers are at the table and each grabs 1 chopstick, leading to circular wait. By restricting the number of philosophers at the table to at most $k \le 4$, at least one chopstick must remain on the table. The philosopher adjacent to the free chopstick will always be able to pick it up, eat, release both of their chopsticks, and allow the next philosophers to eat, preventing deadlock.
   - **Worst-case eating concurrency (5 Points)**: Under $k = 4$, up to 4 philosophers are allowed at the table.
     - *Absolute Max Concurrency*: 2 philosophers can eat at the same time (e.g., $P_0$ uses $C_0, C_1$ and $P_2$ uses $C_2, C_3$).
     - *Worst-Case Concurrency*: If 4 philosophers grab their left chopstick first (e.g., $P_0$ holds $C_0$, $P_1$ holds $C_1$, $P_2$ holds $C_2$, $P_3$ holds $C_3$), then only $P_3$ will find its right chopstick $C_4$ free. $P_3$ will eat, while the other 3 wait. Thus, in the worst-case allocation of chopsticks, the concurrent eating rate is **1**. (At least one is guaranteed to eat due to deadlock-freedom).

2. **Race Condition Tracing (10 Points)**:
   - **All possible final values of `g` (5 Points)**: The possible final values are **2, 3, 4, 5, and 6**.
     - *Explanation*: $6$ is the result of non-interleaved sequential execution. Smaller values occur when a thread's read of a smaller value is overwritten by another thread, causing some increments to be lost. Since both threads must perform at least their final write, the absolute minimum value is $2$ (obtained when Thread A overwrites Thread B's work).
   - **Trace for Minimum Value ($g = 2$) (5 Points)**:
     1. Thread A executes its first loop iteration, reading $g = 0$ into its local register $R_{A} = 0$. (It is preempted before adding or writing).
     2. Thread B runs to completion sequentially (performing 2 iterations of $+2$):
        - Loop 1: Reads $g = 0$, writes $g = 2$.
        - Loop 2: Reads $g = 2$, writes $g = 4$. (Now $g = 4$).
     3. Thread A resumes. It increments its local register $R_{A} = 0 + 1 = 1$, and writes $1$ back to $g$ ($g = 1$). (This overwrites B's entire calculation).
     4. Thread A executes its second iteration: it reads $g = 1$ into $R_{A} = 1$, increments it to $2$, and writes $2$ back to $g$ ($g = 2$).
     5. The final value of $g$ is $2$.

---

### Solution 3: CPU Scheduling — Preemptive SJF with I/O & Processor Affinity

1. **Preemptive SJF with CPU & I/O Bursts (15 Points)**:
   - **Gantt Charts (8 Points)**:
     - **CPU Core**:
       - $P_2$ executes: $t = 0 \to 2$ (2 ms CPU burst, finishes burst, starts I/O).
       - $P_1$ executes: $t = 2 \to 5$ (3 ms CPU burst).
       - $P_2$ executes: $t = 5 \to 8$ (3 ms CPU burst).
       - Idle: $t = 8 \to 9$ (CPU is idle while $P_1$ is finishing its I/O burst).
       - $P_1$ executes: $t = 9 \to 11$ (2 ms CPU burst).
     - **I/O Device**:
       - Idle: $t = 0 \to 2$.
       - $P_2$ executes I/O: $t = 2 \to 4$ (2 ms I/O burst, returns to CPU ready queue).
       - Idle: $t = 4 \to 5$.
       - $P_1$ executes I/O: $t = 5 \to 9$ (4 ms I/O burst, returns to CPU ready queue).
       - Idle: $t = 9 \to 11$.
   - **CPU Utilization (3 Points)**:
     - Total scheduling time = 11 ms.
     - CPU active time = $10\text{ ms}$ (active at $0\to8$ and $9\to11$).
     - CPU Utilization $= \frac{10}{11} \approx \mathbf{90.91\%}$.
   - **Average Waiting Time (AWT) (4 Points)**:
     - $P_1$ waiting time: Waits from $t=0 \to 2$ (2 ms) before running on CPU. (No waiting after I/O burst since CPU is idle at $t=9$). Total $= 2\text{ ms}$.
     - $P_2$ waiting time: Runs immediately at $t=0$. Starts I/O immediately at $t=2$. Finishes I/O at $t=4$, but sits in ready queue until $t=5$ (1 ms) while $P_1$ finishes CPU execution. Total $= 1\text{ ms}$.
     - **AWT** $= \frac{2 + 1}{2} = \mathbf{1.5\text{ ms}}$.

2. **Processor Affinity Mechanics (5 Points)**:
   - **Definitions (2 Points)**:
     - **Soft Affinity**: The OS tries to run a process on the same core, but will migrate it to another core if load-balancing demands it.
     - **Hard Affinity**: The process is strictly tied to a subset of CPU cores (e.g., using `sched_setaffinity()`), and the OS is not allowed to run it elsewhere.
   - **OS Rationale (3 Points)**: When a process runs on a CPU core, its data and code fill the core's local cache hierarchy (L1/L2/L3). Rescheduling the process on a different core invalidates these caches, causing "cold cache" latency overheads as the core retrieves the process's data from main memory. Preserving affinity maintains cache hits and optimizes speed.

---

### Solution 4: Memory Management — TLB Upgrades & Working Set

1. **TLB vs. RAM Upgrade Trade-off (10 Points)**:
   - **Formula**: $\text{EAT} = \text{TLB lookup} + (2 - \alpha) \cdot \text{Memory Access}$.
   - **Option A (Faster RAM)**:
     - Memory Access $= 60\text{ ns}$, $\alpha = 90\%$.
     - $\text{EAT}_A = 10 + (2 - 0.90) \cdot 60 = 10 + 1.1 \cdot 60 = 10 + 66 = \mathbf{76\text{ ns}}$.
   - **Option B (Better TLB)**:
     - Memory Access $= 100\text{ ns}$, $\alpha = 98\%$.
     - $\text{EAT}_B = 10 + (2 - 0.98) \cdot 100 = 10 + 1.02 \cdot 100 = 10 + 102 = \mathbf{112\text{ ns}}$.
   - **Recommendation**: Choose **Option A** (Faster RAM), because it results in a much faster Effective Access Time ($76\text{ ns}$ vs. $112\text{ ns}$).

2. **Working Set Tracking (10 Points)**:
   - **Working Set $W(t_8)$ (3 Points)**: The 8 most recent references at $t_8$ are: `c, c, b, d, a` (at $t_5 \dots t_8$).
     - The window of size $\Delta = 4$ spans references from $t_5$ to $t_8$: `c` ($t_5$), `b` ($t_6$), `d` ($t_7$), `a` ($t_8$).
     - Working Set $W(t_8) = \mathbf{\{a, b, c, d\}}$. (Size = 4).
   - **Trace table (4 Points)**:
     - $t_1$: [d] (Size 1)
     - $t_2$: [d] (Size 1)
     - $t_3$: [d, a] (Size 2)
     - $t_4$: [d, a, c] (Size 3)
     - $t_5$: [d, a, c] (Size 3)
     - $t_6$: [a, c, b] (Size 3)
     - $t_7$: [c, b, d] (Size 3)
     - $t_8$: [c, b, d, a] (Size 4)
     - $t_9$: [b, d, a, c] (Size 4)
     - $t_{10}$: [d, a, c, b] (Size 4)
     - $t_{11}$: [a, c, b, d] (Size 4)
     - $t_{12}$: [c, b, d] (Size 3)
   - **Average Working Set Size (3 Points)**:
     - Sum of sizes $= 1 + 1 + 2 + 3 + 3 + 3 + 3 + 4 + 4 + 4 + 4 + 3 = 35$.
     - **Average Size** $= \frac{35}{12} \approx \mathbf{2.92}$.

---

### Solution 5: File Systems & Mass Storage — Linked Allocation & SCAN/LOOK

1. **Linked Allocation Disk I/O Count (10 Points)**:
   - **Total Operations**: **1 Disk Sector Read** and **2 Disk Sector Writes** (Total of **3 Disk I/O operations**).
   - **Step-by-step Explanation**:
     1. Since the file is $12\text{ KB}$ and each block is $4\text{ KB}$, the file consists of exactly 3 blocks (Block 1, Block 2, Block 3).
     2. To append a block, we must write the new data to a newly allocated block (Block 4) on disk. This requires **1 Disk Write** (to write Block 4's data, with its next pointer set to null).
     3. We must update the pointer of the previous last block (Block 3) to point to Block 4.
     4. Because the FCB is cached in memory and contains a pointer to the last block of the file (Block 3), we know its address immediately. However, its contents are on disk. We must perform **1 Disk Read** to pull Block 3 into memory, update its next pointer to Block 4, and then perform **1 Disk Write** to save Block 3 back to disk.

2. **SCAN vs. LOOK Disk head calculations (10 Points)**:
   - **SCAN Seek Distance (5 Points)**:
     - Queue: $15, 20, 40, 85, 120, 180$. Start: $50$, moving upward.
     - **SCAN Path**: $50 \to 85 \to 120 \to 180 \to 199$ (reaches track limit) $\to 40 \to 20 \to 15$.
     - Math: $(199 - 50) + (199 - 15) = 149 + 184 = \mathbf{333\text{ tracks}}$.
   - **LOOK Seek Distance (5 Points)**:
     - **LOOK Path**: $50 \to 85 \to 120 \to 180$ (reverses at the highest request, doesn't go to 199) $\to 40 \to 20 \to 15$.
     - Math: $(180 - 50) + (180 - 15) = 130 + 165 = \mathbf{295\text{ tracks}}$.
