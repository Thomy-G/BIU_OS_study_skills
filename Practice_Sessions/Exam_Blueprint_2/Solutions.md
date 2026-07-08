## Part B: Official Solution Manual
### Solution 1: System Architectures & Storage Scheduling

1. **MAR-MDR Hardware Handshake:**
   * The CPU places the memory address it needs to read into the **Memory Address Register (MAR)** via the internal bus.
   * The MAR asserts the target address onto the physical address bus.
   * The memory controller retrieves the byte array from physical RAM and places it onto the data bus.
   * The data moves directly into the **Memory Data Register (MDR)**, where the CPU can then read it into internal registers.
2. **C-SCAN Cylinder Trace Calculation:**
   * Queue sorted: $10, 23, 45, 122, 150, 180$.
   * Path: $100 \rightarrow 122 \rightarrow 150 \rightarrow 180 \rightarrow 199 \ (\text{end}) \rightarrow \text{wrap to } 0 \rightarrow 10 \rightarrow 23 \rightarrow 45$.
   * $\text{Seek Delta Step 1 (Upward)} = 199 - 100 = 99\text{ cylinders}$.
   * $\text{Seek Delta Step 2 (Downward Jump)} = 0\text{ cylinders}$ (per instructions).
   * $\text{Seek Delta Step 3 (Post-Wrap upward)} = 45 - 0 = 45\text{ cylinders}$.
   * $\text{Total Seek Movements} = 99 + 45 = \mathbf{144\text{ cylinders}}$.

### Solution 2: Deadlock Avoidance — The Banker's Matrix

1. **Safe Sequence Calculations:**
   * $\sum \textbf{Allocation} = [1+2+1, \,\, 2+0+1, \,\, 1+1+0] = [4, 3, 2]$.
   * Initial Available Vector: $\textbf{Available} = \textbf{Total} - \sum \textbf{Allocation} = [6, 4, 4] - [4, 3, 2] = \mathbf{[2, 1, 2]}$.
   * $\textbf{Need Matrix} = \textbf{Max} - \textbf{Allocation}$:
     $$\textbf{Need} = \begin{pmatrix} P_0 & 1 & 1 & 1 \\ P_1 & 2 & 2 & 1 \\ P_2 & 0 & 0 & 1 \end{pmatrix}$$
   * **Safety Vector Search:**
     * $P_2$'s $\textbf{Need} = [0, 0, 1] \le \textbf{Available } [2, 1, 2] \rightarrow \mathbf{Run \ P_2}$.
     * $\textbf{Available}_{\text{new}} = [2, 1, 2] + [1, 1, 0] = \mathbf{[3, 2, 2]}$.
     * $P_0$'s $\textbf{Need} = [1, 1, 1] \le \textbf{Available } [3, 2, 2] \rightarrow \mathbf{Run \ P_0}$.
     * $\textbf{Available}_{\text{new}} = [3, 2, 2] + [1, 2, 1] = \mathbf{[4, 4, 3]}$.
     * $P_1$'s $\textbf{Need} = [2, 2, 1] \le \textbf{Available } [4, 4, 3] \rightarrow \mathbf{Run \ P_1}$.
   * System is **Safe**. Valid safe sequence is: **$\langle P_2, P_0, P_1 \rangle$**.
2. **Dynamic Request Tracking:**
   * $\textbf{Request}_{P_0} = [1, 0, 1]$. Check 1: Is $\textbf{Request} \le \textbf{Need}_{P_0}$ ($[1,1,1]$)? **Yes.**
   * Check 2: Is $\textbf{Request} \le \textbf{Available}$ ($[2,1,2]$)? **Yes.**
   * Speculative state mutation parameters:
     * $\textbf{Available} = [2, 1, 2] - [1, 0, 1] = [1, 1, 1]$.
     * $\textbf{Allocation}_{P_0} = [1, 2, 1] + [1, 0, 1] = [2, 2, 2]$.
     * $\textbf{Need}_{P_0} = [1, 1, 1] - [1, 0, 1] = [0, 1, 0]$.
   * **Speculative Safety Check:**
     * $P_2$'s $\textbf{Need } [0, 0, 1] \le \textbf{Available } [1, 1, 1] \rightarrow \mathbf{Run \ P_2}$.
     * $\textbf{Available} = [1, 1, 1] + [1, 1, 0] = \mathbf{[2, 2, 1]}$.
     * $P_0$'s $\textbf{Need } [0, 1, 0] \le \textbf{Available } [2, 2, 1] \rightarrow \mathbf{Run \ P_0}$.
     * $\textbf{Available} = [2, 2, 1] + [2, 2, 2] = \mathbf{[4, 4, 3]}$.
     * $P_1$'s $\textbf{Need } [2, 2, 1] \le \textbf{Available } [4, 4, 3] \rightarrow \mathbf{Run \ P_1}$.
   * Since a safe sequence still exists ($\langle P_2, P_0, P_1 \rangle$), **the allocation can be granted immediately**.

### Solution 3: Memory Layouts & Page Replacement Traces

1. **FIFO 3-Frame vs 4-Frame Evaluation Trace:**
   * **3 Frames FIFO:**
     * Fault Trace: F(2), F(2,3), F(2,3,4), Hit(2,3,4), Hit(2,3,4), F(5,3,4), F(5,2,4), F(5,2,4)-Hit, Hit(5,2,4), F(5,3,4), F(2,3,4), Hit(2,3,4).
     * **Total Faults = 7**.
   * **4 Frames FIFO:**
     * Fault Trace: F(2), F(2,3), F(2,3,4), Hit, Hit, F(2,3,4,5), Hit, Hit, Hit, F(3,4,5,2), F(4,5,2,3), F(5,2,3,4).
     * **Total Faults = 7**.
   * **Belady's Anomaly Status:** **No.** Fault footprints remained identical (7 faults). The anomaly is not observed here because the page fault count did not *increase*.
2. **LRU 3-Frame Cache Mapping Trace:**
   * Frame Step History: F[2], F[2,3], F[2,3,4], H[3,4,2], H[4,2,3], F[2,3,5], H[3,5,2], F[5,2,4], H[2,4,5], F[4,5,3], F[5,3,2], F[3,2,4].
   * **Total LRU Page Faults = 9**. (In this specific edge case reference string, FIFO actually outperformed LRU due to cyclic re-referencing patterns).

### Solution 4: Linux Environment Scripting & SUID Mechanics

1. **RUID vs EUID Realities:**
   * **Real User ID (RUID):** Identifies the actual user who launched the process (inherited from the parent shell).
   * **Effective User ID (EUID):** Dictates the actual access rights and security privileges the process currently holds when interacting with the kernel file tables.
   * Setting the **SUID bit** forces the kernel to set the process's **EUID** to match the credentials of the file's *owner* (e.g., Root) rather than the RUID of the caller.
2. **Bash Script Expansion Outputs:**
   * **Loop A (`[*]` Stringification Expansion):** Merges the array elements using the first character of `IFS` (`:`). This collapses the entries into a single string token: `"alpha:one:beta:two"`. Loop A runs exactly once.
   * **Loop B (`[@]` Tokenized Expansion):** Evaluates each element independently while respecting original quote boundaries. It expands to two distinct tokens: `"alpha:one"` and `"beta:two"`.
   ```text
   --- LOOP A ---
   Value: alpha:one:beta:two
   --- LOOP B ---
   Value: alpha:one
   Value: beta:two
   ```

### Solution 5: POSIX Advanced Systems Code Tracking

1. **File Descriptor Duplication Behavior:**
   Executing `dup2(fd, 1)` closes standard output (descriptor slot 1) and redirects slot 1 to point directly to the open file entry for `sync_test.txt`. Any subsequent calls to `printf` write their data payloads directly to the disk file instead of the terminal display.
2. **File Persistence Contents:**
   Because both processes share the *same* underlying open file description across the `fork()` boundary, they share a single, unified file offset cursor.
   * Child runs first (enforced by parent `wait(NULL)`), writes `"Line Alpha\n"`, and increments the cursor.
   * Parent unblocks, resumes at that exact shifted cursor position, and appends `"Line Beta\n"`.
   * **Final File Content:**
     ```text
     Line Alpha
     Line Beta
     ```