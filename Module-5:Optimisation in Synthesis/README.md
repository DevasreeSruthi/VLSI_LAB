# Module 5
# Synthesis Optimization & Generate Constructs

This repository contains notes:

- `if / else if / else` constructs
- `case` statements
- Incomplete `if` and `case` statements
- Latch inference
- Priority logic
- Partial assignments
- Overlapping case conditions
- `for` loops
- `generate for`
- MUX and DEMUX implementation
- Ripple Carry Adder (RCA)
- RTL simulation
- Synthesis using Yosys
- Gate-level visualization using GTKWave

---

# 1. Optimization in Synthesis

Synthesis tools convert RTL code into hardware such as:

- Multiplexers
- Registers
- Combinational logic
- Latches
- Flip-flops
- Gates

The way RTL is written directly affects the hardware inferred by the synthesis tool.

Good RTL coding style helps to:

- Avoid unwanted latches
- Generate efficient combinational logic
- Improve readability
- Avoid unpredictable behavior
- Make synthesis results easier to understand

---

# 2. `if`-`else` Case Constructs

The `if` construct is mainly used for **priority logic**.

## Basic Syntax

```verilog
if (condition1)
begin
    statement1;
end
else if (condition2)
begin
    statement2;
end
else if (condition3)
begin
    statement3;
end
else
begin
    statement4;
end
```

### Important Point

`if` / `else if` / `else` represents **priority logic**.

Only one branch executes.

For example:

```verilog
if (cond1)
    y = c1;
else if (cond2)
    y = c2;
else if (cond3)
    y = c3;
else
    y = 1'b0;
```

If both `cond1` and `cond2` are true, `cond1` gets priority.

### Hardware Interpretation

The above RTL can be implemented as a chain of multiplexers:

```text
              cond3
c3 ---------- MUX
               |
              cond2
c2 ---------- MUX
               |
              cond1
c1 ---------- MUX
               |
               y
```

Therefore, nested `if` statements can create **priority mux structures**.

---

# 3. Incomplete `if` Statements

An incomplete `if` statement does not assign an output for every possible condition.

Example:

```verilog
always @(*)
begin
    if (cond1)
        y = a;
    else if (cond2)
        y = b;
end
```

What happens when both `cond1` and `cond2` are `0`?

There is no assignment to `y`.

Therefore, the synthesis tool must preserve the previous value of `y`.

This results in **latch inference**.

### Hardware

```text
        cond1
a --------MUX
            \
             MUX ---- y
            /
b ---------MUX

       + Latch
```

### Why is this a problem?

If a latch was not intended, it is considered a bad RTL coding style.

An incomplete `if` may cause:

- Inferred latches
- Incomplete combinational logic
- Timing problems
- Difficult-to-debug hardware

---

# 4. Avoiding Latch Inference

Always assign a default value before the conditional statements.

```verilog
always @(*)
begin
    y = 1'b0;

    if (cond1)
        y = a;
    else if (cond2)
        y = b;
end
```

Now `y` always has a value.

Therefore, no latch is inferred.

Another safe approach is to use a final `else`:

```verilog
always @(*)
begin
    if (cond1)
        y = a;
    else if (cond2)
        y = b;
    else
        y = 1'b0;
end
```

---

# 5. Counter Example – Incomplete `if`

A counter is normally written inside a clocked `always` block.

Example:

```verilog
always @(posedge clk or posedge reset)
begin
    if (reset)
        count <= 3'b000;
    else if (en)
        count <= count + 1;
end
```

When `en = 0`, there is no assignment to `count`.

For sequential logic, this means:

```text
count keeps its previous value
```

This is intentional behavior for a register.

Therefore, an incomplete assignment in a clocked block does **not necessarily mean a problem**.

For a combinational block, however, incomplete assignments usually indicate an unintended latch.

---

# 6. `case` Statement

A `case` statement is generally used when selecting between multiple possibilities.

It is especially useful for:

- MUXes
- Decoders
- Control logic
- State machines

## Basic Syntax

```verilog
always @(*)
begin
    case (sel)

        2'b00:
        begin
            y = c1;
        end

        2'b01:
        begin
            y = c2;
        end

        2'b10:
        begin
            y = c3;
        end

        2'b11:
        begin
            y = c4;
        end

    endcase
end
```

The expression inside `case` is compared against each case item.

---

# 7. Case Statement – MUX Representation

For example:

```verilog
always @(*)
begin
    case (sel)
        2'b00: y = c1;
        2'b01: y = c2;
        2'b10: y = c3;
        2'b11: y = c4;
    endcase
end
```

This represents a **4:1 multiplexer**.

```text
 c1 ----\
 c2 -----\
 c3 ------> 4:1 MUX ----> y
 c4 -----/
            ^
            |
           sel
```

---

# 8. Complete vs Incomplete `case`

## Incomplete Case

```verilog
always @(*)
begin
    case (sel)
        2'b00: y = a;
        2'b01: y = b;
    endcase
end
```

What happens for:

```text
sel = 2'b10
sel = 2'b11
```

There is no assignment to `y`.

Therefore, a latch may be inferred.

---

# 9. Using `default` in a Case

A safer coding style is:

```verilog
always @(*)
begin
    case (sel)

        2'b00:
            y = a;

        2'b01:
            y = b;

        default:
            y = 1'b0;

    endcase
end
```

The `default` branch handles all unspecified values.

This prevents incomplete assignment and helps avoid unintended latches.

---

# 10. Caveats with `case`

## 10.1 Incomplete Case

An incomplete `case` may result in:

```text
Incomplete case
        ↓
No assignment for some conditions
        ↓
Latch inference
```

### Solution

Use:

- A `default` case
- Or assign a default value before the case

Example:

```verilog
always @(*)
begin
    y = 1'b0;

    case (sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        2'b11: y = d;
    endcase
end
```

---

# 11. Partial Assignment in Case

Consider:

```verilog
reg [1:0] sel;
reg x, y;

always @(*)
begin
    case (sel)

        2'b00:
        begin
            x = a;
            y = b;
        end

        2'b01:
        begin
            x = c;
        end

        default:
        begin
            x = d;
            y = b;
        end

    endcase
end
```

In the `2'b01` case:

```verilog
x = c;
```

but `y` is not assigned.

Therefore, `y` can retain its previous value and a latch may be inferred for `y`.

### Important Rule

Every output assigned inside a combinational `case` should receive an assignment for every possible case.

A good technique is:

```verilog
always @(*)
begin
    x = 1'b0;
    y = 1'b0;

    case (sel)
        2'b00:
        begin
            x = a;
            y = b;
        end

        2'b01:
        begin
            x = c;
        end

        default:
        begin
            x = d;
            y = b;
        end
    endcase
end
```

---

# 12. Overlapping Case Conditions

Case items should be designed so that the intended selection is clear.

Overlapping or ambiguous conditions can lead to unexpected results depending on the type of case statement and coding style.

For priority behavior, `if / else if` is usually clearer.

```text
if
 ├── condition 1
 ├── condition 2
 ├── condition 3
 └── else

        ↓

Priority logic
```

Whereas:

```text
case
 ├── case 1
 ├── case 2
 ├── case 3
 └── default

        ↓

Selection logic
```

### General Rule

- Use `if / else if` when **priority matters**.
- Use `case` when **selection among mutually exclusive choices** is intended.

---

# 13. `if` vs `case`

| Feature | `if / else if` | `case` |
|---|---|---|
| Main use | Priority logic | Selection logic |
| Hardware | Priority MUX | MUX / decoder |
| Priority | Yes | Normally no intended priority |
| Multiple conditions | Checked sequentially | Compared with case expression |
| Default handling | `else` | `default` |
| Good for | Priority encoder/control | MUX/decoder/state selection |

---

# 14. `for` Loop

A `for` loop is generally used inside procedural blocks such as `always`.

It is useful when the same operation must be performed repeatedly.

Example:

```verilog
always @(*)
begin
    for (i = 0; i < 32; i = i + 1)
    begin
        if (i == sel)
            y = inp[i];
    end
end
```

A `for` loop is useful for evaluating expressions repeatedly inside an RTL block.

---

# 15. `for` Loop vs `generate for`

There are two important looping constructs in Verilog/SystemVerilog.

## Procedural `for` Loop

```verilog
always @(*)
begin
    for (i = 0; i < 32; i = i + 1)
    begin
        ...
    end
end
```

Used:

- Inside procedural blocks
- Inside `always`
- For repeated procedural operations
- For evaluating expressions

---

## ` for generate `

```verilog
genvar i;

generate
    for (i = 0; i < 8; i = i + 1)
    begin
        ...
    end
endgenerate
```

Used:

- Outside procedural `always` blocks
- For hardware replication
- For instantiating multiple copies of hardware

### Main Difference

```text
for loop
    ↓
Procedural repetition

generate for
    ↓
Hardware/module replication
```

---

# 16. Generate Construct

A `generate for` loop can replicate hardware multiple times.

Example:

```verilog
genvar i;

generate
    for (i = 0; i < 8; i = i + 1)
    begin
        and u1 (
            .a(a[i]),
            .b(b[i]),
            .y(y[i])
        );
    end
endgenerate
```

This creates **8 instances** of the AND gate.

Conceptually:

```text
AND0
AND1
AND2
AND3
AND4
AND5
AND6
AND7
```

Instead of manually instantiating every gate.

---

# 17. Why `for generate` is Useful

Suppose a single Full Adder is already designed.

To construct a 4-bit Ripple Carry Adder:

```text
FA0 → FA1 → FA2 → FA3
```

Instead of writing four separate instances, `generate for` can replicate the Full Adder.

This makes RTL:

- Shorter
- Cleaner
- Easier to maintain
- Scalable
- Less error-prone

---

# 18. Ripple Carry Adder Using `generate`

A Ripple Carry Adder (RCA) is constructed by connecting multiple Full Adders.

For a 4-bit RCA:

```text
       num1[0] num2[0]
            │    │
            ▼    ▼
          +-------+
cin ----->|  FA0  |---- sum[0]
          +-------+
              │
             c1
              │
              ▼
          +-------+
num1[1] ->|  FA1  |---- sum[1]
num2[1] ->|       |
          +-------+
              │
             c2
              │
              ▼
             ...
```

The carry from one Full Adder becomes the carry input of the next Full Adder.

---

# 19. RCA Example

For example:

```text
  0110
+ 0011
------
  1001
```

The carry propagates from the least significant bit toward the most significant bit.

The same hardware can be instantiated multiple times using `generate`.

---

# 20. MUX Using `for` Loop

A wide MUX can be implemented using a procedural loop.

Example for a 32:1 MUX:

```verilog
always @(*)
begin
    for (i = 0; i < 32; i = i + 1)
    begin
        if (i == sel)
            y = inp[i];
    end
end
```

Assumption:

```verilog
input [31:0] inp;
```

The loop checks all 32 inputs and selects the input corresponding to `sel`.

---

# 21. DEMUX Using `for` Loop

Example of an 8:1-style one-hot selection:

```verilog
always @(*)
begin
    op_bus[7:0] = 8'b0;

    for (i = 0; i < 8; i = i + 1)
    begin
        if (i == sel)
            op_bus[i] = input;
    end
end
```

This can be used to implement a wide DEMUX.

### Important

The output bus should be initialized before the loop:

```verilog
op_bus = 8'b0;
```

This ensures that all outputs have defined values.

---

# 22. Lab Experiments

The following experiments are based on the RTL coding and synthesis concepts covered above.

---

## Lab 1 – Incomplete `if`

### Objective

To observe latch inference caused by an incomplete `if` statement.


### Simulation Flow

<img width="940" height="450" alt="image" src="https://github.com/user-attachments/assets/6068fee0-1ad0-46c3-8815-e47cab0a00d9" />
<br><br>
<img width="940" height="469" alt="image" src="https://github.com/user-attachments/assets/47d5f3cb-3733-47ba-8d7c-f064c647cc70" />

```bash
iverilog incomp-if.v tb-incomp-if.v
./a.out
gtkwave tb-incomp-if.vcd
```
<img width="940" height="280" alt="image" src="https://github.com/user-attachments/assets/e3f9c45b-c4b2-4b87-8f2b-302a4ee3a867" />

### Synthesis Flow

Start Yosys:

```bash
yosys
```

Then:

```yosys
read_liberty
read_verilog incomp-if.v
synth -top incomp-if
abc -liberty
show
```
<img width="940" height="402" alt="image" src="https://github.com/user-attachments/assets/184d4ba8-3145-43ea-8656-7e7f146bdcd7" />

### Result

The synthesized circuit showed a **latch**.

---

# Lab 2 
<img width="940" height="465" alt="image" src="https://github.com/user-attachments/assets/17cf66bc-0683-4162-a837-6deaf4d90498" />

```bash
iverilog incomp-if.v tb-incomp-if.v
./a.out
gtkwave tb-incomp-if.vcd
```
<img width="940" height="301" alt="image" src="https://github.com/user-attachments/assets/5d45d43a-6233-4d11-abf9-ae6a25f594da" />

### Synthesis

```bash
yosys
read_verilog comp-if.v
synth -top comp-if
abc -liberty
show
```
<img width="940" height="374" alt="image" src="https://github.com/user-attachments/assets/28e48709-e155-4883-8813-b37986fa49b4" />

---

# Lab 3 – Incomplete `case`

<img width="940" height="445" alt="image" src="https://github.com/user-attachments/assets/f0dd5e71-59d8-4b3c-9547-8ed1d7e258ec" />
<br><br>
<img width="940" height="487" alt="image" src="https://github.com/user-attachments/assets/32565bd0-5e32-4ebf-8c42-92bd811add0f" />

### Simulation

```bash
iverilog incomp_case.v tb_incomp_case.v
./a.out
gtkwave tb_incomp_case.vcd
```
<img width="940" height="311" alt="image" src="https://github.com/user-attachments/assets/0fb4b84a-7203-4eef-a189-9f712b72b926" />

### Synthesis

```bash
yosys
read_verilog incomp_case.v
synth -top incomp_case
abc -liberty
show
```
<img width="940" height="352" alt="image" src="https://github.com/user-attachments/assets/7f50e7af-6479-4e4f-8579-2c4162499bd0" />

### Expected Result

A latch is visible in the synthesized circuit.

---

# Lab 4 – Complete `case` with `default`
Code:
<img width="940" height="319" alt="image" src="https://github.com/user-attachments/assets/d9eabc38-aa5e-4d6c-8f7b-9f385850d339" />

<img width="940" height="547" alt="image" src="https://github.com/user-attachments/assets/a358da23-d2c7-4ac9-91aa-92577fe064b3" />

```bash
iverilog comp-case.v tb_comp-case.v
./a.out
gtkwave tb_comp-case.vcd
```
<img width="940" height="343" alt="image" src="https://github.com/user-attachments/assets/4812b1fc-e4c5-4b1c-98ce-c7b5c12b5f60" />

```bash
yosys
read_verilog incomp_case.v
synth -top incomp_case
abc -liberty
show
```
<img width="940" height="356" alt="image" src="https://github.com/user-attachments/assets/129118d8-cd18-4f15-aba3-0a9b8cdbe177" />

### Result

No latch is inferred because every possible selection has an assignment.

---

# Lab 5 – Partial Case Assignment
<img width="940" height="462" alt="image" src="https://github.com/user-attachments/assets/4017b7b4-97c9-44ec-89bf-3a0df661775c" />

<img width="940" height="408" alt="image" src="https://github.com/user-attachments/assets/4697f182-dcd2-4bcb-b0f1-bece388e2dbf" />


# Lab 6 – Bad / Overlapping Case

### Objective

To understand the effect of improperly defined case conditions.

Case conditions should be written clearly and should not unintentionally overlap.

Use `if / else if` when priority is required.

Use `case` when the selection conditions are intended to be mutually exclusive.

### Simulation

```bash
iverilog bad-case.v tb-bad-case.v
./a.out
gtkwave tb-bad-case.vcd
```
<img width="940" height="301" alt="image" src="https://github.com/user-attachments/assets/123d71b1-84f7-4aa3-a51a-17bcf0b238b4" />

### Synthesis

```bash
yosys
read_verilog bad-case.v
synth -top bad-case(no latches)
abc -liberty
write_verilog -noattr bad_case_net.v


iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_case_net.v tb_bad_case.v
./a.out
gtkwave tb_bad_case.vcd
```
<img width="940" height="391" alt="image" src="https://github.com/user-attachments/assets/0de585d7-aaf1-4e89-820e-299f617d72a8" />

---

#  MUX Using `generate`

### Objective

To implement repeated hardware using a `generate for` loop.

### Basic Structure

```verilog
genvar i;

generate
    for (i = 0; i < 32; i = i + 1)
    begin
        // repeated hardware
    end
endgenerate
```

### Why Generate?

Instead of writing the same hardware 32 times manually, the `generate` block replicates it automatically.

---

# DEMUX Using `case`

### Example

```verilog
always @(*)
begin
    case (sel)

        3'b000: op_bus[0] = input;
        3'b001: op_bus[1] = input;
        3'b010: op_bus[2] = input;
        3'b011: op_bus[3] = input;
        3'b100: op_bus[4] = input;
        3'b101: op_bus[5] = input;
        3'b110: op_bus[6] = input;
        3'b111: op_bus[7] = input;

    endcase
end
```

A default assignment should be provided to all outputs to avoid unintended latches.

---

# DEMUX Using `generate`

A DEMUX can also be implemented using a `generate for` loop.

Example:

```verilog
genvar i;

generate
    for (i = 0; i < 8; i = i + 1)
    begin
        assign op_bus[i] = (sel == i) ? input : 1'b0;
    end
endgenerate
```

This creates repeated hardware for each output bit.

### Key Observation

The case-based and generate-based implementations can produce equivalent hardware.

---

# Ripple Carry Adder Using `generate`

### Objective

To construct a Ripple Carry Adder by instantiating multiple Full Adders.

### Concept

```text
FA0 → FA1 → FA2 → FA3
```

Carry propagates from one stage to the next.

### Generate Structure

```verilog
genvar i;

generate
    for (i = 0; i < 4; i = i + 1)
    begin
        full_adder FA (
            .a(num1[i]),
            .b(num2[i]),
            .cin(carry[i]),
            .sum(sum[i]),
            .cout(carry[i+1])
        );
    end
endgenerate
```

The first carry input can be connected to the external `cin`.

---

# 23. RTL Simulation Flow

The general RTL simulation flow used in these labs is:

```text
Verilog RTL
    ↓
Icarus Verilog
    ↓
a.out
    ↓
VCD waveform
    ↓
GTKWave
```
<img width="940" height="235" alt="image" src="https://github.com/user-attachments/assets/bcbce6d0-6309-4ce9-984b-b4e6b56a3ea1" />
<br><br>
<img width="940" height="424" alt="image" src="https://github.com/user-attachments/assets/00be274b-a4c2-41e9-939d-695de1e1adc7" />

### Commands

```bash
iverilog mux_generate.v tb_mux_generate.v
./a.out
gtkwave tb_mux_generate.vcd
```
<img width="940" height="302" alt="image" src="https://github.com/user-attachments/assets/97076766-1d96-4993-aba3-152e287f8b78" />

---
demux_generate:
Code:
<img width="940" height="341" alt="image" src="https://github.com/user-attachments/assets/ad39edd6-6799-4be0-bce5-8e3851aa4d41" />
<br><br>
<img width="578" height="545" alt="image" src="https://github.com/user-attachments/assets/a2a2598e-e36e-414c-ae95-8562a7c4cc91" />

```bash
iverilog demux_case.v tb_demux_case.v
./a.out
gtkwave tb_demux_case.vcd
```
<img width="940" height="372" alt="image" src="https://github.com/user-attachments/assets/d703e126-0594-4b90-8f41-6f304d5b5027" />

```bash
iverilog demux_generate.v tb_demux_generate.v
./a.out
gtkwave tb_demux_generate.vcd
```
<img width="940" height="360" alt="image" src="https://github.com/user-attachments/assets/8ce5813e-3acb-4e7d-8c62-0c53c630d797" />
Both demux_case and demux_generate are same

### Example:Ripple Carry Adder
<img width="940" height="377" alt="image" src="https://github.com/user-attachments/assets/4e0aba5b-593e-400e-a484-a9ff4ff9292a" />
<br><br>
<img width="940" height="534" alt="image" src="https://github.com/user-attachments/assets/3d4721a1-6e1a-43d4-a704-8ef1e84aff4a" />

```bash
iverilog fa.v rca.v tb_rca.v
./a.out
gtkwave tb_rca.vcd
```
<img width="940" height="373" alt="image" src="https://github.com/user-attachments/assets/e76dff6f-dc5f-412f-b196-d9cb2a981467" />

 Yosys synthesis:
<img width="940" height="806" alt="image" src="https://github.com/user-attachments/assets/38b62201-2409-4095-8053-73f64a6b72ef" />
<br><br>
<img width="940" height="314" alt="image" src="https://github.com/user-attachments/assets/dc649cf4-a3be-4c34-965b-843e138e7e9c" />

```bash
write_verilog -noattr rca_net.v
iverilog rca_net.v tb_rca.v
./a.out
gtkwave tb_rca.vcd
```
<img width="940" height="310" alt="image" src="https://github.com/user-attachments/assets/0ab99220-bd64-4b74-896b-a51f1d2657c0" />

# 24. Synthesis Flow Using Yosys

The general synthesis flow is:

```text
Verilog RTL
    ↓
Yosys
    ↓
RTL synthesis
    ↓
Technology mapping
    ↓
Gate-level representation
    ↓
Show circuit
```

### Basic Commands

```bash
yosys
```

```yosys
read_liberty
read_verilog design.v
synth -top design
abc -liberty
show
```

---

# 25. Useful Yosys Commands

## Read Verilog

```yosys
read_verilog design.v
```

Reads the RTL Verilog source file.

## Synthesis

```yosys
synth -top design
```

Synthesizes the specified top-level module.

## Technology Mapping

```yosys
abc -liberty
```

Performs technology mapping using the selected liberty library.

## Display Circuit

```yosys
show
```

Displays the synthesized circuit.

---

# 26. Netlist Generation

After synthesis, a netlist can be generated.

Example:

```yosys
write_verilog -noattr design_net.v
```

The generated netlist can then be used for further simulation or inspection.

---


# 27. Quick Revision

```text
if / else
    ↓
Priority logic

case
    ↓
Selection logic

Incomplete combinational if/case
    ↓
Latch inference

default assignment
    ↓
Avoids incomplete combinational logic

for loop
    ↓
Procedural repetition

generate for
    ↓
Hardware replication

Yosys
    ↓
Synthesis

Icarus Verilog
    ↓
Simulation

GTKWave
    ↓
Waveform analysis
```

---

# 28. Key Takeaways

- `if / else if / else` is mainly used for **priority logic**.
- `case` is useful for **selection logic**, MUXes and decoders.
- An incomplete combinational `if` can infer a **latch**.
- An incomplete combinational `case` can also infer a **latch**.
- A `default` branch helps make a `case` complete.
- Partial assignment of outputs inside a case can cause **latch inference** for the unassigned outputs.
- Use `if / else if` when priority is required.
- Use `case` when conditions represent mutually exclusive selections.
- A procedural `for` loop is used inside procedural blocks.
- `generate for` is used outside procedural blocks to replicate hardware.
- `generate` is particularly useful for structures such as:
  - MUXes
  - DEMUXes
  - Ripple Carry Adders
  - Arrays of gates
  - Repeated module instances
- Icarus Verilog can be used for RTL simulation.
- GTKWave can be used to inspect VCD waveforms.
- Yosys can be used for RTL synthesis and circuit visualization.
- Good RTL coding style helps prevent unintended hardware and makes synthesis results easier to understand.

---

## Overall Flow

```text
                 RTL DESIGN
                     │
        ┌────────────┴────────────┐
        │                         │
   if / case                 for / generate
        │                         │
        │                    Hardware
        │                    replication
        │
   Combinational logic
        │
        ├── Complete ──────► No unintended latch
        │
        └── Incomplete ────► Latch inference
                     │
                     ▼
                RTL Simulation
                     │
                 Icarus Verilog
                     │
                     ▼
                  GTKWave
                     │
                     ▼
                  Synthesis
                     │
                   Yosys
                     │
                     ▼
              Gate-level Circuit

```
