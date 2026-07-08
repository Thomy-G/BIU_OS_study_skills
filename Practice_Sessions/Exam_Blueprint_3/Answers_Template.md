# 📝 EXAM BLUEPRINT #3



### Question 1: Process Concurrency & Signal Inheritance Bounds (20 Points)

*Write your answer here:*



#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>


    
    
        

1. **Pending Status Assessment (10 Points):** State clearly whether the child process evaluates `SIGUSR1` as pending or clear. Explain the underlying POSIX inheritance rule that dictates this behavior.

*Write your answer here:*

2. **Terminal Trace Mapping (10 Points):** Write down the exact string layout sequence printed to stdout during execution. Note: `SIGUSR1` maps to integer **`10`** on standard Linux systems.

*Write your answer here:*


---

### Question 2: Advanced Memory Systems — Memory Fragmentation (20 Points)

*Write your answer here:*


1. **Contiguous Allocation Algorithms (10 Points):** Given four free memory holes of sizes $100\text{ KB}$, $500\text{ KB}$, $200\text{ KB}$, and $300\text{ KB}$ (in physical address order), show how three consecutive allocation requests of $120\text{ KB}$, $280\text{ KB}$, and $150\text{ KB}$ are placed using:

*Write your answer here:*

2. **Thrashing Mitigation (10 Points):** Explain the difference between **Internal Fragmentation** and **External Fragmentation**. How does the **Working Set Model** prevent memory **Thrashing** in multi-programmed systems?

*Write your answer here:*


---

### Question 3: Mass Storage & Asymmetric Index Pointers (20 Points)

*Write your answer here:*




1. **Pointer Block Capacity (10 Points):** Calculate the exact number of block pointers that can be stored inside a single $2\text{ KB}$ index block.

*Write your answer here:*

2. **File Size Capacity Limits (10 Points):** Calculate the maximum absolute file size capacity (in bytes) supported by this multi-level pointer layout.

*Write your answer here:*


---

### Question 4: Process Coordination — The Molecule Builder (20 Points)

*Write your answer here:*








---

### Question 5: Hardware Foundations — Data Interception Primitives (20 Points)

*Write your answer here:*


1. **DMA Ownership Handshake (10 Points):** Detail the chronological bus ownership handshake steps that occur between a CPU, a **Direct Memory Access (DMA) Controller**, and a high-bandwidth device controller during a disk sector read operation.

*Write your answer here:*

2. **Interrupt Handshake Sequence (10 Points):** Explain the operational difference between **Vectored Interrupts** and **Polling**. Which register in the CPU control map enables changing privilege domains from User Space to Kernel Space during an interrupt?

*Write your answer here:*


---