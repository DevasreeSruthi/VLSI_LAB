## Verilog Simulation using Icarus Verilog and GTKWave

## Introduction

Verilog HDL is a Hardware Description Language (HDL) used to design and simulate digital circuits. Before implementing a design on hardware, it is verified through simulation.

In this experiment, we use **Icarus Verilog (iverilog)** for compilation and simulation, and **GTKWave** for viewing the generated waveforms.

---

# What is Icarus Verilog?

Icarus Verilog (iverilog) is an open-source Verilog compiler and simulator.

It compiles the Verilog design file and the testbench into an executable simulation file (`a.out`). Running this executable generates a **Value Change Dump (VCD)** file, which stores the signal transitions during simulation.

---

# Design and Testbench

A Verilog simulation requires two files.

### Design File

The design file contains the logic of the digital circuit.

Example:
- 2:1 Multiplexer
- AND Gate
- OR Gate

### Testbench

A testbench is used to verify the functionality of the design.

The testbench:
- Instantiates the design.
- Generates different input combinations (stimulus).
- Dumps the waveform into a VCD file.
- Ends the simulation after a specified time.

The design being tested is called the **Unit Under Test (UUT).**

---

# How Simulation Works

The complete simulation flow is shown below.

<img width="1221" height="533" alt="image" src="https://github.com/user-attachments/assets/20c88ec2-7d44-446a-a59c-b71dd4c8c331" />


### Step 1

Compile the design and testbench.

```bash
iverilog good_mux.v tb_good_mux.v
```

This creates an executable file named **a.out**.

---

### Step 2

Run the simulation.

```bash
./a.out
```

This generates the **tb_good_mux.vcd** file.

---

### Step 3

Open the waveform.

```bash
gtkwave tb_good_mux.vcd
```

GTKWave displays all signal transitions generated during simulation.

---

# VCD (Value Change Dump)

The VCD file stores all signal value changes during simulation.

It is automatically generated using:

```verilog
$dumpfile("tb_good_mux.vcd");
$dumpvars(0, tb_good_mux);
```

Without these commands, no waveform will be available in GTKWave.

---

# Stimulus Generation

The testbench generates different input combinations.

Example:

```verilog
sel = 0;
i0 = 0;
i1 = 0;

#300;
$finish;
```

The `#300` delay runs the simulation for 300 ns, after which `$finish` terminates the simulation.

---

# GTKWave Implementation

After opening the VCD file in GTKWave:

1. Select the required module.
2. Drag and drop the input and output signals.
3. Click **Zoom Fit** to view the complete waveform.
4. Observe how the output changes with different input combinations.

---

# Files Used

| File | Description |
|------|-------------|
| good_mux.v | Design file |
| tb_good_mux.v | Testbench |
| tb_good_mux.vcd | Generated waveform file |
| a.out | Executable simulation file |

---

# Design and Testbench

<img width="940" height="478" alt="image" src="https://github.com/user-attachments/assets/08472981-54bb-48b0-9a7a-6835807672f5" />


---

# GTKWave Output

<img width="940" height="504" alt="image" src="https://github.com/user-attachments/assets/add4c2ae-f60b-495e-bc59-1611df71ae31" />


---

# Conclusion

Successfully understood the complete Verilog simulation flow using Icarus Verilog and GTKWave, including compilation, execution, VCD generation, and waveform analysis using a 2:1 Multiplexer example.
