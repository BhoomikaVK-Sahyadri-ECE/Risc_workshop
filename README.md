# Passenger Counting System

## Overview

The **Passenger Counting System** integrates **two ultrasonic sensors** with the **CH32V003 RISC-V processor** to count passengers entering and exiting a space. The system detects movement by analyzing the sequence in which the ultrasonic sensors are triggered.

- If **Sensor 1 detects movement before Sensor 2**, the system **increments** the count (entry).
- If **Sensor 2 detects movement before Sensor 1**, the system **decrements** the count (exit).

This project is useful for automated **occupancy monitoring** in buses, rooms, and restricted areas.

## Components Required

- **CH32V003 RISC-V processor**
- **2x Ultrasonic sensors (HC-SR04)**
- **Resistors (e.g., 1kΩ, 330Ω)**
- **Power supply (3.3V)**
- **Breadboard**
- **Jumper wires**

## Circuit Connection

| Component          | CH32V003 Pin |
|--------------------|-------------|
| **Ultrasonic Sensor 1** | |
| TRIG1            | A1 |
| ECHO1            | A2 |
| **Ultrasonic Sensor 2** | |
| TRIG2            | D1 |
| ECHO2            | D2 |
| **Power Supply** | |
| VCC              | 3.3V |
| GND              | GND |

## Working Explanation

1. **Detection Process**:
   - The **HC-SR04 ultrasonic sensors** measure the distance by sending out an ultrasonic pulse and waiting for the echo.
   - The CH32V003 processor calculates the time taken for the echo and determines the presence of a person.

2. **Entry & Exit Logic**:
   - If **Sensor 1 (TRIG1, ECHO1) detects movement first**, followed by **Sensor 2 (TRIG2, ECHO2)**, the system **increases the count**.
   - If **Sensor 2 detects movement first**, followed by **Sensor 1**, the system **decreases the count**.

3. **Displaying the Count**:
   - The passenger count is **printed to the terminal (UART serial output)**.

## Circuit Diagram

![Circuit Diagram](https://github.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/blob/776124403e1e337011ae8bc2d691dba69f708865/Task5/Circuit%20Diagram.png)

## Code Implementation

### **1. `main.c` (Core Logic)**
```c
#include <stdio.h>
#include "ch32v003_uart.h"
#include "ch32v003_gpio.h"
#include "ch32v003_timer.h"

#define TRIG1   GPIO_PIN_A1  // Ultrasonic sensor 1 Trigger pin
#define ECHO1   GPIO_PIN_A2  // Ultrasonic sensor 1 Echo pin
#define TRIG2   GPIO_PIN_D1  // Ultrasonic sensor 2 Trigger pin
#define ECHO2   GPIO_PIN_D2  // Ultrasonic sensor 2 Echo pin

volatile int passenger_count = 0;

// Function to measure distance from ultrasonic sensor
int get_distance(GPIO_TypeDef *TRIG, GPIO_TypeDef *ECHO) {
    int pulse_duration = 0;
    int distance = 0;

    GPIO_Write(TRIG, 1);
    delay_us(10);
    GPIO_Write(TRIG, 0);

    while (!GPIO_Read(ECHO));  // Wait for echo high
    while (GPIO_Read(ECHO)) {  // Measure echo pulse duration
        pulse_duration++;
        delay_us(1);
    }

    distance = pulse_duration / 58;  // Convert to cm
    return distance;
}

// Main function
int main() {
    UART_Init();  // Initialize UART for serial output
    GPIO_Init(TRIG1, OUTPUT);
    GPIO_Init(ECHO1, INPUT);
    GPIO_Init(TRIG2, OUTPUT);
    GPIO_Init(ECHO2, INPUT);

    printf("Passenger Counting System Started...\n");

    while (1) {
        int dist1 = get_distance(TRIG1, ECHO1);
        int dist2 = get_distance(TRIG2, ECHO2);

        if (dist1 < 20 && dist2 < 20) { // Person detected
            delay_ms(500);  // Debounce delay

            if (get_distance(TRIG1, ECHO1) < 20 && get_distance(TRIG2, ECHO2) > 20) {
                passenger_count++;
                printf("Passenger Entered, Count: %d\n", passenger_count);
            } 
            else if (get_distance(TRIG2, ECHO2) < 20 && get_distance(TRIG1, ECHO1) > 20) {
                passenger_count--;
                printf("Passenger Exited, Count: %d\n", passenger_count);
            }
        }
    }
    return 0;
}


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
