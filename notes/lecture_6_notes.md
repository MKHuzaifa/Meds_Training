# Computer Architecture: Lecture 6 Notes

## 1. Combinational Circuit Timing

Digital signals are **not instantaneous** — physical resistance and capacitance (RC delay) cause every signal transition to take a finite amount of time.

### Two Key Delay Metrics

| Metric                          | Symbol    | Definition                                              |
|---------------------------------|-----------|---------------------------------------------------------|
| **Propagation Delay**           | $t_{pd}$  | **Maximum** time for an output to reach its stable final value |
| **Contamination Delay**         | $t_{cd}$  | **Minimum** time before an output *begins* to change   |

> **Analogy:** $t_{pd}$ is the *worst-case* arrival time; $t_{cd}$ is the *earliest possible* departure from the old value.

These two metrics together define the **window of uncertainty** during which an output is unreliable and must not be sampled.

---

## 2. Critical Path

The **longest combinational logic path** between any two registers in a circuit:

- Determines the **minimum achievable clock period** ($T_{min}$)
- Sets the **maximum safe operating frequency** ($f_{max} = 1 / T_{min}$)

```
f_max = 1 / (t_pcq + t_pd_critical + t_setup)

Where:
  t_pcq          = Clock-to-Q propagation delay of the source flip-flop
  t_pd_critical  = Propagation delay along the critical path
  t_setup        = Setup time of the destination flip-flop
```

> ⚠️ **Optimizing the critical path is the single most impactful way to increase a circuit's clock speed.**

---

## 3. Glitches

Temporary, incorrect output transitions caused by signals arriving at different times through different path lengths.

```
Input changes ──▶  Short path settles first ──▶  Output glitches to wrong value
                   Long path settles later  ──▶  Output corrects to final value
```

| Property          | Detail                                                              |
|-------------------|---------------------------------------------------------------------|
| **Cause**         | Unequal propagation delays across different logic paths             |
| **Effect**        | Momentary incorrect output; extra power consumed during transition  |
| **When ignored**  | Safe to ignore if the **steady-state** output is correct and the output is only sampled after $t_{pd}$ |
| **When critical** | Dangerous if the glitchy output directly drives an asynchronous control signal |

---

## 4. Sequential Timing Constraints

For a flip-flop to reliably capture data, two strict timing windows must be respected around every clock edge:

| Constraint         | Symbol      | Definition                                              | Violation Consequence |
|--------------------|-------------|---------------------------------------------------------|-----------------------|
| **Setup Time**     | $t_{setup}$ | Data must be **stable before** the clock edge arrives   | Metastability         |
| **Hold Time**      | $t_{hold}$  | Data must **remain stable after** the clock edge passes | Metastability         |

### Metastability
When setup or hold times are violated, the flip-flop enters a **metastable state**:
- Output oscillates unpredictably between 0 and 1
- Eventually resolves to a valid value, but the resolution time is **non-deterministic**
- Can propagate errors through the entire system

> ⚠️ Metastability is the **primary failure mode** of synchronous digital systems operating with asynchronous inputs (e.g., user buttons, inter-clock-domain signals).

---

## 5. Clock Skew

The **time difference** between the clock signal arriving at different registers across the chip.

```
Clock Source ──▶ Register A  (arrives at time t)
             └▶ Register B  (arrives at time t + skew)
```

- **Effect:** Reduces the time window available for useful combinational logic computation between two registers
- **Impact on timing:**

```
Corrected f_max = 1 / (t_pcq + t_pd_critical + t_setup + t_skew)
```

> Clock skew effectively **steals time** from logic — minimizing it is a key concern in high-speed chip layout and clock tree design.

---

## 6. Circuit Verification

> Hardware testing can consume up to **70% of total design time** — making verification a first-class engineering discipline, not an afterthought.

### Test Benches
Verilog modules written specifically to simulate and validate a **Device Under Test (DUT)**:
- Apply a comprehensive set of input stimuli over simulated time
- Monitor outputs and flag unexpected behavior
- Run entirely in simulation — **no hardware required**

### Golden Models
High-level behavioral models used as a reference standard for automated verification:

| Component         | Role                                                                 |
|-------------------|----------------------------------------------------------------------|
| **DUT**           | The actual hardware module being verified                           |
| **Golden Model**  | A simpler, high-level behavioral description of the correct output  |
| **Comparator**    | Automatically checks DUT output against the golden model output     |

```
Stimulus ──▶ DUT          ──▶ ┐
         └▶ Golden Model  ──▶ Comparator ──▶ PASS / FAIL
```

---

## 7. Design Principles

Two guiding rules for building high-performance hardware:

### Balance Logic Delays
Distribute combinational logic **evenly** across pipeline stages so no single stage becomes a bottleneck:
- An unbalanced design is bottlenecked by its slowest (longest) stage
- Re-partitioning logic to equalize delays raises the achievable clock frequency for the whole system

### Optimize for the Common Case
Focus performance improvements on the paths and operations that occur **most frequently**:
- A rare operation running slowly has little impact on average throughput
- A frequent operation running fast delivers outsized system-wide gains

> These two principles form the foundation of **performance-oriented digital design** and directly inform CPU microarchitecture decisions covered in later lectures.
