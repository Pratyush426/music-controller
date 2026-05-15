# Hardware Music Controller (LPC2138)

This project is a simulation/prototype of a hardware-based music controller built around the ARM7 LPC2138 microcontroller. It features a complete user interface with tactile buttons, LED status indicators, a buzzer, and an LCD display to emulate the behavior of a physical music player. The project is designed using the Proteus Design Suite.

## Features

- **Microcontroller**: NXP LPC2138 (ARM7TDMI-S)
- **Display**: 16x2 Character LCD (8-bit interface)
- **User Inputs (Active Low Buttons)**:
  - Power ON/OFF
  - Play/Pause
  - Next Track / Previous Track
  - Volume Up / Volume Down
  - Music Mode Selection (POP, ROCK, JAZZ)
- **Status Indicators**:
  - **Red LED**: System Off / Standby Mode
  - **Green LED**: Active / Playing Mode
  - **Yellow LED**: Paused Mode
  - **Buzzer**: Audible feedback (2-second beep on system power toggle, continuous when playing)
- **Software Functionality**:
  - Edge-detection for reliable button presses.
  - Smooth scrolling text on the LCD for "Now Playing" information.
  - Real-time track, volume, and playback mode updates on the display.
  - Custom Phase Locked Loop (PLL) initialization for system clock configuration.

## Hardware Pin Mapping

### Inputs (Port 0)
| Component     | Pin   | Description |
|---------------|-------|-------------|
| Power Button  | P0.0  | Toggles system power state |
| Play Button   | P0.1  | Toggles Play/Pause state |
| Next Button   | P0.2  | Advances to next track |
| Prev Button   | P0.3  | Goes back to previous track |
| Vol+ Button   | P0.4  | Increases volume (max 10) |
| Vol- Button   | P0.5  | Decreases volume (min 1) |
| Mode Button   | P0.6  | Cycles through POP, ROCK, JAZZ |

### Outputs (Port 0 & Port 1)
| Component     | Pin   | Description |
|---------------|-------|-------------|
| Red LED       | P0.7  | System Off indicator |
| Green LED     | P0.8  | Playing indicator |
| Yellow LED    | P0.9  | Paused indicator |
| Buzzer        | P0.16 | Audio feedback |
| LCD RS        | P1.16 | LCD Register Select |
| LCD EN        | P1.18 | LCD Enable |
| LCD Data      | P1.24 - P1.31 | 8-bit LCD Data Bus |

## Project Structure

- `Headphone.pdsprj`: The Proteus Design Suite project file containing the schematic and simulation environment.
- `FIRMWARE/LPC2138_1/main.c`: The core C source code for the LPC2138 microcontroller.
- `FIRMWARE/LPC2138_1/LPC213x.h`: LPC2138 hardware register definitions.
- `FIRMWARE/LPC2138_1/crt0.S`: Assembly startup code.
- `FIRMWARE/LPC2138_1/LPC2138_FLASH.ld`: Linker script.

## Getting Started

### Prerequisites
- **Proteus Design Suite**: Required to open and run the `.pdsprj` simulation file.
- **GCC for ARM**: Required if you wish to modify and recompile the firmware.

### Running the Simulation
1. Open `Headphone.pdsprj` in Proteus Design Suite.
2. Click the **Play (Run Simulation)** button at the bottom left of the Proteus window.
3. Interact with the buttons in the schematic to power on the system, play music, change volume, and navigate tracks. Observe the LCD and LED outputs.

## Firmware Details

The firmware is written in C and compiled using GCC for ARM. The main loop continuously polls the button states, applies edge-detection logic to prevent multiple triggers from a single press, and updates the system state accordingly. The LCD is driven using custom 8-bit routines, featuring a non-blocking scroll timer to animate text on the second line of the display.

You can view the full C source code for the project in the [`main.c`](./main.c) file included in this repository.

## Screenshots

Below are some screenshots of the project layout and simulation:

![Screenshot 1](./Screenshot%202026-05-10%20233153.png)
![Screenshot 2](./Screenshot%202026-05-11%20014121.png)
![Screenshot 3](./Screenshot%202026-05-11%20014258.png)
![Screenshot 4](./Screenshot%202026-05-11%20014336.png)
![Screenshot 5](./Screenshot%202026-05-11%20014423.png)
