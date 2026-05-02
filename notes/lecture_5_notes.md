# Computer Architecture: Lecture 5 Notes

## 1. Advanced Verilog Syntax

Efficient manipulation of multi-bit signals using three core techniques:

| Technique         | Description                                              | Example                        |
|-------------------|----------------------------------------------------------|--------------------------------|
| **Slicing**       | Extracting a subset of bits from a vector                | `a[7:4]` → upper nibble of `a` |
| **Concatenation** | Joining multiple signals into one wider signal           | `{a, b}` → combines `a` and `b`|
| **Duplication**   | Repeating a signal a fixed number of times               | `{4{a[0]}}` → repeats LSB 4 times |

> These operations are essential for managing complex **data buses** and performing efficient **bit-level operations** without extra logic.

---

## 2. Modeling Styles

Verilog offers two fundamental approaches to describing hardware:

| Style            | Description                                              | Analogy                        |
|------------------|----------------------------------------------------------|--------------------------------|
| **Structural**   | Explicitly wires together gates and sub-modules          | A circuit schematic in code    |
| **Behavioral**   | Describes high-level *what* the circuit does             | Pseudocode that compiles to gates |

> Most real designs **combine both styles** — behavioral for complex logic, structural for precise control over critical paths.

---

## 3. Sequential Logic in Verilog

### `always` Blocks
Used to describe circuit behavior that responds to signal changes, particularly the clock:

```verilog
always @(posedge clk) begin
    Q <= D;   // Capture D on the rising clock edge
end
```

### Non-Blocking Assignments (`<=`)
The **most critical rule** for modeling sequential hardware:

| Assignment Type  | Symbol | Use Case                                   | Behavior                        |
|------------------|--------|--------------------------------------------|---------------------------------|
| **Blocking**     | `=`    | Combinational logic (`always @(*)`)        | Executes line-by-line           |
| **Non-Blocking** | `<=`   | Sequential logic (`always @(posedge clk)`) | All right-hand sides evaluated simultaneously, then assigned |

> ⚠️ **Using blocking assignments (`=`) inside a clocked `always` block is a common and dangerous mistake** — it creates unintended dependencies between statements, failing to model true parallel hardware behavior.

---

## 4. FSM Design in Verilog

A Finite State Machine is cleanly partitioned into **three separate logical blocks**:

```
         ┌─────────────────┐
Inputs──▶│  Next-State     │──▶┌───────────────┐
         │  Logic          │   │ State Register │──▶ Current State
         │  (Combinational)│◀──│  (Flip-Flops)  │
         └─────────────────┘   └───────────────┘
                                       │
                               ┌───────▼───────┐
                               │ Output Logic  │──▶ Outputs
                               │ (Combinational)│
                               └───────────────┘
```

| Block                      | Type          | Responsibility                              |
|----------------------------|---------------|---------------------------------------------|
| **State Register**         | Sequential    | Stores the current state using flip-flops   |
| **Next-State Logic**       | Combinational | Computes the next state from current state + inputs |
| **Output Logic**           | Combinational | Generates outputs from the current state (Moore) or state + inputs (Mealy) |

---

## 5. Parameterization

Creating **reusable, scalable hardware modules** by defining configurable parameters:

```verilog
module adder #(parameter WIDTH = 8)
              (input  [WIDTH-1:0] a, b,
               output [WIDTH-1:0] sum);
    assign sum = a + b;
endmodule

// Instantiated as a 32-bit adder:
adder #(32) my_adder(.a(x), .b(y), .sum(z));
```

> Parameters like **bus widths** can be overridden at instantiation time — the same module definition scales from an 8-bit to a 32-bit adder without rewriting any logic.

---

## 6. Timing Fundamentals

A design that is **logically correct** can still **fail physically** due to real-world signal delay constraints:

| Timing Concept        | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| **Propagation Delay** | Time for a signal to travel through a gate or wire                         |
| **Setup Time**        | Input must be stable *before* the clock edge arrives                       |
| **Hold Time**         | Input must remain stable *after* the clock edge passes                     |

> ⚠️ Violating **setup** or **hold** times causes **metastability** — the flip-flop output becomes unpredictable, potentially crashing the system.

**Critical Path:** The longest combinational path between two registers — it determines the **maximum safe clock frequency** of the design.

---

## 7. Verification via Test Benches

Simulation is essential before committing a design to hardware synthesis or physical implementation.

### What is a Test Bench?
A separate Verilog module that:
- **Instantiates** the design under test (DUT)
- **Applies stimulus** (input vectors) over simulated time
- **Monitors and checks** outputs against expected values

```verilog
module tb_adder;
    reg  [7:0] a, b;
    wire [7:0] sum;

    adder dut(.a(a), .b(b), .sum(sum));  // Instantiate the DUT

    initial begin
        a = 8'd10; b = 8'd5;   // Apply inputs
        #10;                    // Wait 10 time units
        $display("Sum = %d", sum);  // Check output
        $finish;
    end
endmodule
```

> **Simulate first, synthesize second.** Catching bugs in simulation is far cheaper than discovering them after fabrication or FPGA programming.
