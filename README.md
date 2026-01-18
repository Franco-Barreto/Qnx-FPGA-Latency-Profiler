# Ethan-and-Franco-s-Project-Smiley-Face-Typed-Out
##  Project Abstract
This project implements a **Hardware-in-the-Loop (HIL)** testbench to benchmark the interrupt latency and scheduler jitter of the **QNX Real-Time Operating System (RTOS)**.

By utilizing a **Xilinx Zynq-7000 FPGA** as a deterministic "reflex" node, the system isolates OS-induced delays from hardware latency, providing microsecond-precision measurements of the "Tick-to-Trade" performance on embedded ARM architectures.

##  System Architecture
The system consists of two distinct physical nodes connected via GPIO:

1.  **The Controller (Device Under Test):** A Raspberry Pi 5 running **QNX Neutrino**. It executes a high-priority real-time control loop that asserts a signal and awaits a response.
2.  **The Plant (Ground Truth):** A PYNQ-Z2 FPGA running bare-metal C code on Jupyter. It acts as a zero-jitter signal reflector with a fixed hardware latency of ~5µs.

### The Signal Path
`QNX (User Space) -> Kernel Driver -> GPIO Output -> [WIRE] -> FPGA Input -> Logic -> FPGA Output -> [WIRE] -> QNX Interrupt -> Latency Calculation`

##  Results & Jitter Analysis
![Real-Time Graph](./docs/jitter_graph_real.png)
*Above: Real-time visualization of round-trip latency. The baseline sits at ~115µs (Python overhead), while vertical spikes represent OS scheduler preemption.*

* **Fast Path Latency:** 110-120 µs
* **Jitter Variance:** ±150 µs (under GUI load)
  
##  Hardware Setup
* **Host:** Raspberry Pi 5 (QNX 8.0 BSP)
* **Plant:** TUL PYNQ-Z2 (Zynq XC7Z020)
* **Connectivity:** Direct GPIO coupling (Pins 17/27) with common ground reference.

##  Usage
### 1. Configure QNX Hardware
The Raspberry Pi 5 requires explicit pin muxing for the RP1 chipset:
```bash
# Set Trigger (Pin 27) to Output and Reflex (Pin 17) to Input
gpio-rp1 set 27 op
gpio-rp1 set 17 ip
