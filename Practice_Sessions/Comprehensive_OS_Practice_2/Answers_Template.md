# Operating Systems (89-231) — Comprehensive Practice Answers 2
## User Submission Template

Please fill in your answers in the sections below. Once completed, request the agent to grade/check this file.

---

### Question 1: Linux Systems Programming — Signals, SUID & POSIX C Code

1. **Signal Masking & Execution Trace:**
   - **Signal Mask Inheritance Explanation:**
     *Write your explanation here:*
     
   - **Terminal Output Sequence:**
     *Write the exact printed output sequence here:*
     ```text
     
     ```
   - **Race Conditions & Scheduling Factors:**
     *Write your explanation here:*
     

2. **Privilege Separation & SUID:**
   - **Symbolic Representation of `4751`:**
     *Write symbolic representation here:*
     
   - **RUID & EUID Values:**
     *Write the values of RUID and EUID for the process here:*
     
   - **Purpose of Privilege Separation (SUID):**
     *Write your explanation here:*
     

---

### Question 2: Thread Concurrency & Synchronization — Dining Philosophers & Race Conditions

1. **Dining Philosophers Gatekeeper:**
   - **Deadlock-Free Range of $k$ & Proof:**
     *Write the range of $k$ and your deadlock-freedom proof here:*
     
   - **Worst-Case eating concurrency for $k = 4$:**
     *Write your analysis and maximum eating concurrency here:*
     

2. **Race Condition Tracing:**
   - **All possible final values of `g`:**
     *Write list of values here:*
     
   - **Step-by-step Interleaving Trace for Minimum Value:**
     *Show your assembly-level trace here:*
     

---

### Question 3: CPU Scheduling — Preemptive SJF with I/O & Processor Affinity

1. **Preemptive SJF with CPU & I/O Bursts:**
   - **CPU & I/O Gantt Charts:**
     *Draw or describe your CPU/IO Gantt charts here (you can use text or Mermaid):*
     
   - **CPU Utilization:**
     *Write CPU utilization calculation and result here:*
     
   - **Average Waiting Time (AWT):**
     *Write AWT calculations and results here:*
     

2. **Processor Affinity Mechanics:**
   - **Definitions of Soft vs. Hard Affinity:**
     *Write definitions here:*
     
   - **Why OS Prefers Affinity:**
     *Write explanation here:*
     

---

### Question 4: Memory Management — TLB Upgrades & Working Set

1. **TLB vs. RAM Upgrade Trade-off:**
   - **EAT Calculation for Option A (Faster RAM):**
     *Write calculations and final EAT here:*
     
   - **EAT Calculation for Option B (Better TLB):**
     *Write calculations and final EAT here:*
     
   - **Performance Recommendation:**
     *Write your choice and explanation here:*
     

2. **Working Set Tracking:**
   - **Working Set $W(t_8)$:**
     *Write the set here:*
     
   - **Working Set Size Trace ($t_1 \dots t_{12}$):**
     
| Timestamp | Page Ref | Active Working Set | Working Set Size |
| :--- | :--- | :--- | :--- |
| $t_1$ | $d$ | | |
| $t_2$ | $d$ | | |
| $t_3$ | $a$ | | |
| $t_4$ | $c$ | | |
| $t_5$ | $c$ | | |
| $t_6$ | $b$ | | |
| $t_7$ | $d$ | | |
| $t_8$ | $a$ | | |
| $t_9$ | $c$ | | |
| $t_{10}$ | $b$ | | |
| $t_{11}$ | $d$ | | |
| $t_{12}$ | $d$ | | |

   - **Average Working Set Size:**
     *Write calculation and result here:*
     

---

### Question 5: File Systems & Mass Storage — Linked Allocation & SCAN/LOOK

1. **Linked Allocation Disk I/O Count:**
   - **Sector Read/Write Count:**
     *Write the final I/O operation counts here:*
     
   - **Step-by-step Disk Operation Explanations:**
     *Write your step-by-step breakdown of disk reads and writes here:*
     

2. **SCAN vs. LOOK Disk head calculations:**
   - **SCAN Seek Distance Calculation:**
     *Write track traversal sequence and final seek distance (in tracks) here:*
     
   - **LOOK Seek Distance Calculation:**
     *Write track traversal sequence and final seek distance (in tracks) here:*
     
