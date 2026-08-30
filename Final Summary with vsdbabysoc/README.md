# From Application Software to Hardware

The overall flow shows how a high-level program written by a programmer is converted into machine-level instructions that can be executed by hardware.

```text
┌──────────────────────────────┐
│      APPLICATION SOFTWARE    │
│                              │
│       C / C++ / Java         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          COMPILER            │
│                              │
│ Converts high-level code     │
│ into low-level instructions  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        INSTRUCTIONS          │
│                              │
│        Instruction 1         │
│        Instruction 2         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          ASSEMBLER           │
│                              │
│ Converts assembly language   │
│ into machine code            │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        MACHINE CODE          │
│                              │
│         1010110011           │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│           HARDWARE           │
│                              │
│ Executes the machine code    │
└──────────────────────────────┘
```
# Logic Synthesis to Physical Design

```text
┌──────────────────────────────┐
│       RTL DESIGN             │
│                              │
│       Verilog / HDL          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       LOGIC SYNTHESIS        │
│                              │
│ RTL → Gate-level Netlist     │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        FLOOR PLANNING        │
│                              │
│ Defines chip/core area and   │
│ major block locations        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│          PLACEMENT           │
│                              │
│ Places standard cells        │
│ inside the chip area         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│            CLOCK             │
│                              │
│ Clock distribution is        │
│ planned and optimized        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│           ROUTING            │
│                              │
│ Connects the placed cells    │
│ using metal interconnects    │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│       PHYSICAL LAYOUT        │
│                              │
│ Final physical representation│
│ of the chip                  │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│      MAGIC LAYOUT VIEWER     │
│                              │
│ Used to view and inspect     │
│ the generated layout         │
└──────────────────────────────┘
```
# VLSI LAB – Modules 1 to 5

This repository contains the practical learning and experiments carried out during the VLSI Laboratory. The modules cover the complete front-end VLSI flow from RTL design and simulation to synthesis, optimization, gate-level simulation, and synthesis-aware RTL coding.

---

# Module 1 – Icarus Verilog, Design and Testbench

## Simulator

A **simulator** is a software tool used to verify the behavior of a digital circuit before implementing it in hardware. It applies input signals to the design and observes how the outputs change with time.

## RTL

**RTL (Register Transfer Level)** is a method of describing a digital circuit in terms of the flow of data between registers and the logic operations performed on that data.

RTL is commonly written using **Verilog HDL** or **VHDL**.

## Icarus Verilog

**Icarus Verilog (`iverilog`)** is an open-source Verilog simulator. It compiles Verilog source files and generates an executable simulation file.

### Uses of Icarus Verilog

- Compiling Verilog designs
- Compiling testbenches
- Running simulations
- Generating waveform files such as `.vcd`

## Design

The **design** is the actual hardware circuit being developed. It is described using Verilog HDL.

For example, a 2:1 multiplexer can be represented as:

```verilog
assign y = sel ? i1 : i0;
```

Here, the Verilog code represents the hardware design.

## Testbench

A **testbench** is a Verilog program used to verify the functionality of a design.

A testbench:

- Provides input values to the design
- Changes inputs at different times
- Observes outputs
- Checks whether the design behaves correctly

A testbench is mainly used for **verification** and does not normally represent physical hardware.

## Working of a Simulator

The basic simulation process is:

```text
Verilog Design
      ↓
   Testbench
      ↓
Icarus Verilog
      ↓
   Simulation
      ↓
  VCD Waveform
      ↓
    GTKWave
      ↓
Waveform Verification
```
<img width="1294" height="616" alt="image" src="https://github.com/user-attachments/assets/4f9e1820-98dd-4c7c-baa7-11730b415729" />

The simulator executes the design according to the inputs generated by the testbench. The signal changes are stored in a waveform file and can be viewed using GTKWave.

## Icarus Verilog Commands

### Compile the design and testbench

```bash
iverilog design.v tb.v
```

This generates the default simulation executable:

```text
a.out
```

### Run the simulation

```bash
./a.out
```

### Open the waveform

If the testbench generates a VCD file:

```bash
gtkwave dump.vcd
```

Therefore:

```text
iverilog  → Compilation
./a.out   → Simulation
gtkwave   → Waveform Viewing
```

## GTKWave

**GTKWave** is an open-source waveform viewer used to analyze simulation results.

It can display:

- Inputs
- Outputs
- Clock
- Reset
- Internal signals

GTKWave helps verify whether the circuit produces the expected output for different input conditions.

---
<img width="940" height="504" alt="image" src="https://github.com/user-attachments/assets/9749d9eb-d82b-42a6-a056-1e7434aa8d5f" />

# Module 2 – Introduction to Yosys

## Synthesis

**Synthesis** is the process of converting an RTL description into a gate-level representation of hardware.

```text
RTL Verilog
     ↓
  Synthesis
     ↓
Logic Gates / Standard Cells
     ↓
Gate-Level Netlist
```

Synthesis also performs logic optimization and converts the design into hardware structures suitable for the target technology.

## Yosys

**Yosys** is an open-source framework for RTL synthesis.

Yosys can:

- Read Verilog RTL
- Elaborate the design
- Optimize logic
- Detect flip-flops
- Map logic to standard cells
- Generate a gate-level netlist
<img width="940" height="531" alt="image" src="https://github.com/user-attachments/assets/b0d861d1-cd25-404c-b493-1b6379ed6ab6" />

## Verification

**Verification** is the process of checking whether a designed circuit performs according to its intended specification.

For example, for a 2:1 multiplexer:

```text
sel = 0 → y = i0
sel = 1 → y = i1
```

## Netlist

A **netlist** is a description of a circuit in terms of hardware components and their connections.

After synthesis, the RTL is converted into a gate-level netlist containing elements such as:

- AND gates
- OR gates
- Inverters
- Multiplexers
- Flip-flops
- Technology-specific standard cells

## `.lib` Files

A **Liberty `.lib` file** is a standard-cell timing and characterization library used during synthesis and timing analysis.

It contains information about:

- Cell functionality
- Area
- Input capacitance
- Delay
- Timing characteristics
- Power information
- Setup and hold requirements
- Drive strengths

The `.lib` file allows synthesis tools to understand the characteristics of the available standard cells.

---

# Module 2 – Timing Libraries, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles

## Timing Libraries

A **timing library** contains timing and electrical characteristics of standard cells.

It provides information such as:

- Cell delay
- Setup time
- Hold time
- Clock-to-Q delay
- Input capacitance
- Output drive strength
- Power characteristics

Timing libraries are generally provided in **Liberty `.lib` format**.

## SKY130

**SKY130** is an open-source 130 nm semiconductor technology/process design kit.

It provides:

- Standard cells
- Digital logic cells
- Flip-flops
- Timing libraries
- Technology information
- Models required for digital IC design

The SKY130 libraries can be used by Yosys and other open-source VLSI tools for technology mapping.

## PVT

PVT stands for:

### P – Process

Represents variations in the semiconductor manufacturing process.

Examples:

- Fast process
- Slow process
- Typical process

### V – Voltage

Represents the operating supply voltage.

Voltage variations can affect:

- Delay
- Power
- Circuit performance

### T – Temperature

Represents the operating temperature.

Temperature variations can affect transistor behavior and circuit timing.

Therefore:

```text
P = Process
V = Voltage
T = Temperature
```
<img width="940" height="628" alt="image" src="https://github.com/user-attachments/assets/cffaa87f-c0e5-41f0-a3dd-7098034c7d58" />

PVT analysis is important because real chips operate under different process, voltage and temperature conditions.

<img width="508" height="455" alt="image" src="https://github.com/user-attachments/assets/b76a52bf-108c-4b14-8225-ddc34a221578" />

## Faster Cells

**Faster cells** provide lower propagation delay and stronger drive capability.

### Advantages

- Improve timing
- Reduce critical-path delay
- Help achieve higher operating frequency

### Disadvantages

- Usually consume more area
- Can consume more power

## Slow Cells

**Slow cells** have relatively higher delay and weaker drive capability.

They can be useful when:

- Timing requirements are less demanding
- Area needs to be reduced
- Power needs to be controlled
- Hold-time problems need to be addressed

## Selection of Cells

Standard cells should not be selected only based on speed.

Cell selection involves a trade-off between:

```text
Timing ↔ Area ↔ Power
```

Faster cells may be used on critical paths, while smaller or slower cells may be sufficient on non-critical paths.

The objective is to select the most appropriate cell for the required design constraints.

---

# Hierarchical vs Flat Synthesis

## Hierarchical Synthesis

In **hierarchical synthesis**, the design hierarchy is preserved.

```text
Top Module
 ├── Module A
 ├── Module B
 └── Module C
```

### Advantages

- Easier design management
- Useful for large designs
- Preserves module boundaries
- Easier debugging
- 
<img width="940" height="372" alt="image" src="https://github.com/user-attachments/assets/a1ec7f22-d450-4be8-ac01-4f215d49122b" />

## Flat Synthesis

In **flat synthesis**, the hierarchy is removed or flattened and the complete design is treated as one large logic structure.

```text
Multiple Modules
       ↓
     Flatten
       ↓
Single Logic Representation
```

### Advantages

- Allows optimization across module boundaries
- Can provide better area/timing in some cases

### Disadvantages

- Larger design representation
- More difficult to debug
- Useful hierarchy information may be lost

<img width="940" height="371" alt="image" src="https://github.com/user-attachments/assets/00d3e4d9-9622-41f5-bcb4-5482e6aa1cc7" />

---

# Flip-Flops

A **flip-flop** is a sequential digital circuit capable of storing one bit of information.

A D flip-flop stores the value of the D input at the active clock edge.

```text
D → D Flip-Flop → Q
       ↑
     Clock
```

Flip-flops are widely used in:

- Registers
- Counters
- State machines
- Pipelines
- Sequential circuits

## Synchronous Reset

A **synchronous reset** is activated only at the active clock edge.

```verilog
always @(posedge clk)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
```

The reset affects the flip-flop only when the clock edge occurs.

## Asynchronous Reset

An **asynchronous reset** can affect the flip-flop independently of the clock.

```verilog
always @(posedge clk or posedge reset)
begin
    if (reset)
        q <= 1'b0;
    else
        q <= d;
end
```

The output can reset immediately when `reset` becomes active.

---

# Yosys Synthesis Flow

The general Yosys synthesis flow is:

```text
Read RTL
   ↓
Elaborate Design
   ↓
Synthesis
   ↓
Map Flip-Flops
   ↓
Technology Mapping
   ↓
Write Gate-Level Netlist
```

## `read_verilog`

```yosys
read_verilog design.v
```

Reads the Verilog RTL design into Yosys.

## `synth -top`

```yosys
synth -top top_module
```

Performs synthesis with the specified module as the top-level module.

## `dfflibmap`

```yosys
dfflibmap -liberty sky130.lib
```

Maps generic flip-flops to appropriate technology-specific flip-flop cells available in the target library.

## `abc -liberty`

```yosys
abc -liberty sky130.lib
```

ABC performs logic optimization and technology mapping using the specified Liberty library.

## `write_verilog`

```yosys
write_verilog netlist.v
```

Writes the synthesized design into a Verilog gate-level netlist.

## Complete Synthesis Flow

```yosys
read_verilog design.v
synth -top top_module
dfflibmap -liberty sky130.lib
abc -liberty sky130.lib
write_verilog netlist.v
```

The result is a technology-mapped gate-level Verilog netlist.

---

# Module 3 – Combinational and Sequential Optimizations

## Combinational Optimization

**Combinational optimization** is the process of simplifying combinational logic while maintaining the same functionality.

Examples of combinational circuits:

- Multiplexers
- Adders
- Subtractors
- Encoders
- Decoders
- Logic gates

Optimization aims to reduce:

- Area
- Power
- Delay
- Number of gates

## Sequential Optimization

**Sequential optimization** improves sequential circuits while maintaining their required behavior.

Sequential circuits contain storage elements such as flip-flops.

Examples:

- Registers
- Counters
- FSMs
- Pipelines

Optimization techniques include:

- Removing redundant logic
- Flip-flop optimization
- State optimization
- Retiming
- Reducing unnecessary hardware

## State Optimization

**State optimization** is the process of reducing or simplifying the number of states in a sequential circuit, especially in a Finite State Machine (FSM), while maintaining the same external behavior.

If two states have equivalent outputs and future behavior, they may sometimes be combined.

### Advantages

- Reduced hardware
- Fewer flip-flops
- Reduced area
- Potential timing improvement

---

# Optimization Conditions in Yosys

## `read_verilog`

```yosys
read_verilog design.v
```

Reads the RTL Verilog file.

## `read_liberty`

```yosys
read_liberty -lib sky130.lib
```

Reads the Liberty standard-cell library.

## `synth -top`

```yosys
synth -top top_module
```

Performs synthesis with the specified top module.

## `opt_clean -purge`

```yosys
opt_clean -purge
```

Removes unused or redundant cells and wires from the synthesized design.

The `-purge` option allows more aggressive removal of unused elements.

## `dfflibmap`

```yosys
dfflibmap -liberty sky130.lib
```

Maps flip-flops to technology-specific flip-flop cells.

## `abc -liberty`

```yosys
abc -liberty sky130.lib
```

Performs logic optimization and maps combinational logic to standard cells using the Liberty library.

## `show`

```yosys
show
```

Displays the synthesized logic as a graphical representation.

---

# Module 4 – GLS, Blocking vs Non-Blocking and Synthesis-Simulation Mismatch

## GLS

**GLS (Gate-Level Simulation)** is the simulation of a synthesized gate-level netlist instead of the original RTL design.

### RTL Simulation

```text
RTL Verilog
     ↓
  Simulator
     ↓
   Output
```

### Gate-Level Simulation

```text
Gate-Level Netlist
       ↓
    Simulator
       ↓
     Output
```

## Why GLS?

GLS is performed to verify that the synthesized circuit behaves as expected.

It can detect:

- Synthesis-related problems
- Incorrect reset behavior
- RTL-to-netlist differences
- Incorrect cell mapping
- Synthesis-simulation mismatches
- Timing-related problems in timing GLS

<img width="1238" height="680" alt="image" src="https://github.com/user-attachments/assets/84297340-b106-4217-9c1c-53b32fea82d5" />

---

# RTL vs GLS

| RTL Simulation | Gate-Level Simulation |
|---|---|
| Uses RTL code | Uses synthesized netlist |
| High-level representation | Gate/standard-cell representation |
| Faster simulation | Generally slower |
| Used for functional verification | Used for post-synthesis verification |
| Technology independent | Technology dependent |

The behavior of RTL and GLS should generally match for a correctly synthesized functional design.

---

# Blocking Assignment

Blocking assignment uses:

```verilog
=
```

Example:

```verilog
always @(*)
begin
    y = a & b;
end
```

Blocking assignments execute statements sequentially.

They are generally preferred for **combinational procedural logic**.

---

# Non-Blocking Assignment

Non-blocking assignment uses:

```verilog
<=
```

Example:

```verilog
always @(posedge clk)
begin
    q <= d;
end
```

Non-blocking assignments are generally preferred for **clocked/sequential logic**.

They model simultaneous updates of sequential elements at a clock event.

---

# Blocking vs Non-Blocking

| Blocking `=` | Non-Blocking `<=` |
|---|---|
| Generally used for combinational logic | Generally used for sequential logic |
| Executes statement by statement | Updates are scheduled |
| Can introduce statement-order dependency | Models simultaneous register updates |
| Common in `always @(*)` | Common in `always @(posedge clk)` |

---

# GLS Using Gate-Level Libraries

During GLS, the synthesized netlist contains technology-specific standard cells.

Therefore, the simulator needs the corresponding **gate-level cell models/libraries**.

The SKY130 flow may use:

```text
primitives.v
```

and SKY130 standard-cell Verilog models.

These models provide the behavioral description of the cells instantiated in the synthesized netlist.

The GLS setup is therefore:

```text
Gate-Level Netlist
       +
Gate-Level Cell Libraries
       +
     Testbench
       ↓
  Icarus Verilog
       ↓
     ./a.out
       ↓
       VCD
       ↓
    GTKWave
```

The primitive and SKY130 cell models allow Icarus Verilog to understand and simulate the cells present in the synthesized netlist.

---

# GLS Flow

```text
RTL Design
    ↓
RTL Simulation
    ↓
Yosys Synthesis
    ↓
Technology Mapping
    ↓
Gate-Level Netlist
    ↓
Add SKY130 / Primitive Cell Models
    ↓
Gate-Level Testbench
    ↓
Icarus Verilog
    ↓
./a.out
    ↓
VCD
    ↓
GTKWave
    ↓
Verify GLS Waveforms
```

The main objective is to compare the behavior of the synthesized gate-level design with the original RTL behavior.
In case of bad_mux, itwont be a match of rtl and gls
<img width="940" height="326" alt="image" src="https://github.com/user-attachments/assets/45b34370-5197-4f4a-bf2b-e2a114941876" />

<img width="940" height="335" alt="image" src="https://github.com/user-attachments/assets/23879101-0a10-43ac-a0eb-8705b740b9d1" />

---

# Module 5 – Synthesis Optimizations

## `if-else` Statements

`if-else` statements are used to describe conditional hardware behavior.

Example:

```verilog
if (sel)
    y = b;
else
    y = a;
```

This can synthesize into a **multiplexer**.

For multiple conditions:

```verilog
if (a)
    y = x;
else if (b)
    y = z;
else
    y = 0;
```

The synthesis tool may generate **priority logic** because the conditions are evaluated in order.

## Incomplete Assignments

Incomplete assignments in combinational logic can result in **latch inference**.

Example:

```verilog
always @(*)
begin
    if (en)
        y = a;
end
```

When `en = 0`, no new value is assigned to `y`. Therefore, the hardware may need to retain its previous value, resulting in a latch.

---

# Case Statements

A `case` statement is another way to describe conditional hardware.

Example:

```verilog
case(sel)
    2'b00: y = a;
    2'b01: y = b;
    2'b10: y = c;
    2'b11: y = d;
endcase
```

This can synthesize into multiplexer logic.

A complete `case` description should assign outputs for all required conditions to avoid unintended latch inference.

---

# `for` Loop

A procedural `for` loop can be used in RTL to describe repeated operations.

Example:

```verilog
integer i;

always @(*)
begin
    for(i = 0; i < 4; i = i + 1)
        y[i] = a[i] & b[i];
end
```

In synthesizable RTL, the synthesis tool generally **unrolls the loop**.

Therefore, it does not represent a software-style runtime loop.

```text
for i = 0 to 3
       ↓
Repeated Hardware
       ↓
4 AND operations
```

---

# `generate for`

A `generate for` construct is used to create repeated hardware structures during elaboration.

Example:

```verilog
genvar i;

generate
    for(i = 0; i < 4; i = i + 1)
    begin
        assign y[i] = a[i] & b[i];
    end
endgenerate
```

This creates multiple instances of the specified hardware structure.

## Difference Between `for` and `generate for`

| Procedural `for` | `generate for` |
|---|---|
| Used inside procedural blocks | Used to generate hardware structures |
| Describes repeated operations | Creates repeated hardware instances |
| Used during procedural evaluation | Operates during elaboration |
| Common inside `always` blocks | Common for repeated module/circuit structures |

---

# Complete VLSI Design Flow

The five modules together represent the following VLSI design flow:

```text
MODULE 1
RTL Design & Simulation
         ↓
Verilog + Testbench
         ↓
Icarus Verilog
         ↓
GTKWave
         ↓
MODULE 2
Synthesis & Technology Mapping
         ↓
Yosys
         ↓
Timing Libraries
         ↓
SKY130
         ↓
DFF + ABC Mapping
         ↓
Gate-Level Netlist
         ↓
MODULE 3
Combinational Optimization
         +
Sequential Optimization
         ↓
State Optimization
         ↓
MODULE 4
Gate-Level Simulation
         ↓
Primitive + SKY130 Libraries
         ↓
RTL vs Gate-Level Simulation
         ↓
Blocking / Non-Blocking
         ↓
Synthesis-Simulation Mismatch
         ↓
MODULE 5
Synthesis Optimization
         ↓
if-else / case
         ↓
for / generate
         ↓
Optimized Hardware
```
# Chip Development Process

Every digital chip follows a series of transformations, starting from a **behavioral idea** and eventually becoming a **physical silicon chip**.

## Chip Development Flow

```text
┌─────────────────────────┐
│      SPECIFICATION      │
│                         │
│ • Behavioral requirement│
│ • Requirements          │
│ • Architecture          │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│       RTL DESIGN        │
│                         │
│ • Register Transfer     │
│   Level description     │
│ • Written using HDL     │
│   (VHDL / Verilog)      │
│ • Describes the         │
│   hardware behavior     │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│        SYNTHESIS        │
│                         │
│ • Uses gates &          │
│   standard cells        │
│ • Converts RTL into     │
│   gate-level netlist    │
│ • Gate-level simulation │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│     PHYSICAL DESIGN     │
│                         │
│ • Floor planning        │
│ • Placement             │
│ • Clock tree synthesis  │
│ • Routing               │
│ • Timing analysis       │
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│       FABRICATION       │
│                         │
│ • Silicon manufacturing │
│ • Final physical chip   │
└─────────────────────────┘
---

# Overall Learning Outcome

The VLSI Laboratory provides practical understanding of the **RTL-to-netlist design flow**.

The major concepts learned are:

- Verilog RTL design
- Testbench development
- Icarus Verilog simulation
- GTKWave waveform analysis
- Yosys synthesis
- Liberty timing libraries
- SKY130 standard cells
- PVT conditions
- Hierarchical and flat synthesis
- Flip-flop coding
- Synchronous and asynchronous resets
- Technology mapping
- Combinational optimization
- Sequential optimization
- State optimization
- Gate-Level Simulation
- Blocking and non-blocking assignments
- Synthesis-simulation mismatch
- `if-else` and `case` synthesis
- `for` and `generate for`
- Synthesis-aware RTL coding

The complete flow can be summarized as:

```text
RTL Design
    ↓
RTL Simulation
    ↓
Functional Verification
    ↓
Synthesis
    ↓
Logic Optimization
    ↓
Technology Mapping
    ↓
Standard-Cell Netlist
    ↓
Gate-Level Simulation
    ↓
Netlist Verification
    ↓
Synthesis Optimization
```
# VSDBabySoC – Pre-Synthesis and Post-Synthesis Simulation

## 1. Introduction

**VSDBabySoC** is a small System-on-Chip design that integrates a **RISC-V processor core, PLL, and DAC**.

The major blocks of VSDBabySoC are:

- **RVMYTH** – RISC-V based processor core
- **PLL (`avsdpll`)** – Generates the clock signal required by the processor
- **DAC (`avsddac`)** – Converts the digital output from the processor into the final output
- **`RV_TO_DAC[9:0]`** – 10-bit digital signal transferred from RVMYTH to the DAC
- **`VREFH`** – Reference voltage provided to the DAC
- **`OUT`** – Final output of the VSDBabySoC

---

## 2. VSDBabySoC Block Diagram

```text
        ENb_CP ───────┐
        ENb_VCO ──────┤
        REF ──────────┤
        VCO_IN ───────┤
                      ▼
                ┌─────────────┐
                │     PLL     │
                │   avsdpll   │
                └──────┬──────┘
                       │
                      CLK
                       │
                       ▼
                ┌─────────────┐
        reset ─►│   RVMYTH    │
                │    CORE     │
                └──────┬──────┘
                       │
                 RV_TO_DAC[9:0]
                       │
                       ▼
                ┌─────────────┐
        VREFH ─►│     DAC     │
                │   avsddac   │
                └──────┬──────┘
                       │
                      OUT
                       │
                       ▼
                  FINAL OUTPUT
```

### Block Description

The **PLL** receives the reference and control signals and generates the clock signal `CLK`.

The generated clock is supplied to the **RVMYTH RISC-V processor core**. The processor performs its operations and produces a **10-bit digital output**, `RV_TO_DAC[9:0]`.

This digital output is given to the **DAC**, along with the reference voltage `VREFH`. The DAC converts the digital signal into the final `OUT` signal.

---

## 3. VSDBabySoC Design and Verification Flow

```text
                 ┌──────────────────┐
                 │    RTL DESIGN    │
                 │    VSDBabySoC    │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ PRE-SYNTHESIS    │
                 │   SIMULATION     │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │   RTL WAVEFORM   │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │     SYNTHESIS    │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ GATE-LEVEL       │
                 │    NETLIST       │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ POST-SYNTHESIS   │
                 │   SIMULATION     │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ GATE-LEVEL       │
                 │    WAVEFORM      │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │    WAVEFORM      │
                 │    COMPARISON    │
                 └────────┬─────────┘
                          │
                          ▼
                 ┌──────────────────┐
                 │ FUNCTIONAL MATCH │
                 │       ✓ PASS     │
                 └──────────────────┘
```

---

## 4. Pre-Synthesis Simulation

### Overview

**Pre-synthesis simulation** is performed on the original **RTL design** before synthesis.

The main objective is to verify the functional behavior of the VSDBabySoC at the RTL level.

```text
┌──────────────────┐
│    RTL Design    │
│    Verilog HDL   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ RTL Simulation   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   VCD Waveform   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│     GTKWave      │
└──────────────────┘
```

### Purpose

Pre-synthesis simulation is used to:

- Verify the functionality of the RTL design.
- Check clock and reset behavior.
- Verify the interaction between PLL, RVMYTH, and DAC.
- Observe the `RV_TO_DAC[9:0]` digital output.
- Verify the final `OUT` signal.
- Detect functional errors before synthesis.

### Important Signals

| SignalDescription |                                   |
| ----------------- | --------------------------------- |
| `CLK`             | Clock signal generated by the PLL |
| `reset`           | Reset signal for the RVMYTH core  |
| `ENb_CP`          | PLL control signal                |
| `ENb_VCO`         | PLL control signal                |
| `REF`             | Reference signal                  |
| `VCO_IN`          | VCO input signal                  |
| `VREFH`           | DAC reference voltage             |
| `RV_TO_DAC[9:0]`  | 10-bit digital output from RVMYTH |
| `OUT`             | Final DAC output                  |

### Pre-Synthesis Waveform

The pre-synthesis waveform represents the behavior of the original RTL implementation.

The waveform shows the clock, reset, PLL-related signals, RVMYTH output, DAC input, and final output.

The `RV_TO_DAC[9:0]` signal changes according to the operation of the RVMYTH processor. These digital values are supplied to the DAC, which produces the corresponding `OUT` signal.

**Pre-Synthesis Simulation Result: PASS ✓**

---

## 5. Synthesis

### Overview

**Synthesis** converts the RTL description into a **gate-level netlist**.

```text
┌──────────────────┐
│    RTL Design    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│     Synthesis    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Gate-Level       │
│     Netlist      │
└──────────────────┘
```

During synthesis, the RTL design is transformed into a circuit made up of **logic gates and standard cells**.

The resulting gate-level netlist represents the intended functionality of the RTL design in a form closer to the actual hardware implementation.

### Gate-Level Netlist

```text
RTL Description
      │
      ▼
   Synthesis
      │
      ▼
Gate-Level Netlist
      │
      ├── Logic Gates
      ├── Flip-Flops
      ├── Buffers
      └── Standard Cells
```

The synthesized netlist is used as the input for **post-synthesis simulation**.

---

## 6. Post-Synthesis Simulation

### Overview

**Post-synthesis simulation** is performed using the **gate-level netlist generated during synthesis**.

The purpose is to verify that the synthesized implementation preserves the functionality of the original RTL design.

```text
┌──────────────────┐
│    RTL Design    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│     Synthesis    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Gate-Level       │
│     Netlist      │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│ Post-Synthesis   │
│    Simulation    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   VCD Waveform   │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│     GTKWave      │
└──────────────────┘
```

### Purpose

Post-synthesis simulation is used to:

- Verify the synthesized gate-level design.
- Confirm that the functionality of the RTL is preserved.
- Observe the behavior of the synthesized logic.
- Compare the post-synthesis waveform with the pre-synthesis waveform.
- Identify any functional differences introduced during synthesis.

### Post-Synthesis Waveform

The post-synthesis waveform represents the behavior of the synthesized gate-level implementation.

The important signals observed include:

- `CLK`
- `reset`
- `ENb_CP`
- `ENb_VCO`
- `REF`
- `VCO_IN`
- `VREFH`
- `RV_TO_DAC[9:0]`
- `OUT`

The waveform shows the operation of the synthesized design and is compared with the RTL simulation waveform.

**Post-Synthesis Simulation Result: PASS ✓**

---

## 7. Pre-Synthesis vs Post-Synthesis

The main purpose of comparing pre-synthesis and post-synthesis waveforms is to verify that synthesis has not changed the intended functionality of the design.

```text
              RTL DESIGN
                  │
          ┌───────┴────────┐
          │                │
          ▼                ▼
 ┌────────────────┐   ┌────────────────┐
 │ Pre-Synthesis  │   │    Synthesis   │
 │   Simulation   │   └───────┬────────┘
 └───────┬────────┘           │
         │                    ▼
         │             ┌───────────────┐
         │             │ Gate-Level    │
         │             │   Netlist     │
         │             └───────┬───────┘
         │                     │
         │                     ▼
         │             ┌───────────────┐
         │             │ Post-Synthesis│
         │             │   Simulation   │
         │             └───────┬───────┘
         │                     │
         ▼                     ▼
 ┌────────────────┐    ┌────────────────┐
 │ RTL Waveform   │    │ Gate-Level     │
 │                │    │   Waveform     │
 └───────┬────────┘    └───────┬────────┘
         │                     │
         └──────────┬──────────┘
                    ▼
          ┌──────────────────┐
          │ Waveform Compare │
          └────────┬─────────┘
                   │
                   ▼
          ┌──────────────────┐
          │ Functional Match │
          │       ✓ PASS     │
          └──────────────────┘
```

---

## 8. Waveform Comparison

### Pre-Synthesis

The pre-synthesis waveform is generated from the **RTL implementation**.

```text
RTL
 ↓
Simulation
 ↓
VCD
 ↓
GTKWave
 ↓
RTL Waveform
```

It represents the intended functional behavior of the design before synthesis.

### Post-Synthesis

The post-synthesis waveform is generated from the **synthesized gate-level netlist**.

```text
Gate-Level Netlist
 ↓
Simulation
 ↓
VCD
 ↓
GTKWave
 ↓
Gate-Level Waveform
```

It represents the behavior of the synthesized hardware implementation.

---

## 9. Pre-Synthesis and Post-Synthesis Matching

The pre-synthesis and post-synthesis waveforms were compared based on the important signals of the design.

The observed signals include:

- Clock (`CLK`)
- Reset (`reset`)
- PLL control signals
- Reference signals
- `RV_TO_DAC[9:0]`
- DAC reference (`VREFH`)
- Final output (`OUT`)

The waveforms show matching functional behavior between the RTL simulation and the synthesized gate-level simulation.

```text
       PRE-SYNTHESIS                 POST-SYNTHESIS
             │                              │
             ▼                              ▼
        RTL Simulation              Gate-Level Simulation
             │                              │
             ▼                              ▼
        RTL Waveform                 Synthesized Waveform
             │                              │
             └──────────────┬───────────────┘
                            ▼
                    WAVEFORM COMPARISON
                            │
                            ▼
                    FUNCTIONAL MATCH ✓
```

### Verification Result

**Pre-Synthesis = Post-Synthesis Functionally**

The synthesized design preserves the intended functionality of the original RTL design.

**Final Result: PASS ✓**

---

## 10. Pre-Synthesis vs Post-Synthesis Comparison

| FeaturePre-SynthesisPost-Synthesis |                          |                                  |
| ---------------------------------- | ------------------------ | -------------------------------- |
| Design Representation              | RTL                      | Gate-Level Netlist               |
| Purpose                            | Verify RTL functionality | Verify synthesized functionality |
| Design Level                       | RTL                      | Gate Level                       |
| Simulation                         | RTL Simulation           | Gate-Level Simulation            |
| Waveform                           | RTL Waveform             | Gate-Level Waveform              |
| Viewer                             | GTKWave                  | GTKWave                          |
| `CLK`                              | Observed                 | Observed                         |
| `reset`                            | Observed                 | Observed                         |
| `RV_TO_DAC[9:0]`                   | Observed                 | Observed                         |
| `OUT`                              | Observed                 | Observed                         |
| Functional Behavior                | Correct                  | Matches RTL                      |
| Result                             | PASS ✓                   | PASS ✓                           |

---

## 11. Complete Verification Flow

```text
                    VSDBabySoC RTL
                         │
                         ▼
              ┌─────────────────────┐
              │ Pre-Synthesis       │
              │ Simulation          │
              └──────────┬──────────┘
                         │
                         ▼
                  RTL Waveform
                         │
                         ▼
              ┌─────────────────────┐
              │     Synthesis       │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Gate-Level Netlist  │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Post-Synthesis      │
              │ Simulation          │
              └──────────┬──────────┘
                         │
                         ▼
                Gate-Level Waveform
                         │
                         ▼
              ┌─────────────────────┐
              │ Waveform Comparison │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │ Functional Matching │
              │        ✓ PASS       │
              └─────────────────────┘
```

---

## 12. Key Learning

Through the VSDBabySoC simulation and synthesis flow, the following concepts were demonstrated:

- Understanding the architecture of a small SoC.
- Understanding the role of **PLL, RVMYTH, and DAC**.
- Understanding **RTL/pre-synthesis simulation**.
- Observing simulation waveforms using **GTKWave**.
- Understanding the process of **logic synthesis**.
- Understanding the **gate-level netlist** generated after synthesis.
- Understanding **post-synthesis gate-level simulation**.
- Comparing RTL and synthesized waveforms.
- Verifying that synthesis preserves the intended functionality.
- Understanding the transition from **RTL design to gate-level implementation**.

---

## 13. Final Conclusion

The VSDBabySoC design was successfully verified through **pre-synthesis and post-synthesis simulations**.

The **pre-synthesis simulation** verified the functional behavior of the original RTL design. The RTL was then converted into a **gate-level netlist through synthesis**.

The synthesized design was subsequently verified using **post-synthesis simulation**.

The comparison of the pre-synthesis and post-synthesis waveforms shows that the important signals maintain their expected functional behavior.

Therefore, the synthesized VSDBabySoC design successfully preserves the intended functionality of the original RTL design.

> **Overall Flow:**
> **RTL Design → Pre-Synthesis Simulation → Synthesis → Gate-Level Netlist → Post-Synthesis Simulation → Waveform Comparison → Functional Match ✓**

---

## 14. Simulation Screenshots

### VSDBabySoC Block Diagram

<img width="940" height="340" alt="image" src="https://github.com/user-attachments/assets/353a5288-d934-40b8-9c61-04eb140730db" />


### Pre-Synthesis Waveform

<img width="940" height="363" alt="image" src="https://github.com/user-attachments/assets/9864014c-ef82-4f27-8e58-3cc47340c289" />


### Post-Synthesis Waveform

<img width="940" height="371" alt="image" src="https://github.com/user-attachments/assets/30a7c7cf-2e0e-4836-8b37-1b25952d4cc1" />


---

## 15. Summary

```text
RTL DESIGN
    ↓
PRE-SYNTHESIS SIMULATION
    ↓
RTL WAVEFORM
    ↓
SYNTHESIS
    ↓
GATE-LEVEL NETLIST
    ↓
POST-SYNTHESIS SIMULATION
    ↓
GATE-LEVEL WAVEFORM
    ↓
PRE vs POST COMPARISON
    ↓
FUNCTIONAL MATCH ✓
```

### Final Result

**VSDBabySoC – RTL to Gate-Level Verification Completed Successfully ✓**
