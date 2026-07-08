# 📝 EXAM BLUEPRINT #3

## Part A: Questions

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

## Part B: Official Solution Manual

### Solution 1: Process Concurrency & Signal Inheritance Bounds

1. **Pending Mask Inheritance Assessment:**
   The child process will evaluate `SIGUSR1` as **Clear**.
   * **The Rule:** Under POSIX standards, when a process invokes `fork()`, the child process inherits a copy of the parent's **Blocked Signal Mask**. However, the child's **Pending Signal Mask** is explicitly zeroed out during initialization. Any signals sent to the parent before or during the fork are not inherited by the child.
2. **Terminal Execution Output Sequence:**
   * Line 1: Child runs first or parent blocks at `wait(NULL)`. Child checks its pending status and prints: `"Child: USR1 is Clear\n"`. Then the child exits.
   * Line 2: The child's exit unblocks the parent's `wait(NULL)`. The parent executes its next instruction and prints: `"Parent: Unblocking Signals\n"`.
   * Line 3: The parent calls `sigprocmask` to unblock `SIGUSR1`. The kernel immediately intercepts the pending signal (generated by `kill`) and shifts execution to `signal_handler`, which prints: `"Signal 10 Intercepted!\n"`.
   * **Final Printed Output:**
     ```text
     Child: USR1 is Clear
     Parent: Unblocking Signals
     Signal 10 Intercepted!
     ```

### Solution 2: Advanced Memory Systems — Memory Fragmentation

1. **Contiguous Memory Sizing Comparisons:**
   * Initial Holes: $[100\text{ KB}, \,\, 500\text{ KB}, \,\, 200\text{ KB}, \,\, 300\text{ KB}]$.
   * **First-Fit Placement Track:**
     * Request $120\text{ KB} \rightarrow$ Places in the $500\text{ KB}$ hole (leaves $380\text{ KB}$ free).
     * Request $280\text{ KB} \rightarrow$ Places in the remaining $380\text{ KB}$ segment of that same hole (leaves $100\text{ KB}$ free).
     * Request $150\text{ KB} \rightarrow$ Places in the $200\text{ KB}$ hole (leaves $50\text{ KB}$ free).
   * **Best-Fit Placement Track:**
     * Request $120\text{ KB} \rightarrow$ Places in the $200\text{ KB}$ hole (leaves $80\text{ KB}$ free).
     * Request $280\text{ KB} \rightarrow$ Places in the $300\text{ KB}$ hole (leaves $20\text{ KB}$ free).
     * Request $150\text{ KB} \rightarrow$ Places in the $500\text{ KB}$ hole (leaves $350\text{ KB}$ free).
   * Best-Fit creates worse fragment isolation here because it leaves behind tiny, unusable memory remnants (like the $20\text{ KB}$ hole) that contribute directly to severe external fragmentation.
2. **Fragmentation Definitions & Thrashing Mitigation:**
   * **Internal Fragmentation:** Unused memory locked inside a fixed-size frame allocated to a process.
   * **External Fragmentation:** Unallocated memory spaces scattered across RAM that are collectively large enough to satisfy a request, but cannot be used because they are non-contiguous.
   * **Thrashing Control:** The Working Set Model prevents thrashing by ensuring a process is only scheduled if its active working set of pages ($\sum |W_i|$) fits within the available physical memory frames ($m$). If memory is overcommitted, the OS swaps out an entire process to protect the system's execution stability.

### Solution 3: Mass Storage & Asymmetric Index Pointers

1. **Pointer Capacity per Block:**
   $$\text{Pointers} = \frac{\text{Block Size}}{\text{Pointer Size}} = \frac{2048\text{ bytes}}{8\text{ bytes}} = \mathbf{256\text{ pointers per index block}}$$
2. **File Sizing Boundary Accumulation:**
   * Direct Capacity: $10 \times 2048 = 20,480\text{ bytes}$.
   * Single Indirect: $256 \times 2048 = 524,288\text{ bytes}$.
   * Double Indirect: $256^2 \times 2048 = 65,536 \times 2048 = 134,217,728\text{ bytes}$.
   * Triple Indirect: $256^3 \times 2048 = 16,777,216 \times 2048 = 34,359,738,368\text{ bytes}$.
   * **Maximum File Size Footprint:**
     $$\text{Total} = 20,480 + 524,288 + 134,217,728 + 34,359,738,368 = \mathbf{34,494,491,136\text{ bytes}} \approx \mathbf{32.125\text{ GB}}$$

### Solution 4: Process Coordination — The Molecule Builder

```c
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>

int h_count = 0, o_count = 0;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
sem_t h_queue, o_queue, print_barrier;

void init_synchronizer() {
    sem_init(&h_queue, 0, 0);
    sem_init(&o_queue, 0, 0);
    sem_init(&print_barrier, 0, 1); // Serializes the print phase
}

void* Hydrogen_Thread(void* arg) {
    pthread_mutex_lock(&lock);
    h_count++;
    if (h_count >= 2 && o_count >= 1) {
        h_count -= 2; o_count -= 1;
        sem_wait(&print_barrier); // Acquire print lock
        sem_post(&h_queue); sem_post(&o_queue);
        pthread_mutex_unlock(&lock);
    } else {
        pthread_mutex_unlock(&lock);
        sem_wait(&h_queue);
    }
    printf("H");
    return NULL;
}

void* Oxygen_Thread(void* arg) {
    pthread_mutex_lock(&lock);
    o_count++;
    if (h_count >= 2 && o_count >= 1) {
        h_count -= 2; o_count -= 1;
        sem_wait(&print_barrier); // Acquire print lock
        sem_post(&h_queue); sem_post(&h_queue);
        pthread_mutex_unlock(&lock);
    } else {
        pthread_mutex_unlock(&lock);
        sem_wait(&o_queue);
    }
    printf("O");
    sem_post(&print_barrier); // Release print lock for the next molecule group
    return NULL;
}
```

### Solution 5: Hardware Foundations — Data Interception Primitives

1. **DMA Ownership Handshake Protocol:**
   * The CPU writes details about the target operation (transfer direction, disk sector address, target RAM destination address, and block size count) to the DMA controller registers.
   * The DMA Controller issues a bus request to the CPU to assume control of the shared system memory bus.
   * The CPU grants the request, enters a hold state, and disconnects its lines from the system bus.
   * The DMA Controller streams data blocks directly from the device controller into physical RAM frames autonomously.
   * Once the transfer completes, the DMA controller relinquishes bus ownership and issues a hardware interrupt to the CPU to signal completion.
2. **Interrupt Handshake Sequence Primitives:**
   * **Vectored Interrupts:** The device controller passes a specific index identifier down an interrupt line. The CPU uses this value to look up the exact handler address in the Interrupt Vector Table, minimizing lookup latency.
   * **Polling:** The CPU runs a loop that periodically queries device registers sequentially to check for pending transactions. This consumes significant CPU cycles.
   * The **Program Status Word (PSW)** register contains the core bit flags that manage the CPU's current execution privilege layer (User Mode vs. Kernel Mode).
