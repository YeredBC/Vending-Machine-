## Vending Machine

  <div align="center">
  <img src="VENDING.webp" alt="Real Vending Machine" width="300">
</div>


## Project Overview
In this project we use a Basys 3 to simulate a vending machine, in which you can enter coins of 1, 2, 5 and 10 pesos, you can select between 5 products and it shows the price in two seven segment displays and the money entered and the change on another two seven segment display. The change in shown after pressing a confirmation button.

In this machine it is not possible to enter more than 99 pesos and if the money inserted is less than the price of the product, it does not accept the purchase.

# Content List

1. [Vending Machine](#vending-machine)
2. [Project Overview](#project-overview)
3. [Requirements](#requirements)
4. [Initial Setup](#initial-setup)
5. [Installation](#installation)
6. [Basys 3](#basys-3)
7. [Product Selector](#ProductSelector)
8.  [Coin Counter](#CoinCounter)
9. [Change Calculator](#ChangeCalculator)
10. [Multiplexor](#Multiplexor)
11. [Vending Machine Top](#VendingMachineTop)
12. [Save to Memory](#save-to-memory)
13. [Vivado](#vivado)
14. [Simulation](#simulation)
15. [Add New Things](#add-new-things)
16. [Important Links](#important-links)
17. [Contact](#contact)

# Requirements
To run this project you need the following components:
- Vivado
- Basys 3

# Inital Setup

## Installation
For this project, the use of vivado was essential, which can be found on the AMD website: https://amd.com/es/products/software/adaptive-socs-and-fpgas/vivado.html It should be noted that a user license is required for its use, the version we used to create this machine was version 2022.1.

# BASYS-3
The Basys 3 is a circuit board that allows you to run code in a compact and physical way, it includes 16 levers, 16 LED indicators, 4 7-segment displays, 5 buttons and a quartz clock, which makes it very useful for a variety of uses.


We test our code using the EDA Playground website. On this page we can test the code by programming a testbench that indicates the program inputs. It is important to take into account that the clock can be implemented both in the testbench or in the VHDL code but for the proper functioning of the Basys 3 we must program it in the VHDL code.


Once the simulation on the web page acts the way we expect it to work and we test combinations that should not be possible so that we can avoid errors in the software, we can export the code to Vivado, where we can transfer it to Basys 3


# ProductSelector
Description
The ProductSelector module is part of a vending machine system. It manages product selection and displays the price of the selected product on a 7-segment display. The user interacts with the system via a 5-bit switch (sw_select), and the corresponding price is displayed dynamically using multiplexing.
Functionality
1. Product Selector
The sw_select input determines which product is selected. Each combination of the switch maps to a specific price:
- 00001 -> 10
- 00010 -> 18
- 00100 -> 24
- 01000 -> 30
- 10000 -> 35

If no valid product is selected, the price defaults to 0.
2. Price Display
The price is shown on a 7-segment display. It is divided into tens and units using arithmetic operations:

- Tens: price_internal / 10
- Units: price_internal mod 10

These digits are displayed alternately using multiplexing, controlled by a slower clock signal.
3. Clock Divider
A 100 MHz clock input is divided into a 1 kHz signal for managing the display multiplexing.
4. Reset 
A reset input ensures the system initializes with the price set to 0, preventing undefined behavior on startup or reset.
Inputs and Outputs
Inputs
- clk: 100 MHz clock signal from the FPGA.
- reset: Resets the system and sets the price to 0.
- sw_select: 5-bit input for product selection.
Output 
- seg_display: 7-bit output controlling the 7-segment display segments.
- anode: 4-bit output controlling the active digit (tens or units).
- price: Integer output showing the price of the selected product.
How it Works
1. Clock Division
The high-frequency clock is divided by a factor of 100,000 to produce a slower clock signal (clk_divider) suitable for multiplexing.
2. Multiplexing
The slower clock alternates between the tens and units digits, activating one digit at a time for efficient resource usage.
3. 7-Segment Encoding
The digits are encoded into segment patterns based on the binary representation of the tens and units values.
4. Display Control
The anode signal activates the appropriate display segment, while seg_display drives the individual segments of the 7-segment display.
Aplications
The ProductSelector module is crucial for vending machines, enabling:

- Real-time product selection feedback.
- Efficient resource usage through multiplexed display control.
- Scalability for additional features or products.

# CoinCounter
Description
The CoinCounter module is a digital system that counts coins of different denominations (1, 2, 5, and 10 units). It displays the accumulated total on a 7-segment display, with the total split into tens and units. The user interacts with the system via switches that represent different coin values, and the total is displayed using multiplexing.
Functionality

1. Coin Counting
   The system detects coin insertion through the switches `sw_1`, `sw_2`, `sw_5`, and `sw_10`. Each switch corresponds to a specific coin value:
   - `sw_1` -> 1 unit
   - `sw_2` -> 2 units
   - `sw_5` -> 5 units
   - `sw_10` -> 10 units

   When a switch detects a rising edge (coin insertion), the total is incremented by the corresponding coin value. The total is capped at 99 units.
2. Display Control
   The total is split into tens and units for display:
   - Tens: total / 10
   - Units: total mod 10
   These values are displayed alternately using multiplexing, controlled by a slower clock signal.
3. Clock Divider
   A 100 MHz clock input is divided by 100,000 to produce a 1 kHz clock (`clk_divider`) for managing the display multiplexing.

4. Reset
   A reset input ensures the system initializes with the total set to 0, preventing undefined behavior on startup or reset.
Inputs and Outputs
Inputs
- `clk`: 100 MHz clock signal from the FPGA.
- `reset`: Resets the system and sets the total to 0.
- `sw_1`, `sw_2`, `sw_5`, `sw_10`: Switches representing coin values.
Outputs
- `seg_display`: 7-bit output controlling the segments of the 7-segment display.
- `anode`: 4-bit output controlling the active digit (tens or units).
- `total`: Integer output showing the accumulated total of coins.
How it Works
1. Clock Division
   The 100 MHz clock is divided by 100,000 to produce a 1 kHz clock (`clk_divider`) suitable for display multiplexing.
2. Coin Counting and Edge Detection
   The system detects coin insertion by detecting rising edges on the switches. The total is incremented accordingly.
3. Multiplexing
   The 1 kHz clock alternates between displaying the tens and units digits, enabling efficient use of the 7-segment display.
4. 7-Segment Encoding
   The tens and units values are converted into segment patterns, which are used to control the 7-segment display.
5. Display Control
   The `anode` signal controls which digit (tens or units) is active, while `seg_display` drives the individual segments of the 7-segment display.
Applications
The CoinCounter module is useful in systems requiring coin-based input and display feedback, such as:
- Coin-operated machines (e.g., vending machines).
- Real-time coin counting systems.
- Educational projects demonstrating digital systems and multiplexing.


# ChangeCalculator

# Multiplexor
Description
The Multiplexor module is designed to manage the multiplexing of multiple displays, including the CoinCounter, ProductSelector, and Change displays in a vending machine system. It alternates between the CoinCounter and ProductSelector displays while displaying the change value when it is valid. The module controls the anodes and segments of the 7-segment displays based on the current selection.

Functionality

1. Clock Divider
   A 100 MHz clock is divided to create a slower clock signal (2 kHz) used for multiplexing the displays. This is achieved by counting clock cycles and toggling the `clk_div` signal when the count reaches a defined divisor value (`DIVISOR = 50000`).

2. Change Display
   When the `change_valid` signal is high, the module displays the value of the change on the 7-segment display:
   - The `change` value is split into tens and units using integer division and modulo operations.
   - The tens and units are displayed alternately, with the corresponding anode being activated for each digit.

3. Multiplexing
   The module multiplexes between the CoinCounter and ProductSelector displays when the `change_valid` signal is low. The `digit_sel` signal toggles between these two displays. The `seg_display` and `anode` outputs control which display is active at any given time.

4. 7-Segment Display Encoding
   Each digit (tens or units) is encoded into a 7-segment display pattern. The module uses a case statement to map binary values to corresponding segment patterns for each digit (0-9).

Inputs and Outputs

Inputs
- `clk`: 100 MHz clock signal from the FPGA.
- `anode_coin`: 4-bit signal controlling the anodes of the CoinCounter display.
- `anode_prod`: 4-bit signal controlling the anodes of the ProductSelector display.
- `seg_coin`: 7-bit signal controlling the segments of the CoinCounter display.
- `seg_prod`: 7-bit signal controlling the segments of the ProductSelector display.
- `change_valid`: Signal indicating whether the change value is valid.
- `change`: Integer value representing the amount of change.

Outputs
- `seg_display`: 7-bit output controlling the segments of the final display.
- `anode`: 4-bit output controlling the active digit (tens or units) of the final display.

How it Works

1. Clock Division
   The 100 MHz clock is divided by 50,000 to generate a 2 kHz clock (`clk_div`) for display multiplexing.

2. Change Display
   The `change` value is split into tens and units using division and modulo operations. The corresponding 7-segment display patterns are selected based on these values.

3. Multiplexing
   The `digit_sel` signal alternates between the CoinCounter and ProductSelector displays, toggling every time the `clk_div` signal pulses. The corresponding `seg_display` and `anode` outputs are updated based on the active display.

4. 7-Segment Encoding
   The tens and units values are encoded into 7-segment display patterns using a case statement. The patterns are then output to the `seg_display` based on the current digit.

5. Display Control
   The `anode` signal controls which digit is active (tens or units), while `seg_display` controls the individual segments of the 7-segment display.

 Applications
The Multiplexor module is useful for systems that require efficient management of multiple displays, such as:

- Vending machines: Displaying product selection, coin counting, and change information on shared displays.
- Coin-operated systems: Managing multiple types of information on a limited number of displays.
- Educational projects: Demonstrating multiplexing, clock division, and display control.

# VendingMachineTop

# Save to Memory

# Vivado

# Simulation






## Requirements
(Tu contenido aquí)

...

