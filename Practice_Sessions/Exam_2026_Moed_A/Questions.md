# Exam 1: Operating Systems – 2026 Moed Alef (Semester A)

**Exam Date:** February 19, 2026 
**Lecturer:** Dr. Marina Kogan-Sadetsky 
**TAs:** Doron Kunkul, Nadav Rotman Moshe 
**Allowed Material:** Standard Calculator (plus standard IDF reserve terms if applicable) 
**Duration:** 3 Hours 

---

## Part 1: True / False with Brief Justification (30 Points)

### Section A (5 Questions — 4 Points Each) 
For each statement, mark **True** or **False** and provide a brief technical justification.

1. **Context Switch TLB Invalidation**
   > **Statement:** When a context switch occurs between two separate processes, all entries inside the Translation Lookaside Buffer (TLB) are marked as invalid (i.e., they can no longer be used).
   
* **Answer:** `_____`
* **Justification:**


2. **File Descriptors during `exec()`**
   > **Statement:** The `exec()` system call family automatically closes all open file descriptors belonging to the process in which it is executed.
   
* **Answer:** `_____`
* **Justification:**


3. **Simultaneous Thread States**
   > **Statement:** Given a single process $P$ containing 4 threads, it is possible for the threads to simultaneously exist in the following distinct states: one thread is **RUNNING**, one is **READY**, one is **BLOCKED**, and one is **SUSPENDED**.
   
* **Answer:** `_____`
* **Justification:**


4. **Terminal `kill` Interrupts**
   > **Statement:** Any active process can potentially have its execution terminated if an external user transmits `signal 4` via a standard terminal command interface.
   
* **Answer:** `_____`
* **Justification:**


5. **Linux CFS `vruntime` Calculations**
   > **Statement:** In the Linux operating system scheduler, the `vruntime` metric tracks a process's actual runtime execution and is scaled according to its consumed time quantum and the process priority (`nice` level).
   
* **Answer:** `_____`
* **Justification:**


---

### Section B (5 Questions — 2 Points Each) 
The following statements specifically relate to the mechanics of **processes**. Mark **True** or **False** and provide a brief technical justification.

1. **Zombie Process Lifecycles**
   > **Statement:** A *Zombie* process describes a state where a process has completely finished its execution path, but its Process Control Block (PCB) remains allocated in the OS process table because its parent process has not yet reaped it using a `wait()` or `waitpid()` call.
   
* **Answer:** `_____`
* **Justification:**


2. **Signal Masking Restrictions**
   > **Statement:** A custom software routine cannot assign a custom `signal handler` to capture or intercept `SIGKILL` or `SIGSTOP`, but a process *can* successfully intercept and block these specific signals from being processed by configuring an explicit bitwise signal mask.
   
* **Answer:** `_____`
* **Justification:**


3. **Standard `kill()` System Call Target**
   > **Statement:** The standard POSIX `kill(pid, sig)` system function inherently transmits a `SIGKILL` signal directly to the specific target process matching the inputted `pid` parameter.
   
* **Answer:** `_____`
* **Justification:**


4. **Top-Level Virtual Page Directory Handlers**
   > **Statement:** A process's PCB must store a specific internal pointer referencing its top-level Virtual Page Table Directory because, without this address, the OS cannot safely resume the process's execution context upon completing a standard context switch back into its runtime state.
   
* **Answer:** `_____`
* **Justification:**


5. **Multithreaded Asynchronous Signal Handling**
   > **Statement:** An asynchronous system signal directed at a multi-threaded process can be handled by any active internal thread belonging to that process, provided that specific thread is not actively masking/blocking the signal type.
   
* **Answer:** `_____`
* **Justification:**


---

## Part 2: Open Analysis & Architecture (20 Points) 

### Section A: Instruction Fault Tracing (5 Points) 
Provide a concrete example of a single, valid **x86-64 Assembly instruction** that could hypothetically trigger the absolute *maximum* possible number of **Page Faults** during its individual execution phase on a Linux system.

* **Your Answer:**

* **Detailed Architectural Justification:**

---

### Section B: CPU-Bound Process Mechanics (5 Points) 
1. Clearly define what constitutes a **CPU-bound process**.
2. Explain how a modern operating system dynamically discovers/classifies these types of runtime jobs.
3. Detail how the kernel scheduler utilizes this classification to maximize overall system utility and efficiency.

* **Your Answer:**


---

### Section C: Directory Inode Disks (5 Points) 
Assume a filesystem directory is named `X`. Select **all** options from the list below that will directly cause the actual data payload blocks representing directory `X` to be updated and re-written:

* [ ] **a.** Creating a brand new file inside directory `X`.
* [ ] **b.** Creating a brand new sub-directory inside directory `X`.
* [ ] **c.** Renaming an existing file currently located within directory `X`.
* [ ] **d.** Renaming directory `X` itself to a new name (e.g., changing its name to `Y`).
* [ ] **e.** Adding a new hard link inside a completely different directory that references an existing file inside directory `X`.

---

### Section D: Core UNIX Decay Schedulers (5 Points) 
A historical UNIX system runs the following kernel automation procedures:
* **On every clock tick** (~10 ms): Increments the CPU utilization counter (`CPU usage counter`) for whichever process is currently in the `RUNNING` state.
* **Once per second:** Decays the execution record and re-calculates priorities for *all* active processes using the following algorithms:

$$\text{CPU usage counter} \leftarrow \frac{\text{CPU usage counter}}{2}$$
$$\text{Process Priority} \leftarrow \text{Base Priority} + \text{CPU usage counter} + \text{Nice Value}$$

**Question:** Explain the deep architectural meaning of these updates and describe what scheduling goals they are trying to enforce.

* **Your Answer:**


---

## Part 3: Filesystems & Virtualization (20 Points) 

### Section A: Link Allocation Tracking (10 Points) 
A system administrator runs the following sequential operations within an empty working directory named `OS`:

```bash
root@cs217230:~/OS# ls
root@cs217230:~/OS# touch a.txt
root@cs217230:~/OS# ln -s a.txt b.txt
root@cs217230:~/OS# ln b.txt c.txt
```

1. **How many files** exist inside the `OS` directory after these commands finish execution? 
2. For **each** file identified, what is the exact value of its internal hard-link reference counter (`reference counter`) inside its respective `i-node` structural block? 

* **Your Answer:**


---

### Section B: Hypervisor Classifications (10 Points) 
Explain the specific operational role that a **Hypervisor** performs in hosting, dividing, and isolating resources for virtual hardware machines ($VMs$).

* **Your Answer:**


---

## Part 4: Code Tracing & Output Evaluation (10 Points) 

### Section A: Nested Fork Execution Paths (5 Points) 
Analyze the following POSIX C program structure and determine its exact printed console output sequence. Assume all operations execute without errors.

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

void main() { 
    int i = 1;
    while ((i % 3) != 0) {
        printf("%d\n", i++); 
        int id = fork();
        if (id != 0) {
            wait(NULL);
        }
        printf("%d\n", i);
    }
}
```

* **Your Output Answer:**

---

### Section B: Asynchronous Signals & Deadlocks (5 Points) 
Analyze the following concurrent signal communication program. Determine the complete printed runtime sequence or explain if a stall/deadlock condition is reached.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>

int user_interrupt = 0;

void signal_handler(int signal){
    user_interrupt = 1;
}

void child_function(){
    printf("Do you wanna build a snowmen? %d\n", getpid());
    kill(getpid(), SIGUSR1);
    puts("ok bye..\n");
    exit(1);
}

void main() {
    struct sigaction sa;
    sa.sa_handler = signal_handler;
    sigemptyset(&sa.sa_mask);
    sa.sa_flags = 0;
    sigaction(SIGUSR1, &sa, NULL);

    if(fork() == 0)
        child_function();

    while(!user_interrupt);
    puts("get back to the basement demon child!\n");
}
```

* **Your Answer & Explanation:** 
A developer launches exactly $K$ separate concurrent system threads that run in parallel, all executing this function `func()`.

**Question:** What is the absolute **minimum initial integer value** that must be assigned to counting semaphore $S$ to guarantee that this multi-threaded system will *never* enter a deadlock condition? 

* **Your Answer & Derivation:**
