#Task 3
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
