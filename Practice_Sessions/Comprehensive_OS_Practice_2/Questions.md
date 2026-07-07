# Operating Systems (89-231) — Comprehensive Practice Session 2
## Practice Questions covering Unpracticed Curriculum Areas

---

### Question 1: Linux Systems Programming — Signals, SUID & POSIX C Code (20 Points)

1. **Signal Masking & Execution Trace (10 Points)**: Analyze the following POSIX C program:

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <signal.h>
#include <stdlib.h>

void handler(int sig) {
    printf("Handler\n");
    exit(0);
}

int main() {
    sigset_t set, oldset;
    sigemptyset(&set);
    sigaddset(&set, SIGUSR1);
    
    // Block SIGUSR1
    sigprocmask(SIG_BLOCK, &set, &oldset);
    
    pid_t pid = fork();
    if (pid == 0) {
        // Child registers handler and unblocks SIGUSR1
        struct sigaction sa;
        sa.sa_handler = handler;
        sigemptyset(&sa.sa_mask);
        sa.sa_flags = 0;
        sigaction(SIGUSR1, &sa, NULL);
        
        printf("Child Ready\n");
        sigprocmask(SIG_UNBLOCK, &set, NULL);
        
        // Wait for signals
        while(1) {
            pause();
        }
    } else {
        // Parent sends signal immediately
        printf("Parent Signaling\n");
        kill(pid, SIGUSR1);
        wait(NULL);
        printf("Parent Done\n");
    }
    return 0;
}
```
   - Explain how signal masks are inherited during a `fork()` system call.
   - Trace the execution and write the exact output sequence of this program. Explain any potential race conditions or synchronization factors.

2. **Privilege Separation & SUID (10 Points)**: A script file named `secure_updater` is owned by user `root` (UID `0`). Its permission bits are set to octal representation `4751` (which has the Set-UID bit set).
   - Draw or explain the symbolic representation of these permissions.
   - A normal system user with UID `1001` executes this script. Define the **Real UID (RUID)** and **Effective UID (EUID)** of the process during its execution, and explain the purpose of this privilege separation model.

---

### Question 2: Thread Concurrency & Synchronization — Dining Philosophers & Race Conditions (20 Points)

1. **Dining Philosophers Gatekeeper (10 Points)**: Five philosophers ($P_0 \dots P_4$) sit around a table with 5 shared chopsticks ($C_0 \dots C_4$). To prevent deadlocks, a gatekeeper semaphore `gate` is introduced, and each philosopher must execute the following wrapper:
   ```c
   sem_wait(&gate);
   sem_wait(&chopstick[i]);
   sem_wait(&chopstick[(i + 1) % 5]);
   
   // Eat
   
   sem_post(&chopstick[i]);
   sem_post(&chopstick[(i + 1) % 5]);
   sem_post(&gate);
   ```
   - If the `gate` semaphore is initialized to value $k$, determine which values of $k$ guarantee that the system is **deadlock-free**. Prove your answer.
   - For $k = 4$, determine the maximum number of philosophers that can eat concurrently in the worst-case scenario.

2. **Race Condition Tracing (10 Points)**: Two concurrent threads (Thread A and Thread B) share a global variable `g` which is initialized to `0`. They execute the following loops:

| Thread A | Thread B |
| :--- | :--- |
| `for (int i = 0; i < 2; i++) {` | `for (int j = 0; j < 2; j++) {` |
| `    g = g + 1;` | `    g = g + 2;` |
| `}` | `}` |

Assume that registers are local to each thread, but reads and writes of `g` are atomic assembly operations (`LDR r1, [g]`, `ADD r1, r1, #val`, `STR r1, [g]`).
   - List all possible final values of `g` after both threads complete.
   - Show a step-by-step interleaving trace of read, add, and write operations that results in the **minimum possible final value** of `g`.

---

### Question 3: CPU Scheduling — Preemptive SJF with I/O & Processor Affinity (20 Points)

1. **Preemptive SJF with CPU & I/O Bursts (15 Points)**: Two processes ($P_1, P_2$) arrive in the system ready queue at $t = 0$. Their execution patterns are defined as:
   - $P_1$: CPU burst of 3 ms, then I/O burst of 4 ms, then CPU burst of 2 ms.
   - $P_2$: CPU burst of 2 ms, then I/O burst of 2 ms, then CPU burst of 3 ms.

   *Constraints*: 
   - Scheduling is Preemptive Shortest Job First (SJF) (the scheduler preempts a process if a arriving/returning process has a shorter remaining CPU burst).
   - There is a single CPU and a single I/O device. CPU scheduling does not block the I/O device (they can run concurrently).
   - Assume **zero context-switch overhead**.
   - Draw the CPU and I/O execution Gantt Charts and calculate the **CPU Utilization** and the **Average Waiting Time (AWT)** of the processes.

2. **Processor Affinity Mechanics (5 Points)**: In Symmetric Multiprocessing (SMP) schedulers:
   - Define **Processor Affinity** and contrast **Soft Affinity** with **Hard Affinity**.
   - Explain why operating systems prefer to preserve processor affinity (mentioning CPU caches and performance).

---

### Question 4: Memory Management — TLB Upgrades & Working Set (20 Points)

1. **TLB vs. RAM Upgrade Trade-off (10 Points)**: A system uses a single-level page table:
   - Memory access latency is $100\text{ ns}$.
   - TLB lookup latency is $10\text{ ns}$.
   - The current TLB hit ratio ($\alpha$) is $90\%$.
   A system administrator wants to reduce the Effective Access Time (EAT) and has two upgrade options:
   - **Option A**: Buy faster RAM, reducing memory access latency to $60\text{ ns}$ (TLB hit ratio remains $90\%$).
   - **Option B**: Buy a larger/faster TLB, increasing the TLB hit ratio ($\alpha$) to $98\%$ (memory access latency remains $100\text{ ns}$).
   Calculate the EAT for both options and determine which option yields the best system performance.

2. **Working Set Tracking (10 Points)**: A process references the following page trace:
   $$\text{Trace: } d, \quad d, \quad a, \quad c, \quad c, \quad b, \quad d, \quad a, \quad c, \quad b, \quad d, \quad d$$
   Using a working-set window size of $\Delta = 4$:
   - Find the working set $W(t_8)$ immediately after referencing the 8th page (`a`).
   - Calculate the active working set sizes for all timestamps $t_1 \dots t_{12}$ and compute the **Average Working Set Size** across the entire trace.

---

### Question 5: File Systems & Mass Storage — Linked Allocation & SCAN/LOOK (20 Points)

1. **Linked Allocation Disk I/O Count (10 Points)**: A file is stored on a disk using **Linked Allocation**.
   - Each disk block is $4\text{ KB}$ and contains data plus a 4-byte pointer to the next block.
   - The File Control Block (FCB) is already cached in memory, and contains pointers to both the **first block** and the **last block** of the file.
   - A process requests to append 1 block of data to the end of a file which is currently $12\text{ KB}$ in size.
   Calculate the exact number of disk sector reads and writes required to allocate, link, and write this appended block. Explain each disk operation.

2. **SCAN vs. LOOK Disk head calculations (10 Points)**: A hard disk has tracks numbered $0$ to $199$. The disk head is currently positioned at track $50$ and is moving towards higher track numbers. The queue of pending read/write track requests is:
   $$\text{Queue: } 20, \quad 85, \quad 15, \quad 120, \quad 180, \quad 40$$
   Calculate the total head seek distance (in tracks) using:
   - The **SCAN** scheduling algorithm.
   - The **LOOK** scheduling algorithm.
