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

<table>
  <tr>
    <th colspan="3">Inputs (Port 0)</th>
    <th width="50"></th>
    <th colspan="3">Outputs (Port 0 & Port 1)</th>
  </tr>
  <tr>
    <td><b>Component</b></td><td><b>Pin</b></td><td><b>Description</b></td>
    <td></td>
    <td><b>Component</b></td><td><b>Pin</b></td><td><b>Description</b></td>
  </tr>
  <tr>
    <td>Power Button</td><td>P0.0</td><td>Toggles system power state</td>
    <td></td>
    <td>Red LED</td><td>P0.7</td><td>System Off indicator</td>
  </tr>
  <tr>
    <td>Play Button</td><td>P0.1</td><td>Toggles Play/Pause state</td>
    <td></td>
    <td>Green LED</td><td>P0.8</td><td>Playing indicator</td>
  </tr>
  <tr>
    <td>Next Button</td><td>P0.2</td><td>Advances to next track</td>
    <td></td>
    <td>Yellow LED</td><td>P0.9</td><td>Paused indicator</td>
  </tr>
  <tr>
    <td>Prev Button</td><td>P0.3</td><td>Goes back to previous track</td>
    <td></td>
    <td>Buzzer</td><td>P0.16</td><td>Audio feedback</td>
  </tr>
  <tr>
    <td>Vol+ Button</td><td>P0.4</td><td>Increases volume (max 10)</td>
    <td></td>
    <td>LCD RS</td><td>P1.16</td><td>LCD Register Select</td>
  </tr>
  <tr>
    <td>Vol- Button</td><td>P0.5</td><td>Decreases volume (min 1)</td>
    <td></td>
    <td>LCD EN</td><td>P1.18</td><td>LCD Enable</td>
  </tr>
  <tr>
    <td>Mode Button</td><td>P0.6</td><td>Cycles through POP, ROCK, JAZZ</td>
    <td></td>
    <td>LCD Data</td><td>P1.24 - P1.31</td><td>8-bit LCD Data Bus</td>
  </tr>
</table>

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
1. Launch **Proteus Design Suite** on your machine.
2. Open the `Headphone.pdsprj` file located in the root of this repository.
3. Click the **Play (Run Simulation)** button located at the bottom left corner of the Proteus workspace.
4. **Interact with the Virtual Hardware**:
   - Press the **Power** button (P0.0) to turn the system on. You will hear a 2-second beep and see the LCD initialize.
   - Use the **Play**, **Next**, **Prev**, and **Mode** buttons to control the music playback simulation.
   - Adjust the volume using the **Vol+** and **Vol-** buttons and observe the real-time updates on the 16x2 LCD display.
   - Observe the LED indicators (Red for Off/Standby, Green for Playing, Yellow for Paused).
5. To stop the simulation, click the **Stop** button in Proteus.

## Backend (Embedded) Architecture

The "backend" of this hardware project is the embedded C firmware running on the bare-metal ARM7 microcontroller. The architecture is designed around a single-threaded super-loop (infinite `while(1)` loop) with non-blocking timing mechanisms.

### 1. Hardware Initialization
- **Phase Locked Loop (PLL)**: The system clock is configured using custom PLL routines to ensure the ARM7 core runs at the correct frequency for precise timing.
- **GPIO Configuration**: Port directions (input/output) are explicitly defined using `IO0DIR` and `IO1DIR` registers.
- **LCD Initialization**: The 16x2 LCD is initialized in 8-bit mode via custom `cmd()` and `dat()` routines toggling the `LCD_RS` and `LCD_EN` pins.

### 2. The Super-Loop State Machine
The core logic resides in an infinite loop that manages the system states:
- **Input Polling & Edge Detection**: The firmware continuously polls the Port 0 pins. It implements a software-based edge-detection mechanism by comparing the current pin state (`curr`) with the state from the previous iteration (`prev`). This prevents a single physical button press from registering multiple times.
- **State Variables**: 
  - `power_state`: Acts as a master gate. If 0, all other inputs are ignored and the system enters standby (Red LED on).
  - `play_state`, `track_idx`, `volume`, `music_mode`: Variables that dictate the current playback context.
- **Non-Blocking Display Animation**: Instead of using blocking `delay_ms()` calls which would freeze the button inputs, the scrolling "Now Playing" text on the LCD is driven by a `scroll_timer` variable incremented every loop iteration. Once it hits a threshold, the text shifts by one character, ensuring the UI remains highly responsive to user inputs.

You can view the full C source code for the project in the [`main.c`](./main.c) file included in this repository.

## Screenshots

Below are screenshots of the project layout and simulation in action:

<table align="center">
  <tr>
    <td><img src="./Screenshot%202026-05-10%20233153.png" alt="Screenshot 1"></td>
    <td><img src="./Screenshot%202026-05-11%20014121.png" alt="Screenshot 2"></td>
  </tr>
  <tr>
    <td><img src="./Screenshot%202026-05-11%20014258.png" alt="Screenshot 3"></td>
    <td><img src="./Screenshot%202026-05-11%20014336.png" alt="Screenshot 4"></td>
  </tr>
  <tr>
    <td colspan="2" align="center"><img src="./Screenshot%202026-05-11%20014423.png" alt="Screenshot 5" width="50%"></td>
  </tr>
</table>
