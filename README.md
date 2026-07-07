# Bar-Ilan University — Operating Systems (89-231)
## Interactive Exam Practice Simulator for Antigravity

This repository contains a custom agentic **Skill** designed for the **Antigravity IDE** to run interactive, realistic exam practice simulations. The system is custom-tailored to mimic the style, grading severity, and curriculum guidelines of the Bar-Ilan University Operating Systems course.

---

## 🌟 Key Features
- **8 Core Curriculum Topics**:
  1. **Von Neumann & Basic OS Structure**: Shell execution mechanics (built-ins vs. external commands) and Access Control List (ACL) symbolic permissions.
  2. **Processes**: POSIX multi-process trees (`fork()`), execution flow tracing, parent-child synchronization (`wait()`), and signal masking.
  3. **Threads**: Thread models ($M:1$, $1:1$, $M:N$), resource sharing behavior (PC, Registers, Stacks, Heap, File Descriptors), and blocking call analysis.
  4. **CPU Scheduling**: Computational math for average waiting time (AWT) and average turnaround time (ATT) under FCFS, SJF, and Round Robin (RR), factoring in context-switch overhead.
  5. **Deadlocks**: Banker's Algorithm safety state evaluations, Need Matrix computations, and dynamic resource request tracking.
  6. **Main Memory**: Address bit partitioning for Multi-Level Paging, TLB lookup optimization, and Effective Access Time (EAT) equations.
  7. **Virtual Memory**: Page replacement simulations (FIFO, LRU, Optimal) and evaluating/explaining Belady's Anomaly.
  8. **File Systems & Storage**: Asymmetric Inode capacities (direct, single/double/triple indirect pointers) and C-SCAN/SCAN disk head seek scheduling.
- **Past Exam Vault**: Pre-loaded with official blueprints and subject guides:
  - `Exam_Blueprint_1.md`
  - `Exam_Blueprint_2.md`
  - `Exam_Blueprint_3.md`
  - `OS_Exam_Questions_by_Subject.md`
  - `Past_Exams/Past exams` file
- **Structured Practice Sessions**: Automatically generates clean directory files for questions, user templates, and detailed solutions.

---

## ⚙️ How to Install in Antigravity

Since Antigravity automatically detects workspace skills, installing this is as simple as placing the `.agents` folder in your workspace root:

### Step 1: Clone or Copy the Repository
Clone this repository directly, or copy the `.agents` folder into the root of your active project workspace:
```bash
git clone https://github.com/Thomy-G/BIU_OS_study_skills.git
```
Your workspace folder should look like this:
```
BIU_OS_study_skills/
├── .agents/
│   └── skills/
│       └── biu-os-exam-practice/
│           ├── SKILL.md
│           └── references/
│               ├── Exam_Blueprint_1.md
│               ├── Exam_Blueprint_2.md
│               ├── Exam_Blueprint_3.md
│               └── OS_Exam_Questions_by_Subject.md
├── Past_Exams/
│   └── Past exams
├── Practice_Sessions/
└── README.md
```

### Step 2: Trigger the Exam Practice
Open the workspace in your Antigravity IDE. Once loaded, open a chat session with your AI Coding Assistant and say:
> *"Let's start the exam simulation"* or *"Test me using the biu-os-exam-practice skill."*

---

## 📝 Practice Session Workflow
When you start a session, the simulator will:
1. Initialize a subfolder under `Practice_Sessions/` for the current topic (e.g. `Practice_Sessions/Comprehensive_OS_Practice/`).
2. Write a question spec (`Questions.md`) and an answer template (`Answers_Template.md`).
3. You can write your responses in the template file.
4. When you are ready, save the file and say **"Review this"** or **"Check my answers"**.
5. The agent will grade your submission strictly (like a university grader) and write a comprehensive analysis and correct solutions into a solution file (`Solutions.md`).
