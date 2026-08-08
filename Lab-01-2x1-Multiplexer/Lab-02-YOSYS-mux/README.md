# RTL Design and Synthesis using Yosys – SKY130

## Overview

This repository contains my learning and practical work on **RTL design, logic synthesis, technology mapping, and netlist verification** using **Yosys** and the **SKY130 standard-cell library**.

The work was carried out as part of an **RTL-to-Netlist / Digital VLSI synthesis flow** using a Linux virtual machine.

The main objective was to understand how an RTL design written in Verilog is converted into a technology-mapped gate-level netlist using cells available in a `.lib` (Liberty) library.

---

# 1.RTL Design

RTL (Register Transfer Level) is a behavioral description of the required digital circuit.

### Example

```verilog
module good_mux(i0, i1, sel, y);

input i0, i1, sel;
output y;

assign y = sel ? i1 : i0;

endmodule
```

This describes a **2:1 multiplexer**.

The RTL describes the required functionality; synthesis determines how that functionality can be implemented using available logic cells.

---

# 2. What is Synthesis?

Synthesis is the process of converting RTL into a **gate-level representation**.

## Basic Flow

```text
        RTL / Verilog
              |
              v
           Yosys
              |
       RTL synthesis
              |
              v
       Gate-level design
              |
      Technology mapping
              |
              v
   SKY130 standard cells
              |
              v
          Netlist
```

**Yosys** is used as the synthesis tool in this flow.

---

# 3. What is a Netlist?

A **netlist** is a structural representation of a digital circuit.

It describes:

- Which cells/gates are used
- How the cells are connected
- Inputs and outputs
- Internal signals/nets

After technology mapping, the netlist can contain cells from the selected standard-cell library.

For example, a 2:1 MUX may be represented using a SKY130 MUX cell or, depending on the synthesis/mapping flow, using a combination of NAND, inverter, AOI/OAI, buffer, and other standard cells.

---

# 4. What is a `.lib` File?

A `.lib` file is a **Liberty-format standard-cell library**.

It contains information about cells such as:

- Cell names
- Logic/function
- Input/output pins
- Timing information
- Propagation delays
- Power information
- Area
- Different cell drive strengths

## Example SKY130 Library Cell

```text
sky130_fd_sc_hd__mux2_1
```

The suffix `_1` represents a particular drive-strength variant.

A library can contain several versions of the same logical function with different speed, area, and power characteristics.

---

# 5. Why Are Different Cell Flavours Needed?

Different standard cells can implement the same logical function but have different characteristics.

## Faster Cells

- Larger/wider transistors
- Can charge/discharge capacitances faster
- Lower cell delay
- Usually require more area
- Usually have higher power consumption

## Slower Cells

- Smaller transistors
- Higher delay
- Lower area
- Lower power

Therefore, using only the fastest cells is not always optimal.

The synthesis tool needs to select an appropriate combination of cells depending on design requirements and constraints.

---

# 6. Timing and Maximum Clock Frequency

For a flip-flop-to-flip-flop path:

```text
FF A  --->  Combinational Logic  --->  FF B
                  |
                  v
             Propagation delay
```

The basic setup-time relationship is:

```text
Tclk >= Tcq(A) + Tcomb + Tsetup(B)
```

Where:

- `Tcq(A)` = clock-to-Q delay of flip-flop A
- `Tcomb` = propagation delay through combinational logic
- `Tsetup(B)` = setup time of flip-flop B

The maximum clock frequency is approximately:

```text
Fmax = 1 / Tclk(min)
```

Therefore:

```text
Lower critical-path delay
        ↓
Lower minimum clock period
        ↓
Higher maximum frequency
        ↓
Better performance
```

---

# 7. Why Do We Need Slower Cells?

Fast cells are not always sufficient.

For **setup timing**, faster cells are useful because they reduce propagation delay.

For **hold timing**, however, the data path may become too fast.

A simplified hold relationship is:

```text
Thold(B) <= Tcq(A) + Tcomb
```

If data reaches the receiving flip-flop too quickly, a hold violation can occur.

Therefore, a real physical-design flow may use a mixture of:

- Fast cells for critical paths
- Slower cells where extra delay is required
- Different drive strengths depending on load and timing

This creates a trade-off between:

```text
Performance ↔ Area ↔ Power ↔ Timing
```

---

# 8. Cell Selection

The synthesis tool performs **technology mapping** to select cells from the available `.lib`.

The objective is not simply:

> "Use the fastest cell everywhere."

Instead, the tool tries to obtain an implementation that satisfies the required design constraints while balancing:

- Timing
- Area
- Power
- Cell availability
- Drive strength
- Load

---

# 9. Yosys

**Yosys** is an open-source RTL synthesis framework.

In this work, Yosys was used to:

- Read Verilog RTL
- Check the design
- Perform synthesis
- Perform technology mapping
- Generate a mapped netlist
- Generate a graphical representation of the synthesized design

---

# 10. Important Yosys Commands

## Read the RTL

```text
read_verilog good_mux.v
```

This reads the Verilog RTL design.

## Synthesize the Design

```text
synth -top good_mux
```

This performs synthesis and uses `good_mux` as the top module.

## Technology Mapping Using ABC

```text
abc -liberty /home/vsduser/VLSI/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

The `-liberty` option tells ABC to use the specified Liberty library for technology mapping.


## Display the Synthesized Circuit

```text
show
```

This generates a graphical representation of the current design.
<img width="940" height="531" alt="image" src="https://github.com/user-attachments/assets/f1e513b3-e35b-425f-a2e4-22808fe93aed" />


## Write the Synthesized Netlist

A typical command is:

```text
write_verilog -noattr good_mux_netlist.v
```

The exact output filename can be changed according to the project.

---

# 11. ABC Technology Mapping

**ABC** is used by Yosys for logic optimization and technology mapping.

The basic idea is:

```text
RTL
 |
 v
Yosys synthesis
 |
 v
Logic representation
 |
 v
ABC optimization / mapping
 |
 v
Cells from SKY130 .lib
 |
 v
Technology-mapped netlist
```

For example, my mapped `good_mux` design produced a SKY130 cell:

```text
sky130_fd_sc_hd__mux2_1
```

with the inputs:

```text
i0
i1
sel
```

and output:

```text
y
```

Depending on the library and ABC/Yosys flow, the same logical MUX function may also be implemented using multiple cells such as NAND, inverter, AOI/OAI, and buffer cells.

The important point is that the mapped circuit must preserve the functionality of the RTL.

---

# 12. Example: good_mux

## RTL Functionality

```text
        i0 ─────┐
                │
        i1 ─────┤  2:1 MUX ──── y
                │
       sel ─────┘
```

The logic is:

```text
y = sel ? i1 : i0
```

or equivalently:

```text
y = (~sel & i0) | (sel & i1)
```

After technology mapping, the design can be represented using SKY130 standard cells.

In my result, ABC mapped the design to:

```text
sky130_fd_sc_hd__mux2_1
```

---

# 13. Verifying the Synthesized Design

Synthesis should not change the intended functionality.

The synthesized netlist can be verified using a simulator.

## Verification Flow

```text
RTL / Netlist
      |
      | + Testbench
      v
   Icarus Verilog
      |
      v
     VCD
      |
      v
   GTKWave
      |
      v
Compare waveforms
```

The primary inputs and primary outputs remain compatible, so the same testbench can be used to compare the RTL and synthesized design, provided the simulation setup and cell models are appropriate.

---

# 14. RTL-to-Netlist-to-Waveform Flow

The complete learning flow is:

```text
                Verilog RTL
                     |
                     v
                  Yosys
                     |
             RTL synthesis
                     |
                     v
              Logic network
                     |
                     v
                   ABC
                     |
             Technology mapping
                     |
                     v
             SKY130 standard cells
                     |
                     v
                Netlist.v
                     |
                     v
              Icarus Verilog
                     |
                     v
                  VCD file
                     |
                     v
                 GTKWave
                     |
                     v
              Waveform check
```

---

# 15. Main Concepts Learned

Through this practical work, I learned:

- RTL design and behavioral representation
- Verilog basics
- RTL synthesis
- Yosys synthesis flow
- Netlist generation
- Liberty `.lib` files
- Standard-cell libraries
- Technology mapping
- ABC
- SKY130 standard cells
- Cell flavours and drive strengths
- Timing and propagation delay
- Setup and hold concepts
- Fast vs. slow cells
- Area-power-performance trade-offs
- Netlist visualization using `show`
- Verilog netlist simulation
- 

---

# 16. Practical Result
<img width="940" height="548" alt="image" src="https://github.com/user-attachments/assets/56ff77c6-3385-4743-8373-8183de2b4e84" />


For the `good_mux` example, the synthesized design was successfully technology-mapped using the SKY130 library.

The graphical output showed a SKY130 standard cell:

```text
sky130_fd_sc_hd__mux2_1
```

implementing the **2:1 multiplexer**.

The generated netlist contains the corresponding standard-cell representation and can be used for further simulation and physical-design stages.
