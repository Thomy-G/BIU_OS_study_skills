# 📝 EXAM BLUEPRINT #2



### Question 1: System Architectures & Storage Scheduling (20 Points)

*Write your answer here:*


1. **Hardware Handshake (10 Points):** Describe the register tracking mechanism between the **Memory Address Register (MAR)** and **Memory Data Register (MDR)** when a CPU executes an asymmetric memory read pipeline under the Von Neumann architecture framework.

*Write your answer here:*

2. **Mass Storage Optimization (10 Points):** A storage cylinder head is positioned at track index **$100$**. The pending file access request queue is ordered as: $23, 180, 45, 122, 10, 150$. Calculate the total mechanical movement delta (in cylinders) using **C-SCAN (Circular SCAN)** moving towards higher index tracks (up to track 199, then wrapping down to 0 at no cost).

*Write your answer here:*


---

### Question 2: Deadlock Avoidance — The Banker's Matrix (20 Points)

*Write your answer here:*





1. **State Safety Evaluation (10 Points):** Compute the current **Need Matrix** and determine whether the system resides in a Safe State. Prove your position using a generated execution trace.

*Write your answer here:*

2. **Dynamic Request Verification (10 Points):** If process $P_0$ issues an immediate resource request allocation vector $\textbf{Request}_{P_0} = [1, 0, 1]$, can the operating system fulfill it immediately? Prove using the full Banker's structural equations.

*Write your answer here:*


---

### Question 3: Memory Layouts & Page Replacement Traces (20 Points)

*Write your answer here:*




1. **FIFO Evaluation & Anomaly Validation (10 Points):** Map the reference string through a **First-In, First-Out (FIFO)** replacement tracking register setup using:

*Write your answer here:*

2. **LRU Allocation Mapping (10 Points):** Map the identical page request allocation timeline assuming a **Least Recently Used (LRU)** cache swap strategy with 3 frames. Compare the fault total against the FIFO 3-frame footprint.

*Write your answer here:*


---

### Question 4: Linux Environment Scripting & SUID Mechanics (20 Points)

*Write your answer here:*


1. **SUID Variable Analysis (10 Points):** Explain the difference between a process's **Real User ID (RUID)** and its **Effective User ID (EUID)**. How does setting the SUID privilege bit on a compiled executable modify this relationship at runtime?

*Write your answer here:*

2. **Bash Array Loop Processing (10 Points):** Analyze the following Bash shell script block:

*Write your answer here:*


#!/bin/bash




---

### Question 5: POSIX Advanced Systems Code Tracking (20 Points)

*Write your answer here:*



#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/wait.h>

    

1. **File Descriptor Duplication Analysis (10 Points):** Explain what happens to standard output (`stdout`) inside the child process after executing `dup2(fd, 1)`. Where does `printf` write its data payload?

*Write your answer here:*

2. **File Persistence Assessment (10 Points):** After this program completes its execution sequence entirely, what will be the exact text content stored inside `sync_test.txt`? Trace the file pointer offset behavior.

*Write your answer here:*


---