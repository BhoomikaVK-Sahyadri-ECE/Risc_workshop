# Task 5
# Temperature-Controlled Fan

## Overview

The Temperature-Controlled Fan project integrates a temperature sensor (LM35) with the CH32V003 RISC-V processor to create an automated fan system. The system monitors ambient temperature in real-time and adjusts the fan's speed accordingly. The LM35 temperature sensor detects the surrounding temperature and sends an analog signal to the CH32V003 processor, which then generates PWM signals to control the fan's speed. This project provides an energy-efficient solution by ensuring the fan operates only when needed and at appropriate speeds.

## Components Required

- **CH32V003 RISC-V processor**
- **LM35 temperature sensor**
- **Small DC fan (5V)**
- **Transistor (e.g., BC547)**
- **Resistors (e.g., 10kΩ, 1kΩ)**
- **Power supply**
- **Breadboard**
- **Jumper wires**

## Circuit Connection

### Connecting the LM35 Sensor:
- **VCC (Pin 1)**: Connect to the 3.3V pin of the CH32V003 processor.
- **OUT (Pin 2)**: Connect to an ADC pin (e.g., A0) on the processor.
- **GND (Pin 3)**: Connect to the ground (GND) of the processor.

### Connecting the DC Fan:
- **Positive Terminal**: Connect to the 5V power supply via the transistor (see below).
- **Negative Terminal**: Connect to GND.

Use a BC547 transistor to control the fan's operation:
- **Base**: Connect to a 1kΩ resistor, which is then connected to the PWM pin (e.g., D2) of the CH32V003 processor.
- **Collector**: Connect to the negative terminal of the fan.
- **Emitter**: Connect to GND.

### Power Supply:
- Provide **3.3V** to the CH32V003 processor and **5V** for the DC fan.

### Pinout Diagram

| Component             | CH32V003 Pin       |
|-----------------------|--------------------|
| **LM35 Sensor**        |                    |
| VCC                   | 3.3V (VIN)         |
| OUT                   | A0 (ADC)           |
| GND                   | GND                |
| **DC Fan**             |                    |
| Positive Terminal     | 5V Power Supply    |
| Negative Terminal     | Collector (via BC547) |
| **BC547 Transistor**  |                    |
| Base                  | D2 (PWM via 1kΩ)   |
| Collector             | Fan Negative Terminal |
| Emitter               | GND                |

## Circuit Diagram

![Circuit Diagram](https://github.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/blob/776124403e1e337011ae8bc2d691dba69f708865/Task5/Circuit%20Diagram.png)

## Working Explanation

1. **Temperature Sensing**: The LM35 sensor measures the ambient temperature and outputs a corresponding analog voltage (10mV per °C).
2. **Signal Processing**: The CH32V003 processor reads this analog voltage via its ADC pin (e.g., A0) and calculates the temperature.
3. **Fan Speed Control**: Based on the temperature value, the processor generates a PWM signal on pin D2, which controls the fan speed via the transistor. A higher temperature results in a higher duty cycle, increasing the fan speed.
4. **Efficient Operation**: When the temperature drops below a threshold, the fan slows down or stops to save energy.


# Task 4
# RV32I Pipelined Processor Simulation and Waveform Analysis

This project demonstrates the implementation and simulation of a 5-stage pipelined RV32I processor, focusing on the execution of basic RISC-V instructions (`add`, `sub`, `and`, `or`, `xor`) and visualizing their behavior through waveform analysis using **Icarus Verilog** and **GTKWave**.

---

## Key Highlights

### 1. **Pipeline Architecture**
The processor implements a standard 5-stage pipeline:
- **Instruction Fetch (IF)**
- **Instruction Decode (ID)**
- **Execution (EX)**
- **Memory Access (MEM)**
- **Write-Back (WB)**

Each instruction progresses through these stages, with results displayed in the waveform for critical signals like `PC` and `WB_OUT`.

---

### 2. **Simulation Environment**
- **Verilog Files**: 
  - `iiitb_rv32i.v`: Processor design.
  - `iiitb_rv32i_tb.v`: Testbench for simulating instructions.
- **Simulation Tool**: Icarus Verilog.
- **Waveform Viewer**: GTKWave.

![Waveform Obtained](https://raw.githubusercontent.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/4e64a5e739995f8bd097d020fce5567bde0f6e34/Task4/Waveform%20Obtained.png)

![Simulation 1](https://raw.githubusercontent.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/4e64a5e739995f8bd097d020fce5567bde0f6e34/Task4/Simulation1.png)

![Simulation 2](https://raw.githubusercontent.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/4e64a5e739995f8bd097d020fce5567bde0f6e34/Task4/Simulation2.png)



Steps to simulate:
```bash
# Clone the repository
$ git clone https://github.com/vinayrayapati/rv32i.git
$ cd rv32i

# Compile the design and testbench
$ iverilog -o iiitb_rv32i iiitb_rv32i.v iiitb_rv32i_tb.v

# Run the simulation
$ ./iiitb_rv32i

# View the waveform
$ gtkwave iiitb_rv32i.vcd
## Example Instructions and Observations

Here are some example instructions with the corresponding Program Counter (PC) and Write-Back Output (WB_OUT):

### Instruction 1: `add r6, r2, r1`
- **PC:** `0x00000000`
- **WB_OUT:** `r2 + r1`

### Instruction 2: `sub r7, r1, r2`
- **PC:** `0x00000004`
- **WB_OUT:** `r1 - r2`



### Instruction 3: `and r8, r1, r3`
- **PC:** `0x00000008`
- **WB_OUT:** `r1 & r3`

### Instruction 4: `xor r9, r7, r8`
- **PC:** `0x0000000C` 
- **WB_OUT:** `r7 ^ r8` 
