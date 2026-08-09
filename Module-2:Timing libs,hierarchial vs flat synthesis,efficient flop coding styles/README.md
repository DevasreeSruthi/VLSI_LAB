# VLSI Synthesis – Timing Libraries, Hierarchical vs Flat Synthesis & Efficient Flop Coding

## 📌 Topics Covered

- Introduction to Timing Libraries
- PVT (Process, Voltage, Temperature)
- Standard Cell Libraries
- Hierarchical vs Flat Synthesis
- Submodules and Module Instantiation
- Flip-Flop Coding Styles
- Synchronous Reset
- Asynchronous Reset
- Yosys Synthesis Flow
- Technology Mapping
- `dfflibmap`
- `abc`
- Netlist Generation

---

# 1. Introduction to Timing Libraries

A **timing library** contains information about standard cells used during digital circuit synthesis and timing analysis.

A library provides information such as:

- Cell functionality
- Delay
- Power
- Area
- Timing characteristics
- Input capacitance
- Output drive strength

## Common Library Types

Libraries are generally characterized for different operating conditions:

- **Slow Library**
- **Fast Library**
- **Typical Library**

The correct library must be selected according to the required operating conditions of the design.

---

# 2. Sky130 Standard Cell Library

The library used in this synthesis exercise is:

~~~text
sky130_fd_sc_hd_tt_025C_1v80.lib
~~~
<img width="940" height="628" alt="image" src="https://github.com/user-attachments/assets/f97e7839-c95a-4fc1-8ce4-638314e9fd17" />

## Library Information

| Parameter | Value |
|---|---|
| Technology | CMOS |
| Process | 130 nm |
| Library | Sky130 High Density |
| Delay Model | Lookup Table |
| Time Unit | ns |
| Voltage Unit | V |
| Power Unit | nW |
| Current Unit | mA |
| Resistance Unit | kΩ |
| Capacitance Unit | pF |

## Library Name Breakdown

~~~text
sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

- `sky130` → SkyWater 130 nm technology
- `fd_sc` → Fully Digital Standard Cell
- `hd` → High Density
- `tt` → Typical Process
- `025C` → Temperature = 25°C
- `1v80` → Supply Voltage = 1.80 V
- `.lib` → Liberty timing library file

---

# 3. PVT – Process, Voltage and Temperature

PVT represents the three major conditions that affect circuit behaviour.

~~~text
P → Process
V → Voltage
T → Temperature
~~~

These variations are important because a design must work reliably under different manufacturing and operating conditions.

## Process

Process variation occurs due to variations during semiconductor fabrication.

Different chips manufactured using the same process may have slightly different characteristics.

## Voltage

Supply voltage variations affect the behaviour and timing of the circuit.

Changes in voltage can affect:

- Delay
- Power
- Switching behaviour
- Circuit reliability

## Temperature

Semiconductor devices are sensitive to temperature.

Temperature variations can affect:

- Propagation delay
- Leakage current
- Power consumption
- Overall circuit performance

## Typical Corner Used

~~~text
tt_025C_1v80
~~~

means:

~~~text
tt   → Typical Process
025C → 25°C
1v80 → 1.80 V
~~~

The goal is to make the silicon work reliably despite variations in process, voltage and temperature.

---

# 4. Hierarchical vs Flat Synthesis

## Hierarchical Design

In hierarchical design, a large design is divided into smaller modules or submodules.

Example:

~~~text
Top Module
│
├── Submodule 1
│
└── Submodule 2
~~~

For example:

~~~text
submodule 1 → AND gate
submodule 2 → OR gate
~~~

The top module instantiates the submodules.

## Advantages

- Better organization
- Easier debugging
- Easier reuse of modules
- Suitable for large designs
- Preserves module boundaries

---
<img width="940" height="372" alt="image" src="https://github.com/user-attachments/assets/7df5c252-1dfc-4e1f-b36b-f6e019aa5120" />

<img width="940" height="748" alt="image" src="https://github.com/user-attachments/assets/bddeaae9-0e2e-4c6c-87ce-5304ca7436d2" />


# 5. Flat Synthesis

In flat synthesis, the hierarchy is removed and the complete design is treated as a single logical structure.

The Yosys command used for flattening is:

~~~text
flatten
~~~
<img width="940" height="371" alt="image" src="https://github.com/user-attachments/assets/32da7706-a565-4df8-90dc-eb852a584fb0" />

## Why Flatten?

Flattening allows the synthesis tool to optimize logic across module boundaries.

This can help with:

- Logic optimization
- Removing redundant logic
- Better technology mapping
- Potentially improved area and timing

## Difference

~~~text
Hierarchical Synthesis
        ↓
Modules remain separate
        ↓
Module boundaries preserved


Flat Synthesis
        ↓
Hierarchy removed
        ↓
Entire design optimized together
~~~

---

# 6. Submodules and Module Instantiation

A large RTL design can be divided into smaller modules called submodules.

The top-level module can instantiate these submodules to create a hierarchical design.

## Example

~~~verilog
module submodule(
    input a,
    input b,
    output y
);

assign y = a & b;

endmodule
~~~

The submodule can be instantiated inside a top module:

~~~verilog
module top(
    input a,
    input b,
    output y
);

submodule u1 (
    .a(a),
    .b(b),
    .y(y)
);

endmodule
~~~

Here:

- `top` is the top-level module.
- `submodule` is the child module.
- `u1` is the instance name.
- The ports are connected using named port connections.

---
<img width="940" height="657" alt="image" src="https://github.com/user-attachments/assets/6049960b-3777-47d4-a89f-5d6fbee149c0" />

# 7. Flip-Flops

Flip-flops are sequential storage elements.

They store the state of a circuit and change their output according to the clock and control signals.

A flip-flop generally has:

~~~text
D     → Data Input
CLK   → Clock
RESET → Reset
Q     → Output
~~~

Flip-flops are important because they store state between clock cycles.

---

# 8. Why Proper Flip-Flop Coding Matters

The way a flip-flop is written in Verilog affects the hardware inferred by the synthesis tool.

Poor or incorrect coding may result in:

- Unwanted hardware
- Incorrect reset behaviour
- Difficult timing analysis
- Unintended latches or logic

Therefore, efficient RTL coding is important for good synthesis results.

---

# 9. Synchronous Reset

In a **synchronous reset**, the reset is checked only at the active clock edge.

## Verilog Example

~~~verilog
always @(posedge clk)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
~~~

## Operation

~~~text
Clock Edge
    ↓
Check Reset
    ↓
Reset = 1 → Q = 0
Reset = 0 → Q = D
~~~

The reset does not immediately change `Q`.

It takes effect only when the clock edge occurs.

---

# 10. Asynchronous Reset

In an **asynchronous reset**, the reset can affect the flip-flop independently of the clock.

## Verilog Example

~~~verilog
always @(posedge clk or posedge reset)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
~~~

## Operation

~~~text
Reset asserted
      ↓
Q becomes 0 immediately

OR

Clock edge occurs
      ↓
If reset = 0
      ↓
Q follows D
~~~

## Key Difference

| Synchronous Reset | Asynchronous Reset |
|---|---|
| Reset depends on clock | Reset does not depend on clock |
| Reset checked at clock edge | Reset acts immediately |
| `posedge clk` | `posedge clk or posedge reset` |
| Easier timing control | Useful for immediate initialization |

---

# 11. Flip-Flop Initialization

A flip-flop does not automatically have a known value when simulation starts.

Without reset, the initial value may be unknown:

~~~text
Q = X
~~~

Therefore, reset is commonly used to initialize the flip-flop.

For example:

~~~verilog
if (reset)
    q <= 1'b0;
~~~

This ensures that the flip-flop starts from a known state.

---

# 12. Yosys Synthesis Flow

The basic Yosys synthesis flow used in this exercise is:

~~~text
RTL Verilog
     ↓
read_verilog
     ↓
hierarchy
     ↓
flatten
     ↓
dfflibmap
     ↓
abc
     ↓
write_verilog
~~~

---

# 13. Read Verilog File

First, load the RTL Verilog design.

~~~yosys
read_verilog diff_async_set.v
~~~

This reads the Verilog source file into Yosys.

---



# 14. Mapping Flip-Flops – `dfflibmap`

The command:

~~~yosys
dfflibmap -liberty sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

maps the flip-flops inferred by Yosys to available flip-flop cells in the Sky130 standard-cell library.

## Purpose

~~~text
RTL Flip-Flop
      ↓
dfflibmap
      ↓
Sky130 Flip-Flop Cell
~~~

This ensures that the synthesized design uses actual cells available in the target technology library.

---

# 15. Technology Mapping using ABC

Use:

~~~yosys
abc -liberty sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

ABC performs logic optimization and technology mapping.

It maps the logical representation of the design to standard cells available in the Liberty library.

## Flow

~~~text
Optimized Logic
      ↓
ABC
      ↓
Technology Mapping
      ↓
Sky130 Standard Cells
~~~

---

# 16. Difference Between `dfflibmap` and `abc`

## `dfflibmap`

Used mainly for mapping flip-flops to library-specific sequential cells.

~~~text
dfflibmap -liberty sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

## `abc`

Used for combinational logic optimization and technology mapping.

~~~text
abc -liberty sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

Both are important parts of the synthesis process.

---

# 17. Generate Synthesized Netlist

After synthesis and mapping, the resulting design can be written to a Verilog netlist.

## Example

~~~yosys
write_verilog -noattr synthesized.v
~~~

The `-noattr` option removes synthesis attributes from the generated Verilog.

The output file contains the synthesized representation of the design.

---

# 18. Complete Example Yosys Flow

A complete synthesis sequence can be written as:

~~~yosys
read_verilog diff_async_set.v

synth -top async_set

dfflibmap -liberty sky130_fd_sc_hd_tt_025C_1v80.lib

abc -liberty sky130_fd_sc_hd_tt_025C_1v80.lib

write_verilog -noattr synthesized.v
~~~

---
<img width="940" height="475" alt="image" src="https://github.com/user-attachments/assets/df8e41d8-8bd0-4ec1-947f-bcf3baf87e21" />

<img width="940" height="343" alt="image" src="https://github.com/user-attachments/assets/1f26c374-1871-48de-8861-d377314754f8" />

# 19. Important Yosys Commands

| Command | Purpose |
|---|---|
| `read_verilog` | Reads Verilog RTL |
| `hierarchy` | Sets and checks design hierarchy |
| `flatten` | Removes module hierarchy |
| `dfflibmap` | Maps flip-flops to library cells |
| `abc` | Optimizes and maps combinational logic |
| `write_verilog` | Writes synthesized Verilog |

---

# 20. Hierarchical Synthesis vs Flat Synthesis

## Hierarchical

~~~text
Top Module
    │
    ├── Submodule 1
    │       └── Logic
    │
    └── Submodule 2
            └── Logic
~~~

The hierarchy is preserved.

## Flat

~~~text
Top Module
    │
    └── Complete optimized logic
~~~

All modules are combined and optimized together.

## Main Idea

~~~text
Hierarchical → Preserve structure

Flat → Remove hierarchy → More cross-module optimization
~~~

---

# 21. Efficient Flip-Flop Coding – Summary

## Synchronous Reset

~~~verilog
always @(posedge clk)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
~~~
<img width="940" height="443" alt="image" src="https://github.com/user-attachments/assets/29df3dbf-4e41-4a65-adf0-a71cfdc470bb" />

<img width="940" height="349" alt="image" src="https://github.com/user-attachments/assets/09c1533d-eaf5-4173-9d71-ad13a057cfa2" />

## Asynchronous Reset

~~~verilog
always @(posedge clk or posedge reset)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
~~~

Use the reset style according to the required hardware architecture.

---
<img width="940" height="458" alt="image" src="https://github.com/user-attachments/assets/f2a4c378-42bf-4bdf-aa93-4b315ed92feb" />

<img width="940" height="345" alt="image" src="https://github.com/user-attachments/assets/7c8c6ced-fcfb-4f13-939e-cfc26502f94d" />

# 22. Overall Learning

The synthesis process converts RTL code into technology-specific standard-cell logic.

~~~text
Verilog RTL
    ↓
Read RTL
    ↓
Hierarchy Analysis
    ↓
Process RTL
    ↓
Flatten / Preserve Hierarchy
    ↓
Logic Optimization
    ↓
Flip-Flop Mapping
    ↓
Combinational Technology Mapping
    ↓
Standard Cell Netlist
~~~

The timing library and PVT corner determine how the standard cells are characterized for a particular operating condition.

---

# 23. Technology and Operating Conditions Used

For this synthesis exercise:

~~~text
Technology : Sky130
Process    : Typical
Temperature: 25°C
Voltage    : 1.80 V
Library    : sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

The selected library corresponds to:

~~~text
tt   → Typical Process
025C → 25°C
1v80 → 1.80 V
~~~

The goal is to make the silicon work reliably under the selected operating condition.

---

# 24. Key Takeaways

The important concepts learned from this exercise are:

1. **Timing libraries** provide information about cell functionality, timing, power, area, and electrical characteristics.

2. **PVT** represents Process, Voltage, and Temperature variations that influence circuit performance.

3. The **Sky130 standard-cell library** provides technology-specific cells for implementing the synthesized design.

4. **Hierarchical synthesis** preserves the module structure of the design.

5. **Flat synthesis** removes hierarchy and allows broader optimization across module boundaries.

6. **Flip-flops** are sequential storage elements used to retain circuit state.

7. **Synchronous reset** operates only at the active clock edge.

8. **Asynchronous reset** can operate independently of the clock.

9. Proper **RTL coding style** is important for ensuring that the desired hardware is inferred by the synthesis tool.

10. The Yosys `proc` command converts procedural RTL into an internal representation suitable for synthesis.

11. The `flatten` command removes design hierarchy.

12. The `dfflibmap` command maps inferred flip-flops to available library-specific cells.

13. The `abc` command performs combinational logic optimization and technology mapping.

14. The `write_verilog` command generates the synthesized Verilog netlist.

15. The final synthesized design is represented using standard cells from the selected technology library.

---

# 29. Overall Synthesis Flow

The complete synthesis flow can be summarized as:

~~~text
RTL Design
    ↓
Yosys RTL Processing
    ↓
Hierarchy Analysis
    ↓
Logic Optimization
    ↓
Flip-Flop Mapping
    ↓
Technology Mapping
    ↓
Sky130 Standard Cells
    ↓
Synthesized Netlist
~~~

The synthesis process transforms the original RTL description into a technology-specific implementation.

---

# 25. Conclusion

This exercise provided an understanding of the complete RTL-to-netlist synthesis process using **Yosys** and the **Sky130 standard-cell library**.

The experiment covered:

- Timing libraries
- PVT conditions
- Sky130 standard cells
- Hierarchical synthesis
- Flat synthesis
- Submodules and module instantiation
- Flip-flop coding styles
- Synchronous reset
- Asynchronous reset
- Flip-flop initialization
- RTL processing
- Logic optimization
- Flip-flop mapping
- Technology mapping
- Synthesized netlist generation
- Interesting optimisations
- <img width="940" height="558" alt="image" src="https://github.com/user-attachments/assets/0ddde76f-5c92-4caa-86f2-233526acf9d3" />

<img width="940" height="982" alt="image" src="https://github.com/user-attachments/assets/3cff743e-4558-451e-a565-cb601f6cd857" />

<img width="940" height="485" alt="image" src="https://github.com/user-attachments/assets/135a2a3b-cdc8-4c74-a692-7abcff530903" />

<img width="940" height="366" alt="image" src="https://github.com/user-attachments/assets/20e10b78-4e7e-4366-bb96-f9e935a7b5dc" />

The complete synthesis flow can be summarized as:

~~~text
RTL Design
    ↓
Read RTL
    ↓
Hierarchy Analysis
    ↓
Process RTL
    ↓
Flatten / Preserve Hierarchy
    ↓
Logic Optimization
    ↓
Flip-Flop Mapping
    ↓
Combinational Technology Mapping
    ↓
Standard Cell Netlist
~~~

The timing library and PVT corner determine how the standard cells are characterized for a particular operating condition.

For this exercise:

~~~text
Technology : Sky130
Process    : Typical
Temperature: 25°C
Voltage    : 1.80 V
Library    : sky130_fd_sc_hd_tt_025C_1v80.lib
~~~

The experiment demonstrates how a Verilog RTL design is transformed into a technology-specific standard-cell netlist while considering the characteristics of the selected timing library and operating conditions.

## 📚 Learning Outcome

The experiment demonstrates the fundamental concepts of **RTL synthesis, timing libraries, PVT characterization, hierarchy management, sequential logic inference, technology mapping, and generation of a technology-specific standard-cell netlist using Yosys and the Sky130 library.**
