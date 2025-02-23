# Passenger Counting System

## Overview

The **Passenger Counting System** integrates **two ultrasonic sensors** with the **CH32V003 RISC-V processor** to count passengers entering and exiting a space. The system detects movement by analyzing the sequence in which the ultrasonic sensors are triggered.

- If **Sensor 1 detects movement before Sensor 2**, the system **increments** the count (entry).
- If **Sensor 2 detects movement before Sensor 1**, the system **decrements** the count (exit).

This project is useful for automated **occupancy monitoring** in buses, rooms, and restricted areas.

## Components Required

- **CH32V003 RISC-V processor**
- **2x Ultrasonic sensors (HC-SR04)**
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
| VCC              | 5V |
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

![Circuit Diagram](https://github.com/BhoomikaVK-Sahyadri-ECE/Risc_workshop/blob/f9a9a804ca7c3202590e6eef35d15601c3ba176f/Task5/Circuit_diagram.png)

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


