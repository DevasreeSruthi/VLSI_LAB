# Digital VLSI Laboratory

Welcome to my **Digital VLSI Laboratory** repository. 📚

This repository contains my learning, practical work, experiments, and documentation carried out as part of my **VLSI Laboratory**.

The work includes **RTL Design, Verilog, Simulation, Synthesis, Technology Mapping, Timing Libraries, and Netlist Verification**.

---

## 📂 Modules and Labs

### 🔹 Lab 01 – 2:1 Multiplexer

This lab covers the design and simulation of a **2:1 Multiplexer** using Verilog.

#### Topics Covered

- RTL Design
- Verilog Implementation
- 2:1 Multiplexer
- Icarus Verilog Simulation
- GTKWave Waveform Analysis
- Simulation Results
- Functional Verification

👉 [View Lab 01 – 2:1 Multiplexer](./Lab-01-2x1-Multiplexer)

---

### 🔹 Lab 02 – RTL Design and Synthesis using Yosys

This lab covers the **RTL-to-Netlist synthesis flow** using Yosys and the SKY130 standard-cell library.

#### Topics Covered

- RTL Design
- Verilog
- RTL Synthesis
- Yosys
- ABC Technology Mapping
- SKY130 Standard-Cell Library
- Liberty `.lib` Files
- Timing Concepts
- Cell Selection
- Drive Strengths
- Netlist Generation
- Icarus Verilog Simulation
- GTKWave Waveform Verification

👉 [View Lab 02 – RTL Design and Synthesis using Yosys](./Lab-02-YOSYS-mux)

---

# 🔹 Module 2 – Timing Libraries, Hierarchical vs Flat Synthesis & Efficient Flop Coding Styles

This module focuses on important concepts involved in **technology-specific synthesis and efficient RTL coding**.

The module explores how RTL designs are synthesized using timing libraries, how hierarchy can be preserved or flattened, and how different flip-flop coding styles affect the synthesized hardware.

## 📌 Topics Covered

- Timing Libraries
- Sky130 Standard Cell Library
- PVT – Process, Voltage and Temperature
- Hierarchical Synthesis
- Flat Synthesis
- Submodules
- Module Instantiation
- Flip-Flops
- Efficient Flip-Flop Coding
- Synchronous Reset
- Asynchronous Reset
- Flip-Flop Initialization
- Yosys Synthesis Flow
- RTL Processing
- Logic Optimization
- `dfflibmap`
- ABC Technology Mapping
- Netlist Generation
- Standard-Cell Mapping
- Technology-Specific Synthesis

👉 [View Module 2 – Timing Libraries, Hierarchical vs Flat Synthesis & Efficient Flop Coding Styles](./Module-2%3ATiming%20libs%2Chierarchial%20vs%20flat%20synthesis%2Cefficient%20flop%20coding%20styles)

---

## 🛠️ Tools Used

- Verilog
- Icarus Verilog
- GTKWave
- Yosys
- ABC
- SKY130 Standard-Cell Library
- Liberty `.lib` Timing Libraries
- Linux / Ubuntu Virtual Machine

---

## 🔬 VLSI Design Flow

The practical work in this repository follows the general digital VLSI design flow:

```text
RTL Design
     ↓
Verilog Coding
     ↓
Simulation
     ↓
Waveform Verification
     ↓
RTL Synthesis
     ↓
Logic Optimization
     ↓
Technology Mapping
     ↓
Standard Cell Mapping
     ↓
Netlist Generation
     ↓
Netlist Verification
```
---

# 🔹 Module 3 – Combinational & Sequential Optimization

In Module 3, I learned how synthesis tools optimize digital circuits by removing redundant and unnecessary logic while preserving the intended functionality.

#### Combinational Optimization

Topics covered:

- Constant propagation
- Boolean logic optimization
- K-Map
- Quine–McCluskey method
- Multiplexer optimization
- Removal of redundant logic

#### Sequential Optimization

Topics covered:

- Sequential constant propagation
- D flip-flop optimization
- State optimization
- Retiming
- Sequential logic cloning
- Counter optimization

👉 [View Module 3 –Combinational and Sequential Optimisations ](./Module-3%3ACombinational%20and%20Sequential%20Optimisations)

# 🔹 Module 4 — GLS, Blocking vs Non-Blocking & Synthesis-Simulation Mismatch
- Gate-Level Simulation (GLS)
- RTL vs Gate-Level Netlist
- Functional GLS
- Timing GLS
- IVerilog GLS flow
- Synthesis-simulation mismatch
- Missing sensitivity list
- `always @(*)`
- Blocking assignments (`=`)
- Non-blocking assignments (`<=`)
- Blocking vs non-blocking assignments
- Sequential logic coding guidelines
- Combinational logic coding guidelines
- Blocking assignment caveats
- Statement-order dependency
- Ternary operator MUX
- Yosys synthesis flow
- Gate-level netlist generation
- GLS using gate-level libraries
- GTKWave waveform verification
- Practical GLS experiments
👉 [View Module 4 –GLS,blocking v/s non-blocking and synthesis simulation mismatch ](.//Module-4%3AGLS%2Cblocking%20v/s%20non-blocking%20and%20synthesis%20simulation%20mismatch)

# 📘 Module 5: Optimisation in Synthesis

- `if / else if / else` constructs
- `case` statements
- Priority logic and priority encoding
- Incomplete assignments and latch inference
- Partial and overlapping case conditions
- Procedural `for` loop vs `generate for`
- Multiplexer (MUX)
- Demultiplexer (DEMUX)
- Ripple Carry Adder (RCA)
- RTL simulation and waveform analysis
- Yosys synthesis
- GTKWave waveform visualization
- RTL-to-synthesis flow
- Synthesis optimisation techniques
- Comparison of different RTL coding styles

👉 [Module 5 – Optimisation in Synthesis](https://github.com/DevasreeSruthi/VLSI_LAB/tree/main/Module-5%3AOptimisation%20in%20Synthesis)




