# Computer Architecture: Lecture 1 Notes

## 1. The Transformation Hierarchy

The lecture introduces the concept that computing is a multi-layered translation process:

> **Problem → Algorithm → Program → OS → ISA → Microarchitecture → Logic → Devices → Electrons**

**Key Insight:** Highest efficiency is achieved through **"cross-layer design"** — optimizing hardware and software together rather than in isolation.

---

## 2. Transistors as Digital Switches

Modern computing relies on **MOSFETs** (Metal-Oxide-Semiconductor Field-Effect Transistors) acting as simple voltage-controlled switches:

| Type  | Conducts (ON) When Gate Receives |
|-------|----------------------------------|
| nMOS  | High (1) signal                  |
| pMOS  | Low (0) signal                   |

---

## 3. CMOS Logic & Gate Design

**Complementary Metal-Oxide-Semiconductor (CMOS)** technology uses both nMOS and pMOS to create reliable logic gates:

- **NOT Gate (Inverter):** The most basic circuit; uses one pMOS to "pull up" the output and one nMOS to "pull down".
- **NAND/NOR Gates:** These are the "universal" building blocks of hardware. In CMOS, a NAND gate is physically simpler and more efficient to build than an AND gate.
