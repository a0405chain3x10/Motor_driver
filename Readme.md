# Discrete H-Bridge DC Motor Driver

This repository contains the KiCad schematics and PCB layout files for a custom DC motor driver. Designed around a discrete MOSFET H-Bridge topology, the board incorporates dedicated gate drivers and a hardware-level logic safety interlock to prevent catastrophic shoot-through conditions.

<img width="833" height="594" alt="image" src="https://github.com/user-attachments/assets/27bd93b0-160d-41d6-b9ff-2f08d3542f7e" />

<img width="1105" height="863" alt="image" src="https://github.com/user-attachments/assets/c5b77902-c5ec-4276-a3d4-952a0f70b32a" />

<img width="1105" height="863" alt="image" src="https://github.com/user-attachments/assets/07342903-1e76-4050-a18f-4a6320075bc8" />

## Hardware Architecture
The motor driver is built to handle high-current DC loads using a robust discrete component approach:
*   **Power Delivery:** The main power stage accepts a 12V input via a dedicated screw terminal (J1), supported by a 220uF/25V electrolytic bulk capacitor (C7) to handle transient loads.
*   **Logic Power:** A separate 3.3V input (J3) is utilized to power the onboard logic gates and safely interface with low-voltage microcontrollers.
*   **H-Bridge Configuration:** The switching stage utilizes two PMOS (Q1, Q3) and two NMOS (Q2, Q4) transistors in a standard H-Bridge layout to control motor direction and speed.
*   **Gate Driving:** Two TC4428xOA ICs (U1, U2) are used to provide the necessary current to rapidly charge and discharge the MOSFET gates, ensuring highly efficient PWM switching and minimizing thermal dissipation.
*   **Motor Output:** The driven motor connects directly to the H-Bridge via a 2-pin screw terminal (J4) labeled `M+` and `M-`.

## Hardware Safety Interlock
To protect the H-Bridge from short circuits caused by simultaneous directional commands (shoot-through), the board features a physical logic-gate safety layer:
*   **Logic ICs:** The interlock circuit is constructed using standard DIP-packaged 74HC08 (AND) and 74HC04 (NOT/Inverter) logic chips.
*   **Signal Processing:** Raw `R` and `L` input signals from the control connector (J2) are processed through the logic gates to generate mutually exclusive `R_Safe` and `L_Safe` signals. 
*   **Fail-Safe Operation:** If both directional inputs are triggered simultaneously (either by software bug or electrical noise), the hardware logic overrides the input and prevents the active signals from reaching the TC4428xOA gate drivers, keeping the MOSFETs safely deactivated.

## PCB Layout & Design Features
The physical dual-layer board is optimized for current handling and thermal management:
*   **High-Current Routing:** The `M+` and `M-` motor output paths utilize exceptionally thick copper traces routed across both the top and bottom layers to handle heavy current loads without overheating.
*   **Thermal Management:** The four TO-220 MOSFETs (Q1-Q4) are aligned sequentially along the edge of the board, allowing them to share a single, continuous heatsink for optimal thermal dissipation.
*   **Mixed-Technology Assembly:** The design smartly combines through-hole components for robust mechanical connections (connectors, bulk capacitors, DIP logic ICs) with surface-mount (SMD) components for the gate drivers, decoupling capacitors, and pull-down resistors to save board space and optimize routing.

## Bill of Materials (BOM)

### Semiconductors & ICs
| Reference | Part | Package | Description | Quantity |
| :--- | :--- | :--- | :--- | :--- |
| U1, U2 | TC4428xOA | SOIC-8 (SMD) | 1.5A Dual High-Speed Gate Driver | 2 |
| Q1, Q3 | PMOS Transistor | TO-220 | High-Side P-Channel Power MOSFET | 2 |
| Q2, Q4 | NMOS Transistor | TO-220 | Low-Side N-Channel Power MOSFET | 2 |
| U5, U6 | 74HC08 | DIP-14 | Quad 2-Input AND Gate | 2 |
| U3, U4 | 74HC04 | DIP-14 | Hex Inverter (NOT Gate) | 2 |

### Passives (Resistors & Capacitors)
| Reference | Value | Package | Description | Quantity |
| :--- | :--- | :--- | :--- | :--- |
| C7 | 220uF / 25V | Radial (THT) | Bulk Electrolytic Decoupling Capacitor | 1 |
| C4, C6, C8 | 1uF / 25V | SMD | Ceramic Decoupling Capacitor | 3 |
| C3, C5, C9, C10, C11, C12, C13 | 100nF (0.1uF) | SMD | Ceramic Decoupling Capacitor | 7 |
| R1, R2, R3, R4 | 10 Ω | SMD | Gate Current Limiting Resistor | 4 |
| R5, R6, R7, R8 | 10k Ω | SMD | Input Signal Pull-down Resistor | 4 |
| R9, R10, R11, R12 | 47k Ω | SMD | Gate Pull-down Resistor | 4 |

### Connectors & Hardware
| Reference | Part | Description | Quantity |
| :--- | :--- | :--- | :--- |
| J1 | 2-Pin Screw Terminal | 12V Main Power Input | 1 |
| J4 | 2-Pin Screw Terminal | Motor Output (M+, M-) | 1 |
| J2 | 3-Pin Header/Terminal | Logic Inputs (R, L, GND) | 1 |
| J3 | 2-Pin Header/Terminal | 3.3V Logic Power Input | 1 |
| Heatsink | TO-220 Continuous | Multi-component heatsink across Q1-Q4 | 1 |

## Setup and Fabrication
1. Clone this repository to view or modify the design files in KiCad:
   ```bash
   git clone https://github.com/adityasoni0405/<your-repo-name>.git
   ```
2. Open `motor driver.kicad_pro` in KiCad to explore the schematic and PCB.
3. To fabricate the board, export the Gerber files directly from the `.kicad_pcb` layout interface. When ordering from a fabricator (e.g., JLCPCB, PCBWay), a standard 1.6mm thickness with 1oz copper is sufficient, but 2oz copper is recommended if driving high-current loads continuously.
