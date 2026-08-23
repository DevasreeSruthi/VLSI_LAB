# Module 4 — GLS, Blocking vs Non-Blocking Assignments & Synthesis-Simulation Mismatch

## 1. What is GLS?

**GLS (Gate-Level Simulation)** is the process of running the testbench against the **synthesized gate-level netlist** instead of the original RTL design.

### Basic Idea

```text
RTL Design + Testbench
          ↓
    RTL Simulation
```

After synthesis:

```text
Gate-Level Netlist + Same Testbench
          ↓
    Gate-Level Simulation (GLS)
```

The purpose of GLS is to verify that the synthesized implementation behaves as expected.

### Important Points

- The **testbench remains the same**.
- Only the design being simulated changes.
- RTL simulation checks the RTL implementation.
- GLS checks the synthesized gate-level implementation.

---

## 2. Why is GLS Required?

GLS is performed to verify the correctness of the design **after synthesis**.

### Main Purposes of GLS

- Verify the logical correctness of the synthesized design.
- Ensure that synthesis has not changed the intended functionality.
- Detect **synthesis-simulation mismatches**.
- Verify the synthesized gate-level implementation.
- Validate timing when delay information is available.

For timing validation, GLS needs to be run with **delay-annotated gate-level models**.

```text
Gate-Level Netlist
        +
Delay Annotation
        ↓
Timing-Aware GLS
```

---

## 3. RTL vs Gate-Level Netlist

### RTL

RTL describes the functionality of a circuit using constructs such as:

- `always`
- `assign`
- `if`
- `case`
- Logical operators
- Arithmetic operators

Example:

```verilog
assign y = (a & b) | c;
```

### Gate-Level Netlist

After synthesis, the RTL is converted into gates/cells.

```text
a ───┐
     AND ───┐
b ───┘      │
            OR ─── y
c ──────────┘
```

The gate-level netlist contains actual gates or standard-cell instances rather than high-level RTL descriptions.

---

## 4. GLS Using IVerilog

The basic GLS flow using **IVerilog** is:

```text
             RTL Design
                 ↓
              Synthesis
                 ↓
        Gate-Level Netlist
                 ↓
               IVerilog
                 ↑
             Testbench
                 ↓
                VCD
                 ↓
             GTKWave
```
<img width="1268" height="649" alt="image" src="https://github.com/user-attachments/assets/44744d90-eaa8-4be4-89ea-2ceeb5e68989" />

### Flow

```text
Design
  │
  ├──────────────┐
  │              │
  ↓              ↓
Gate-Level     Testbench
Netlist            │
  │                │
  └───────┬────────┘
          ↓
       IVerilog
          ↓
         VCD
          ↓
      GTKWave
```

---

## 5. Example Design

Consider the following combinational circuit:

```verilog
assign y = (a & b) | c;
```

The corresponding logic is:

```text
a ───┐
     AND ───┐
b ───┘      │
            OR ─── y
c ──────────┘
```

The same testbench can first be used with the RTL design and then with the synthesized gate-level netlist.

---

## 6. Timing Validation in GLS

GLS can also be used for **timing validation**.

If the gate-level models are **delay annotated**, the simulator can model the propagation delays of the gates.

```text
Gate-Level Netlist
        +
Delay Information
        ↓
   GLS Simulation
        ↓
 Timing Validation
```

Therefore:

> GLS can be used for functional verification and, when delay information is available, timing validation.

---

## 7. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** occurs when the behavior observed during RTL simulation is different from the behavior of the synthesized circuit.

```text
RTL Simulation
      ≠
Gate-Level Simulation
```

### Common Causes

- Missing sensitivity list
- Incorrect use of blocking assignments
- Incorrect use of non-blocking assignments

---

## 8. Missing Sensitivity List

Consider:

```verilog
module mux (
    input i0,
    input i1,
    input sel,
    output reg y
);

always @(sel)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end

endmodule
```

The sensitivity list contains only:

```verilog
@(sel)
```

Therefore, the `always` block is executed only when `sel` changes.

---
<img width="384" height="338" alt="image" src="https://github.com/user-attachments/assets/f514b6ed-755c-4ab9-891f-26763b8043a8" />

## 9. Problem with the Missing Sensitivity List

The output `y` depends on:

```text
sel
i0
i1
```

But the sensitivity list contains only:

```text
sel
```

Therefore:

```text
Change in sel → always block executes
Change in i0  → always block does not execute
Change in i1  → always block does not execute
```

This can produce incorrect RTL simulation behavior.

### Example

Suppose:

```text
sel = 0
i0  = 0
i1  = 1
```

Then:

```text
y = i0 = 0
```

If `i0` changes from `0` to `1` while `sel` remains `0`, the `always` block may not execute because `i0` is not present in the sensitivity list.

Therefore, `y` may incorrectly remain `0` in simulation.


```

A safer and simpler method is:

```verilog
always @(*)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end
```

### Meaning of `@(*)`

`@(*)` automatically includes the signals required by the combinational block in the sensitivity list.

Therefore, the block is triggered whenever a relevant input changes.

---

## 10. `always @(sel)` vs `always @(*)`

| Feature | `always @(sel)` | `always @(*)` |
|---|---|---|
| Executes when `sel` changes | Yes | Yes |
| Executes when `i0` changes | No | Yes |
| Executes when `i1` changes | No | Yes |
| Suitable for this combinational MUX | No | Yes |
| Risk of simulation mismatch | High | Low |

### Rule

For combinational logic:

```verilog
always @(*)
```

is preferred over an incomplete sensitivity list.

---

## 11. How a Verilog Simulator Works

A Verilog simulator is generally **event-driven**.

A procedural block executes when a signal in its sensitivity list changes.

For example:

```verilog
always @(sel)
```

means:

```text
Wait for sel to change
        ↓
Execute always block
```

If `i0` changes while `sel` does not change:

```text
i0 changes
   ↓
sel did not change
   ↓
always block is not triggered
```

This is why an incomplete sensitivity list can cause simulation problems.

---

## 12. Blocking Assignments

The blocking assignment operator is:

```verilog
=
```

Example:

```verilog
q0 = d;
q  = q0;
```

A blocking assignment executes immediately and in the order in which it is written.

### Important Property

The next statement waits for the previous blocking assignment to execute.

```text
Statement 1
    ↓
Statement 2
    ↓
Statement 3
```

Therefore, blocking assignments introduce **procedural ordering**.

---

## 13. Non-Blocking Assignments

The non-blocking assignment operator is:

```verilog
<=
```

Example:

```verilog
q0 <= d;
q  <= q0;
```

With non-blocking assignments:

- RHS values are evaluated.
- LHS updates are scheduled.
- The updates occur after the current procedural evaluation.
- Multiple assignments can model parallel register updates.

Conceptually:

```text
Evaluate all RHS values
          ↓
Update all LHS values
```

This is why non-blocking assignments are preferred for sequential logic.

---

## 14. Blocking vs Non-Blocking

| Blocking `=` | Non-Blocking `<=` |
|---|---|
| Executes immediately | Update is scheduled |
| Order dependent | Models parallel sequential updates |
| Later statements can see updated values | Later statements see previous values during the same clock event |
| Commonly used for combinational logic | Commonly used for sequential logic |
| Can cause sequential simulation problems | Preferred for flip-flop/register modeling |

### General Rule

```text
Combinational logic → Blocking `=`

Sequential logic → Non-Blocking `<=`
```

---

## 15. Caveats with Blocking Statements

Blocking assignments can create problems when they are used to model sequential logic.

Consider a circuit containing two flip-flops:

```text
        ┌─────┐       ┌─────┐
d ─────►│ FF1 │──────►│ FF2 │────► q
        │ q0  │       │ q   │
        └─────┘       └─────┘
```

The intended behavior is:

```text
q0 ← d
q  ← old q0
```

Therefore, the data should move through the two flip-flops on successive clock cycles.

---

## 16. Blocking Assignment in a Sequential Circuit

Suppose we write:

```verilog
always @(posedge clk)
begin
    q0 = d;
    q  = q0;
end
```

Because `=` is blocking:

```text
q0 = d
   ↓
q = q0
```

The second statement sees the **new value of `q0`**.

Therefore, during simulation, `q` can appear to receive the new value of `q0` during the same clock event.

This can make the two-flop structure appear to behave incorrectly in simulation.

---
<img width="367" height="491" alt="image" src="https://github.com/user-attachments/assets/1202f9f9-5fe7-439d-9426-5660454c6203" />

## 17. Correct Sequential Coding Using Non-Blocking

The preferred implementation is:

```verilog
always @(posedge clk)
begin
    q0 <= d;
    q  <= q0;
end
```

Now:

```text
q0 <= d
q  <= old q0
```

Both RHS values are evaluated before the register updates.

Therefore:

```text
Clock 1:
    q0 gets d
    q gets old q0

Clock 2:
    q0 gets new d
    q gets previous q0
```

This correctly models two flip-flops.

---
<img width="342" height="532" alt="image" src="https://github.com/user-attachments/assets/1722e81c-9d4a-4d55-875f-3f35989d78c6" />

## 18. Why Blocking Can Cause Synthesis-Simulation Mismatch

Consider:

```verilog
always @(posedge clk)
begin
    q0 = d;
    q  = q0;
end
```

### Simulation

Simulation follows procedural ordering:

```text
q0 = d
 ↓
q = q0
```

Therefore, `q` can receive the newly assigned value of `q0`.

### Synthesis

Synthesis interprets the clocked logic and infers hardware based on the sequential structure.

The synthesized circuit can contain separate flip-flops:

```text
d → FF(q0) → FF(q)
```

Thus, the RTL simulation and synthesized hardware can show different behavior.

This is a **synthesis-simulation mismatch**.

---

## 19. Use Non-Blocking for Sequential Circuits

The most important rule is:

> **Use non-blocking assignments for sequential circuits.**

Example:

```verilog
always @(posedge clk)
begin
    q0 <= d;
    q  <= q0;
end
```

Non-blocking assignments should be used for:

- Flip-flops
- Registers
- Counters
- Sequential state machines
- Clocked logic

---

## 20. Order Dependence of Blocking Assignments

Consider:

```verilog
always @(posedge clk)
begin
    q0 = a | b;
    y  = q0 & c;
end
```

The simulator executes:

```text
1. q0 = a | b
2. y  = q0 & c
```

Because `q0` has already been updated, `y` uses the new value of `q0`.

If the order is changed:

```verilog
always @(posedge clk)
begin
    y  = q0 & c;
    q0 = a | b;
end
```

then `y` uses the old value of `q0`.

Therefore:

```text
Order 1 → One simulation result

Order 2 → Different simulation result
```

This shows that blocking assignments can make sequential simulation **order dependent**.

---

## 21. Why Order Matters

The order of statements should not determine the intended hardware behavior of sequential logic.

For example:

```verilog
always @(posedge clk)
begin
    q0 = a | b;
    y  = q0 & c;
end
```

and:

```verilog
always @(posedge clk)
begin
    y  = q0 & c;
    q0 = a | b;
end
```

can produce different simulation results.

However, synthesis may infer the same hardware structure.

Therefore:

```text
Simulation behavior
        ≠
Synthesized hardware behavior
```

This is a major source of synthesis-simulation mismatch.

---

## 22. Blocking Assignments for Combinational Logic

Blocking assignments are appropriate for combinational procedural logic.

Example:

```verilog
always @(*)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end
```

Here, the output is calculated from the current inputs.

For combinational logic:

```text
Input changes
     ↓
Combinational block executes
     ↓
Output is calculated
```

Therefore:

```verilog
=
```

is normally used.

---

## 23. Ternary Operator MUX

A MUX can be written using the ternary operator.

Example:

```verilog
assign y = sel ? i1 : i0;
```

Meaning:

```text
If sel = 1 → y = i1
If sel = 0 → y = i0
```

Equivalent `if-else` implementation:

```verilog
always @(*)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end
```

Both describe a **2:1 MUX**.

### MUX Representation

```text
        ┌─────┐
i0 ────►│     │
        │ MUX │────► y
i1 ────►│     │
        └──┬──┘
           │
          sel
```

---

## 24. Ternary Operator MUX — File Flow

The ternary-operator MUX example can be implemented using:

```verilog
module ternary_operator_mux (
    input  i0,
    input  i1,
    input  sel,
    output y
);

assign y = sel ? i1 : i0;

endmodule
```

A corresponding testbench is used to apply different values of:

- `i0`
- `i1`
- `sel`

and observe `y`.

### Expected Behavior

```text
sel = 0 → y = i0
sel = 1 → y = i1
```

---

## 25. Simulation Using IVerilog

The basic simulation flow is:

```text
Verilog Design
      +
Testbench
      ↓
IVerilog
      ↓
Simulation Output / VCD
      ↓
GTKWave
```

Typical command:
```bash
gvim ternary_operator_mux.v -o bad_mux.v -o good_mux.v
```
<img width="940" height="457" alt="image" src="https://github.com/user-attachments/assets/a5f177d2-8adc-440b-857e-711b57a89bb9" />

```bash
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
```

Run the simulation:

```bash
./a.out
```

View the waveform:

```bash
gtkwave tb_ternary_operator_mux.vcd
```

<img width="940" height="200" alt="image" src="https://github.com/user-attachments/assets/00984ce3-37df-40c3-a87f-53249a1d86da" />


## 26. Yosys Synthesis Flow

**Yosys** can be used to synthesize the RTL and generate a gate-level netlist.

A typical flow is:

```yosys
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty <library>.lib
write_verilog -noattr ternary_operator_mux.net.v
show
```
<img width="940" height="432" alt="image" src="https://github.com/user-attachments/assets/90254b92-306b-4924-9a83-619e07c222bd" />

### Main Steps

```text
Read RTL
   ↓
Synthesis
   ↓
Technology Mapping
   ↓
Generate Netlist
   ↓
View Synthesized Circuit
```

---

## 27. Gate-Level Netlist and GLS

After synthesis, a gate-level netlist is generated.

Example:

```text
ternary_operator_mux.net.v
```

The gate-level netlist can then be simulated using IVerilog.

### GLS Flow

```text
RTL
 ↓
Yosys
 ↓
Gate-Level Netlist
 ↓
IVerilog
 ↓
VCD
 ↓
GTKWave
```

The **same testbench** can be used to compare RTL behavior with synthesized gate-level behavior.

---

## 28. GLS Using Gate-Level Libraries

The synthesized netlist may contain standard-cell or gate-level library cells.

Therefore, the corresponding Verilog library models must also be available during simulation.

A typical flow contains files such as:

```text
my_lib/verilog_model/primitives.v
my_lib/verilog_model/sky130_fd_sc_hd.v
gate-level-netlist.v
testbench.v
```

A representative compilation command is:

```bash
iverilog \
  my_lib/verilog_model/primitives.v \
  my_lib/verilog_model/sky130_fd_sc_hd.v \
  gate-level-netlist.v \
  testbench.v
```

Run:

```bash
./a.out
```

Then inspect the waveform:

```bash
gtkwave testbench.vcd
```
<img width="940" height="280" alt="image" src="https://github.com/user-attachments/assets/591107da-edde-48b1-be90-81a21b4efc51" />

---
Example: Bad_mux

i.<img width="940" height="302" alt="image" src="https://github.com/user-attachments/assets/06b38ca2-b613-43e0-8748-c56933984410" />
ii.<img width="940" height="290" alt="image" src="https://github.com/user-attachments/assets/c02f4f38-cce7-4ef2-b6b6-7ef68ce96802" />
Here it is a **Synthesis Simulation mismatch**

## 29. Blocking-Statement GLS Experiment

The experiment demonstrates a **synthesis-simulation mismatch caused by blocking assignments**.

### Example

<img width="940" height="338" alt="image" src="https://github.com/user-attachments/assets/16c2f1fc-ffca-4d71-b322-85c3352da31e" />

<img width="472" height="143" alt="image" src="https://github.com/user-attachments/assets/c3331556-4358-450b-b9d5-0ddb83bb0a31" />

### Synthesis

```bash
iverilog blocking_caveat.v tb_blocking_caveat.v
```

Run the simulation:

```bash
./a.out
```

View the waveform:

```bash
gtkwave tb_blocking_caveat.vcd
```
<img width="940" height="357" alt="image" src="https://github.com/user-attachments/assets/aa761ed1-8169-4f74-bd4a-05e0268154b0" />

The RTL can be synthesized using Yosys:

```yosys
read_verilog blocking_caveat.v
synth -top blocking_caveat
abc -liberty <library>.lib
write_verilog -noattr blocking_caveat.net.v
show
```
<img width="940" height="430" alt="image" src="https://github.com/user-attachments/assets/aa6fa4e9-d9b4-4889-ac63-a790ebe99df5" />

### Gate-Level Simulation

```bash
iverilog \
  my_lib/verilog_model/primitives.v \
  my_lib/verilog_model/sky130_fd_sc_hd.v \
  blocking_caveat.net.v \
  tb_blocking_caveat.v
```

Run:

```bash
./a.out
```

View:

```bash
gtkwave tb_blocking_caveat.vcd
```
<img width="940" height="359" alt="image" src="https://github.com/user-attachments/assets/7c96d6dd-aabc-429e-99ab-130c89fe34b9" />

This experiment demonstrates how improper use of blocking assignments in sequential logic can lead to a **synthesis-simulation mismatch**.

---

## 30. RTL Simulation vs GLS

| RTL Simulation | Gate-Level Simulation |
|---|---|
| Uses RTL code | Uses synthesized gate-level netlist |
| High-level description | Gate/cell-level description |
| Usually faster | Usually slower |
| Checks intended functionality | Checks synthesized implementation |
| Usually does not contain physical gate delays | Can contain gate delays |
| Performed before synthesis | Performed after synthesis |

### Simple Comparison

```text
RTL Simulation
    ↓
Checks RTL functionality

GLS
    ↓
Checks synthesized hardware behavior
```

---

## 31. Functional GLS vs Timing GLS

### Functional GLS

Uses the synthesized gate-level netlist to verify functionality.

```text
Gate-Level Netlist
       +
Testbench
       ↓
Functional GLS
```

### Timing GLS

Uses a gate-level model with delay information.

```text
Gate-Level Netlist
       +
Delay Annotation
       +
Testbench
       ↓
Timing GLS
```

Timing GLS helps validate whether the synthesized implementation behaves correctly when gate propagation delays are considered.

---

## 32. Important Commands Used in the Flow

### Open Verilog Files

```bash
gvim <design>.v
```

### IVerilog Compilation

```bash
iverilog -o a.out <design>.v <testbench>.v
```

### Run Simulation

```bash
./a.out
```

### View Waveform

```bash
gtkwave <waveform>.vcd
```

### Yosys Synthesis

```yosys
read_verilog <design>.v
synth -top <top_module>
abc -liberty <library>.lib
write_verilog -noattr <netlist>.v
show
```

### General Flow

```text
Design
 ↓
iverilog
 ↓
./a.out
 ↓
VCD
 ↓
gtkwave
```

---

## 33. Complete Practical Flow

The complete flow covered in this module is:

```text
                    RTL Design
                        │
                        ↓
                 RTL Simulation
                        │
                        ↓
                    Synthesis
                        │
                        ↓
              Gate-Level Netlist
                        │
             ┌──────────┴──────────┐
             ↓                     ↓
      Functional GLS          Timing GLS
             │                     │
             ↓                     ↓
            VCD                   VCD
             │                     │
             └──────────┬──────────┘
                        ↓
                    GTKWave
```

### Detailed Flow

```text
1. Write RTL
       ↓
2. Write Testbench
       ↓
3. Run RTL Simulation
       ↓
4. Verify waveform
       ↓
5. Synthesize using Yosys
       ↓
6. Generate Gate-Level Netlist
       ↓
7. Include Gate/Standard-Cell Libraries
       ↓
8. Run GLS using the SAME Testbench
       ↓
9. Generate VCD
       ↓
10. View waveform using GTKWave
       ↓
11. Compare RTL and GLS behavior
```

---

## ⭐ Final Rule to Remember

> **For combinational logic: use `always @(*)` with blocking `=`.**

> **For sequential logic: use `always @(posedge clk)` with non-blocking `<=`.**

> **For GLS: use the synthesized gate-level netlist with the same testbench.**

> **For timing GLS: use delay-annotated gate-level models.**

---
