# Module 3 – Combinational and Sequential Optimizations

## 1. Introduction to Optimization

Optimization in digital design is mainly performed to reduce/squeeze the logic and obtain an optimized design.

An optimized design is generally more efficient in terms of:

- Area
- Power
- Performance

---

# 2. Combinational Logic Optimization

## Why Combinational Logic Optimization?

The main objective is to simplify the logic and obtain an optimized circuit.

### Benefits

- Reduced area
- Reduced power consumption
- Better performance
- Reduced number of gates/transistors

## Techniques Used

### 1. Constant Propagation

Constant propagation is a direct optimization technique in which known constant values (`0` or `1`) are propagated through the circuit.

### 2. Boolean Logic Optimization

Common techniques include:

- K-Map
- Quine–McCluskey method

---

## Example: Constant Propagation

Consider:

$$
Y = \overline{AB+C}
$$

If:

$$
A=0
$$

then:

$$
Y=\overline{0B+C}
$$

$$
Y=\overline{C}
$$

Therefore:

$$
\boxed{Y=\overline{C}}
$$

Instead of implementing the complete logic circuit, only an inverter for `C` is required.

### Advantage

The original circuit may require several MOS transistors, whereas after constant propagation the simplified circuit requires only the logic necessary for:

    C → NOT → Y

Hence, **area and power are reduced**.

---

# 3. Boolean Logic Optimization

Boolean expressions can often be simplified using algebraic rules or multiplexer-based logic optimization.

### Example

For a 2:1 multiplexer:

$$
Y=\overline{S}I_0+SI_1
$$

Repeated simplification of the expression can eliminate redundant logic.

The notes' example simplifies to:

$$
\boxed{Y=a\overline{c}}
$$

---

# 4. Sequential Logic Optimization

Sequential logic optimization is used to simplify circuits containing:

- Flip-flops
- Registers
- Counters
- State machines

## Techniques

### Basic Technique

- Sequential constant propagation

### Advanced Techniques

- State optimization
- Retiming
- Sequential logic cloning
- Floorplan-based optimization
- Synthesis-based optimization

---

# 5. Sequential Constant Propagation

Sequential constant propagation identifies flip-flops whose outputs always have a constant value.

If the output of a flip-flop is always `0` or always `1`, the flip-flop and associated logic may be removed or simplified.

## Example – Reset

If a flip-flop is reset such that:

$$
Q=0
$$

and the output logic is:

$$
Y=\overline{A\cdot Q}
$$

then:

$$
Y=\overline{A\cdot0}
$$

$$
Y=\overline{0}
$$

Therefore:

$$
\boxed{Y=1}
$$

Thus, `Y` is always `1`, independent of the clock and reset conditions after the constant is established.

---

## Set Flip-Flop Example

If a flip-flop has a **SET** input:

- When `SET = 1`, `Q = 1`
- When `SET = 0`, `Q` depends on the data/input and clock

Therefore, simply having a set/reset input does **not necessarily** make the flip-flop a sequential constant.

### Important Point

A flip-flop with a `D` input becomes a sequential constant only when its `Q` output can be proven to **always take a constant value**.

---

# 6. State Optimization

State optimization is used to reduce unnecessary or unused states in sequential circuits.

### Benefits

- Reduced number of flip-flops
- Reduced area
- Reduced power
- Simplified state logic
- Improved performance

---

# 7. State Optimization – Multiplexer Examples

## Example 1 – `opt_check1.v`

The Verilog code is:

    module opt_check1(input a, input b, output y);
        assign y = a ? b : 0;
    endmodule

The multiplexer has:

- Select = `a`
- Input 0 = `0`
- Input 1 = `b`

Therefore:

$$
Y=\overline{a}(0)+ab
$$

$$
\boxed{Y=ab}
$$

### Yosys Commands

    yosys
    read_liberty
    read_verilog opt_check1.v
    synth -top opt_check1
    opt_clean -purge
    abc -liberty
    show

---
<img width="940" height="206" alt="image" src="https://github.com/user-attachments/assets/f6ee90c5-7752-4b47-afb8-6f0b0059e257" />

## Example 2 – `opt_check2.v`

The Verilog code is:

    module opt_check2(input a, input b, output y);
        assign y = a ? 0 : b;
    endmodule

For the multiplexer:

- Select = `a`
- Input 0 = `b`
- Input 1 = `0`

Therefore:

$$
Y=\overline{a}b+a(0)
$$

$$
\boxed{Y=\overline{a}b}
$$

### Yosys Commands

    yosys
    read_liberty
    read_verilog opt_check2.v
    synth -top opt_check2
    opt_clean -purge
    abc -liberty
    show

---
<img width="940" height="214" alt="image" src="https://github.com/user-attachments/assets/e6ba79db-67dd-4ef1-ad52-edaac13ad25b" />

## Example 3 – `opt_check3.v`

The Verilog code is:

    module opt_check3(input a, input b, input c, output y);
        assign y = a ? (c ? b : 0) : 0;
    endmodule

The logic contains two multiplexers.

The expression can be expanded as:

$$
Y=a(cb+\overline{c}(0))+\overline{a}(0)
$$

Therefore:

$$
Y=abc
$$

$$
\boxed{Y=abc}
$$

### Yosys Commands

    yosys
    read_verilog opt_check3.v
    synth -top opt_check3
    opt_clean -purge
    abc -liberty
    show

---
<img width="940" height="309" alt="image" src="https://github.com/user-attachments/assets/0c5c2c63-4516-42fa-b5e4-f97791bf37ef" />

# 8. Sequential Optimization – D Flip-Flop Constant Propagation

A sequential optimization experiment can be performed using D flip-flops.

## Typical Files

- `dff_const1.v`
- `dff_const2.v`
- `dff_const3.v`
- Testbench files such as `tb_dff_const1.v`
<img width="940" height="411" alt="image" src="https://github.com/user-attachments/assets/9c808a21-a195-4653-a665-04f14ba09e84" />

## Simulation Commands

    iverilog dff_const1.v tb_dff_const1.v
    ./a.out
    gtkwave tb_dff_const1.vcd

Similarly:

    iverilog dff_const3.v tb_dff_const3.v
    ./a.out
    gtkwave tb_dff_const3.vcd

<img width="940" height="460" alt="image" src="https://github.com/user-attachments/assets/461cd3d2-dcfe-41fa-92ea-60b50ad1b91d" />
<img width="940" height="455" alt="image" src="https://github.com/user-attachments/assets/6dd36059-8aaa-4958-a21a-e137b65481a6" />

## Editing/Copying Files

    gvim dff_const1.v
    cp dff_const1.v dff_const2.v
    gvim dff_const2.v

---
<img width="940" height="380" alt="image" src="https://github.com/user-attachments/assets/631fab6b-56f6-42c3-b276-8d2eb0c4387b" />

# 9. Yosys Flow for Sequential Optimization

A typical synthesis flow is:

    yosys
    read_liberty
    read_verilog dff_const2.v
    synth -top <top_module>
    opt_clean -purge
    abc -liberty
    show

The resulting circuit can be compared with the original circuit to determine whether the sequential constant has been optimized away.

<img width="940" height="552" alt="image" src="https://github.com/user-attachments/assets/c129500b-ceca-4f79-895a-aac5ae9239eb" />

The outputs are:
<img width="940" height="151" alt="image" src="https://github.com/user-attachments/assets/ce664d01-1271-4fd7-a794-65e6601526a1" />

<img width="940" height="763" alt="image" src="https://github.com/user-attachments/assets/770ff7ff-260d-4497-bdd8-066a294ac5a6" />

<img width="940" height="459" alt="image" src="https://github.com/user-attachments/assets/35868f91-24cb-47f6-81e4-53198fe85128" />

<img width="940" height="135" alt="image" src="https://github.com/user-attachments/assets/8770a766-3ee8-4083-9293-429d8609b501" />

---

# 10. Sequential Optimization – Unused Outputs

Sequential optimization can also remove unnecessary counter bits or unused outputs.

## Example: Counter

The Verilog code is:

    module counter_opt(input clk, input reset, output q);

        reg [2:0] count;

        assign q = count[0];

        always @(posedge clk, posedge reset) begin
            if (reset)
                count <= 3'b000;
            else
                count <= count + 1;
        end

    endmodule

Here, the counter is:

    count[2:0]

but only:

    count[0]

is used as the output.

Therefore, the upper bits may be identified as **unused logic**, depending on the synthesis/optimization context.

---
<img width="940" height="507" alt="image" src="https://github.com/user-attachments/assets/9472ed61-fd08-49e2-a983-6d52e58d84f3" />

# 11. Counter Optimization

The notes show a constant-value condition such as:

    count[2:0] = 3'b100

The purpose of the optimization is to identify whether counter bits or sequential logic can be eliminated when they do not affect observable outputs.
<img width="940" height="550" alt="image" src="https://github.com/user-attachments/assets/81365568-3a6f-4720-9b54-bb434e439057" />

<img width="940" height="99" alt="image" src="https://github.com/user-attachments/assets/9e79b264-5d5d-484e-b71d-5245968143b8" />

### Main Idea

If certain counter bits do not affect any observable output or required functionality, synthesis tools may identify them as unnecessary and optimize them.

### Benefits

- Reduced number of flip-flops
- Reduced area
- Reduced power consumption
- Reduced switching activity
- Simplified sequential logic

---

# 12. Yosys Flow for Counter Optimization

A typical synthesis flow is:

    yosys
    read_liberty
    read_verilog counter_opt2.v
    synth -top <top_module>
    dfflibmap
    abc -liberty
    show

The synthesized design can then be compared with the original design.

---

# 13. Important Optimization Commands

| Command | Purpose |
|---|---|
| `read_verilog` | Reads the Verilog design |
| `read_liberty` | Reads the standard-cell library |
| `synth -top` | Performs synthesis for the specified top module |
| `opt_clean -purge` | Removes unused/cleanable logic |
| `dfflibmap` | Maps flip-flops to library cells |
| `abc -liberty` | Performs technology mapping using ABC |
| `show` | Displays the synthesized circuit |
| `iverilog` | Compiles Verilog for simulation |
| `./a.out` | Runs the compiled simulation |
| `gtkwave` | Opens the generated waveform |
| `gvim` | Opens/edits Verilog files |

---

# 14. Quick Revision

## Combinational Optimization

### Main Techniques

1. Constant propagation
2. Boolean logic optimization
   - K-Map
   - Quine–McCluskey

### Main Goals

$$
\boxed{\text{Less Area + Less Power + Better Performance}}
$$

---

## Sequential Optimization

### Basic Technique

- Sequential constant propagation

### Advanced Techniques

- State optimization
- Retiming
- Sequential logic cloning
- Floorplan-based optimization
- Synthesis-based optimization

### Important Idea

If a sequential element is proven to always produce a constant output, it can potentially be removed during synthesis.

---

# 15. Overall Optimization Flow

A typical optimization flow is:

    Verilog RTL
        ↓
    Read Design
        ↓
    Synthesis
        ↓
    Constant Propagation
        ↓
    Optimization / Cleanup
        ↓
    Technology Mapping
        ↓
    ABC
        ↓
    Show / Analyze Optimized Circuit

## Key Commands to Remember

    read_liberty
    read_verilog
    synth -top
    opt_clean -purge
    dfflibmap
    abc -liberty
    show

---

## ⭐ Module 3 – One-Line Summary

> **Combinational and sequential optimization simplifies RTL logic by removing constants, redundant logic, unused states, and unnecessary sequential elements to achieve lower area, lower power, and better performance.**
