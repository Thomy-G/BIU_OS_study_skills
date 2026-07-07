# Operating Systems (89-231) — Comprehensive Practice Answers
## User Submission Template

Please fill in your answers in the sections below. Once completed, request the agent to grade/check this file.

---

### Part 1: Von Neumann Architecture, Introduction & Basic OS Structure
**Topic: Shell Command Types & Permissions**

1. **Shell Execution Mechanics:**
   
   *Write your answer here:*
   
2. **Access Control List Permissions:**
   - **Symbolic Representation:** 
     
   - **Owner Actions:** 
     
   - **Group Actions:** 
     
   - **Others Actions:** 
     

---

### Part 2: Processes
**Topic: Process Lifecycles & POSIX Fork Trees**

1. **Process Tree Diagram:**
   
   *Draw or describe your process tree here (e.g. using a Mermaid flowchart or text):*
   
2. **Terminal Output Sequence:**
   
   *Write the printed output lines here:*
   ```text
   
   ```
   *Explanation of scheduling factors:*
   

---

### Part 3: Threads
**Topic: Thread Model Resource Sharing & Blocking**

1. **Resource Sharing Behavior:**
   *Complete the table by writing **Unique** or **Shared** for each resource:*

| Resource / Context                   | Shared or Unique? |
| :----------------------------------- | :---------------- |
| Program Counter (PC) & CPU Registers |                   |
| User Stack                           |                   |
| Heap Memory                          |                   |
| Static/Global Variables              |                   |
| Open File Descriptors                |                   |

2. **Blocking Operations in Many-to-One ($M:1$) Models:**
   
   *Write your explanation here:*
   

---

### Part 4: Scheduling
**Topic: CPU Scheduling & Context Switches**

1. **Gantt Chart & Waiting Math:**
   - **First-Come, First-Served (FCFS):**
     - *Gantt Chart:*
```mermaid
gantt
    title Question 4.1: FCFS
    dateFormat X
    axisFormat %s
    
    section CPU core
    
```

     - *Turnaround Times (P1, P2, P3):*
     - *ATT:*
     - *Waiting Times (P1, P2, P3):*
     - *AWT:*

   - **Round Robin (RR, $q=3\text{ ms}$):**
     - *Gantt Chart:*

```mermaid
gantt
    title Question 4.1: Round Robin q=3ms
    dateFormat X
    axisFormat %s
    
    section CPU core
    
```

     - *Turnaround Times (P1, P2, P3):*
     - *ATT:*
     - *Waiting Times (P1, P2, P3):*
     - *AWT:*

2. **System Overhead Trade-offs:**
   
   *Write your explanation here:*
   

---

### Part 5: Deadlocks
**Topic: Banker's Matrix & Safety**

1. **Need Matrix Calculation:**
   *Fill in the Need Matrix values for each process:*
   
   $$\textbf{Need} = \begin{pmatrix} P_0 & \_ & \_ & \_ \\ P_1 & \_ & \_ & \_ \\ P_2 & \_ & \_ & \_ \\ P_3 & \_ & \_ & \_ \end{pmatrix}$$

2. **Safety State Evaluation:**
   
   *Write your safety analysis here. State if the system is Safe or Unsafe. Show the trace of Available resources and the execution sequence if safe, or prove deadlock capability if unsafe:*
   
3. **Dynamic Request Tracking:**
   
   *Write your answer here. Can the OS grant the request of $[1, 0, 1]$ to $P_1$? Show calculations:*
   

---

### Part 6: Main Memory
**Topic: Two-Level Paging & Effective Access Time**

1. **Address Bit Partitioning:**
   - **Outer Page Table Index bits:** 
   - **Inner Page Table Index bits:** 
   - **Page Offset bits:** 
   *Show address bit division breakdown calculation:*

2. **EAT Calculation:**
   
   *Write your calculations and the final required TLB Hit Ratio ($\alpha$) here:*
   

---

### Part 7: Virtual Memory
**Topic: Page Replacement & Belady's Anomaly**

1. **FIFO Trace & Anomaly Check:**
   - **FIFO with 3 Frames:**
     - *Show trace or faults:*
     - *Total Page Faults:*
   - **FIFO with 4 Frames:**
     - *Show trace or faults:*
     - *Total Page Faults:*
   - **Belady's Anomaly Status:** *Does it occur? Yes/No, and why:*

2. **LRU Frame Mapping:**
   - **LRU with 3 Frames:**
     - *Show trace or faults:*
     - *Total Page Faults:*
   - **Comparison with FIFO 3-frame trace:**
     *Write comparison here:*

---

### Part 8: File System Interface & Implementation
**Topic: Asymmetric Inode Capacity & C-SCAN Disk Head**

1. **Asymmetric Inode Sizing:**
   
   *Write your capacity calculations and final file size limits here:*
   
2. **Disk Scheduling Calculations:**
   
   *Show your C-SCAN track movement sequence and total seek distance calculation here:*
   
