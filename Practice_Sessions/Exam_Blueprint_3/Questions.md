# 📝 EXAM BLUEPRINT #3



### Question 1: Process Concurrency & Signal Inheritance Bounds (20 Points)

A Linux process sets up a signal architecture block before splitting via a fork statement call:

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>

void signal_handler(int sig) {
    printf("Signal %d Intercepted!\n", sig);
}

int main() {
    sigset_t block_mask;
    sigemptyset(&block_mask);
    sigaddset(&block_mask, SIGUSR1);
    
    // Trap Initialization
    signal(SIGUSR1, signal_handler);
    sigprocmask(SIG_BLOCK, &block_mask, NULL);
    
    pid_t pid = fork();
    if (pid == 0) {
        // Child execution path
        sigset_t pending_signals;
        sigpending(&pending_signals);
        
        if (sigismember(&pending_signals, SIGUSR1)) {
            printf("Child: USR1 is Pending\n");
        } else {
            printf("Child: USR1 is Clear\n");
        }
        exit(0);
    } else {
        // Parent execution path
        kill(getpid(), SIGUSR1); // Parent throws USR1 to itself
        wait(NULL);
        printf("Parent: Unblocking Signals\n");
        sigprocmask(SIG_UNBLOCK, &block_mask, NULL);
    }
    return 0;
}
```

1. **Pending Status Assessment (10 Points):** State clearly whether the child process evaluates `SIGUSR1` as pending or clear. Explain the underlying POSIX inheritance rule that dictates this behavior.
2. **Terminal Trace Mapping (10 Points):** Write down the exact string layout sequence printed to stdout during execution. Note: `SIGUSR1` maps to integer **`10`** on standard Linux systems.

---

### Question 2: Advanced Memory Systems — Memory Fragmentation (20 Points)

1. **Contiguous Allocation Algorithms (10 Points):** Given four free memory holes of sizes $100\text{ KB}$, $500\text{ KB}$, $200\text{ KB}$, and $300\text{ KB}$ (in physical address order), show how three consecutive allocation requests of $120\text{ KB}$, $280\text{ KB}$, and $150\text{ KB}$ are placed using:
   * **First-Fit**
   * **Best-Fit**
   Which approach creates worse external fragmentation in this scenario?
2. **Thrashing Mitigation (10 Points):** Explain the difference between **Internal Fragmentation** and **External Fragmentation**. How does the **Working Set Model** prevent memory **Thrashing** in multi-programmed systems?

---

### Question 3: Mass Storage & Asymmetric Index Pointers (20 Points)

An inode file mapping structure utilizes $2\text{ KB}$ ($2048\text{ bytes}$) blocks. File index reference pointers map across a 64-bit space, consuming exactly $8\text{ bytes}$ per pointer entry. The control map defines:

* 10 Direct Pointers
* 1 Single Indirect Pointer
* 1 Double Indirect Pointer
* 1 Triple Indirect Pointer

1. **Pointer Block Capacity (10 Points):** Calculate the exact number of block pointers that can be stored inside a single $2\text{ KB}$ index block.
2. **File Size Capacity Limits (10 Points):** Calculate the maximum absolute file size capacity (in bytes) supported by this multi-level pointer layout.

---

### Question 4: Process Coordination — The Molecule Builder (20 Points)

An operating system thread synchronization pool must coordinate two types of threads, **Oxygen** and **Hydrogen**, to build water molecules ($H_2O$). The threads must form groups consisting of exactly 2 Hydrogens and 1 Oxygen before printing their characters.

Complete the following synchronization code skeleton using only **POSIX Semaphores (`sem_t`)** and **Mutexes (`pthread_mutex_t`)** to ensure that groups are printed atomically without interleaving (e.g., printing always outputs completed triplets like `HHO`, `HOH`, or `OHH`).

```c
// Define necessary shared global variables, mutexes, and semaphores here:
int h_count = 0, o_count = 0;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
sem_t h_queue, o_queue;

void init_synchronizer() {
    // Complete semaphore initialization code here:
}

void* Hydrogen_Thread(void* arg) {
    // Implement entry, print, and exit barrier logic:
    printf("H");
    return NULL;
}

void* Oxygen_Thread(void* arg) {
    // Implement entry, print, and exit barrier logic:
    printf("O");
    return NULL;
}
```

---

### Question 5: Hardware Foundations — Data Interception Primitives (20 Points)

1. **DMA Ownership Handshake (10 Points):** Detail the chronological bus ownership handshake steps that occur between a CPU, a **Direct Memory Access (DMA) Controller**, and a high-bandwidth device controller during a disk sector read operation.
2. **Interrupt Handshake Sequence (10 Points):** Explain the operational difference between **Vectored Interrupts** and **Polling**. Which register in the CPU control map enables changing privilege domains from User Space to Kernel Space during an interrupt?

---