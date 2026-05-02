# Computer Architecture: Lecture 4 Notes

## 1. Finite State Machine (FSM) Design

### Problem Definition: Smart Traffic Light Controller
A real-world application of sequential logic involving:
- **Two avenues (A and B)** equipped with traffic sensors
- **Goal:** Maximize green light time for traffic-heavy roads while maintaining fairness between both directions

---

### Moore vs. Mealy Machines

| Feature          | Moore Machine                              | Mealy Machine                                      |
|------------------|--------------------------------------------|----------------------------------------------------|
| **Output depends on** | Current state only                    | Current state **and** external inputs              |
| **Compactness**  | Requires more states                       | Generally more compact                             |
| **Reliability**  | Stable outputs                             | Prone to **glitches** (inputs can bypass state register) |
| **Traffic Light** | ✅ Used here — light colors defined within each state | ❌ Not used |

---

### State Encoding Strategies

Different ways to assign binary values to states, each with trade-offs:

| Strategy              | Description                                              | Advantage                        | Cost                    |
|-----------------------|----------------------------------------------------------|----------------------------------|-------------------------|
| **Fully Encoded**     | Minimum bits used (e.g., 2 bits for 4 states)           | Fewest flip-flops                | More complex output logic |
| **One-Hot Encoding**  | Each state gets its own dedicated bit                    | Simpler output logic             | More flip-flops used    |
| **Output Encoding**   | Output values are used directly as state bits            | Eliminates output logic entirely | Less flexibility        |

---

## 2. FPGA Lab Overview

### What is an FPGA?
A **Field Programmable Gate Array** is a reconfigurable hardware platform consisting of:
- Thousands of **Look-Up Tables (LUTs)**
- Programmable **switches and interconnects**

> FPGAs allow algorithms to be implemented **directly in hardware** without the high cost and time of custom chip fabrication.

The lab uses the **[Basys 3 Artix-7 FPGA Trainer Board](https://digilent.com/shop/basys-3-artix-7-fpga-trainer-board-recommended-for-introductory-users/)** by Digilent.

---

### Lab Progression

Students build complexity incrementally:

1. **Simple comparison and addition circuits**
2. **More complex combinational and sequential modules**
3. **A fully functional 32-bit MIPS microprocessor** capable of running a *Snake* game

---

### Tools: Xilinx Vivado

Vivado automates two critical steps in the hardware design flow:

| Step                      | Description                                            |
|---------------------------|--------------------------------------------------------|
| **Synthesis**             | Converts HDL code into a netlist of logic gates        |
| **Placement & Routing**   | Maps the logic gates to physical resources on the FPGA |

---

## 3. Introduction to Verilog (HDL)

### Why Use an HDL?

Modern chips contain **billions of transistors** — manually drawing gates is completely infeasible. Hardware Description Languages (HDLs) like **Verilog** solve this by allowing designers to describe circuits in code.

Verilog supports two styles of description:

| Style                     | Description                                                             |
|---------------------------|-------------------------------------------------------------------------|
| **Structural**            | Explicitly specifies how gates and modules are connected                |
| **Behavioral**            | Describes *what* the circuit does; the compiler determines the gates    |

---

### Core Concepts

- **Modules:** The fundamental building block of Verilog — analogous to a *class* in object-oriented software. Every design is composed of interconnected modules.

- **Concurrency:** Unlike sequential languages (C++, Python), Verilog describes hardware where **everything executes simultaneously**, all synchronized by the **Clock signal**.

- **Vector Syntax:** Multi-bit signals are handled using bracket notation:
  ```verilog
  input  [31:0] a;   // 32-bit input bus
  output [31:0] y;   // 32-bit output bus
  ```
  This is essential for representing data buses and wide registers in a processor design.
