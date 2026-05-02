# Computer Architecture: Lecture 3 Notes

## 1. Wrapping Up Combinational Logic

### Logical Completeness
Any logic function can be constructed using only:
- **{AND, OR, NOT}** gates, or
- A **single universal gate** type — either **NAND** or **NOR** alone

### Comparators
Circuits used to check **equality** between two bit-patterns:

> An equality checker is built from a series of **XNOR gates** feeding into a final **AND gate**.

### ALU (Arithmetic Logic Unit)
The **"core" of a processor**, responsible for both:

| Operation Type | Examples         |
|----------------|------------------|
| **Arithmetic** | Add, Subtract    |
| **Logic**      | AND, OR          |

- A **control signal (Opcode)** selects which function to execute using internal **multiplexers**.

### Tri-State Buffers
Specialized switches with **three possible output states**:

| State                | Description                                   |
|----------------------|-----------------------------------------------|
| **High (1)**         | Normal logic high                             |
| **Low (0)**          | Normal logic low                              |
| **High-Impedance (Z)** | "Disconnected" / floating — output is isolated |

> This allows multiple components to **share a single Bus** without causing short circuits.

---

## 2. The Need for Sequential Logic

**Combinational logic** has no memory — output depends *only* on the current input. **Sequential logic** introduces the ability to remember past states.

- **State:** A snapshot of all relevant system elements at a given moment in time.

### Memory Hierarchy

| Storage Element   | Speed    | Cost     |
|-------------------|----------|----------|
| Latches/Flip-Flops | Fastest | Most Expensive |
| DRAM              | Medium   | Medium   |
| Hard Drives       | Slowest  | Cheapest |

---

## 3. Basic Storage Elements: Latches

How a single bit is "captured" using feedback loops:

- **Cross-Coupled Inverters:** The simplest feedback loop — can store a 0 or 1, but provides **no way to change the stored value**.

- **RS Latch:** Adds **Reset (R)** and **Set (S)** inputs to allow writing.
  - ⚠️ **Forbidden State:** When S=0, R=0 (in a NAND latch), the output becomes unpredictable, leading to **metastability**.

- **Gated D Latch:** Resolves the RS latch problem by adding an **Enable** signal.
  - Output **Q** only updates when the Enable signal is **High**.
  - When Enable is **Low**, the stored value is held stable.

---

## 4. Synchronous Systems & The Clock

To prevent chaotic, unpredictable behavior from asynchronous transitions, modern computers use a **Clock**.

- **Clock Signal:** A periodic oscillating signal that **synchronizes all state changes** across the entire machine.
- **Metastability:** A dangerous condition where a circuit oscillates between 0 and 1 indefinitely — typically caused by:
  - Violating **timing constraints** (setup/hold times)
  - Using **forbidden input combinations**

---

## 5. D Flip-Flops: Edge-Triggered Storage

The most critical distinction in sequential logic:

| Feature              | Latch (Level-Triggered)              | D Flip-Flop (Edge-Triggered)              |
|----------------------|--------------------------------------|-------------------------------------------|
| **Trigger**          | Transparent while Enable is High     | Captures input only on the **rising clock edge** |
| **Problem**          | State can change multiple times per clock cycle | State is **stable** for the entire clock cycle |
| **Use in machines**  | Problematic for state machines       | Standard for **predictable computation** |

### How a D Flip-Flop Works
Built from **two latches in a Master-Slave configuration**:

1. The **Master latch** captures the input while the clock is Low.
2. On the **Positive (Rising) Edge** of the clock, the Master locks and the **Slave latch** transfers the value to output Q.
3. The output remains **stable** until the next rising edge — ensuring safe, predictable state transitions.
