# Computer Architecture: Lecture 2 Notes

## 1. CMOS Transistor Rules

Circuits follow a strict division of labor between transistor types:

- **pMOS** is used to "pull up" the output to **1**
- **nMOS** is used to "pull down" the output to **0**

> **Why?** nMOS is poor at passing high voltages, and pMOS is poor at passing low voltages — so each type is only used where it performs best.

---

## 2. Power Consumption

Total power in a CMOS circuit is the sum of two components:

| Type              | Description                                           |
|-------------------|-------------------------------------------------------|
| **Dynamic Power** | Switching energy; heavily affected by voltage and frequency |
| **Static Power**  | Leakage current that occurs even when the circuit is idle   |

> **Total Power = Dynamic Power + Static Power**

---

## 3. Logic Minimization

Boolean algebra and **De Morgan's Law** (also called *bubble pushing*) are used to simplify logic circuits, resulting in:

- Reduced **physical area**
- Reduced **latency**

---

## 4. Canonical Forms

Two standard ways to express any logic function:

- **Sum of Products (SOP):** An OR of ANDs (minterms) — used when the output is **1**.
- **Product of Sums (POS):** An AND of ORs (maxterms) — used when the output is **0**.

---

## 5. Key Building Blocks

| Component | Function |
|-----------|----------|
| **Decoder** | Activates one specific output based on an input pattern; critical for memory addressing |
| **Multiplexer (Mux)** | Selects one of several data inputs based on a control signal |
| **Look-Up Table (LUT)** | Implements any logic function via a mux; the fundamental unit of FPGAs |

---

## 6. Arithmetic Circuits

- **Full Adder:** Adds three bits — A, B, and Carry-in — producing a Sum and Carry-out.
- **Ripple Carry Adder:** A simple chain of full adders where the carry travels **bit-by-bit** — functional but slow.
- **Carry Look-ahead Adder:** Uses advanced logic to calculate all carries **in parallel**, achieving much higher speed.

---

## 7. Programmable Logic (PLA)

A **Programmable Logic Array** is a flexible structure consisting of:

- An **AND array** followed by an **OR array**
- Can be programmed to implement **any sum-of-products (SOP)** logic function
- Offers a balance between flexibility and efficiency in hardware design
