# MicroHub — Raspberry Pi LED Matrix Menu System

A menu-driven application launcher for an 8x8 LED matrix display, built on a Raspberry Pi. Navigate between mini-apps — including a word clock and Snake — using two physical push buttons, with menu options scrolling across the display.

## Overview

- **Hardware:** Raspberry Pi, MAX7219 8x8 LED matrix display (SPI), 2× push buttons, analog joystick + MCP3008 ADC (SPI, for Snake)
- **Language:** Python
- **Key libraries:** `luma.led_matrix`, `luma.core`, `gpiozero`, `Adafruit_MCP3008` (or equivalent SPI ADC driver)
- **University paper:** ENSE810 — AUT

## How It Works

`Main.py` runs the top-level menu loop, cycling through available apps (`WordClock`, `Snake`) and calling `DisplayMenuText.py` to scroll the current option's name across the LED matrix.

Two buttons control menu navigation:
- **Next** — clears the display and advances to the next menu item
- **Select** — launches the selected app as a subprocess

Each app runs as its own independent Python script, launched via `subprocess.run()`, and takes over the display — and, where relevant, the input method — until the user exits back to the menu.

```
[Main.py] → menu loop → [DisplayMenuText.py] → scroll text on LED matrix
                              │
                    (on select) ▼
                    [WordClock.py] / [Snake.py]
```

## Apps

### Word Clock

Displays the current system time as a sentence (e.g. "TWENTY PAST FOUR") on the LED matrix, using a custom character-position mapping to light up individual letters to form the correct phrase. Time is read from the system clock and updated continuously; a button press exits back to the menu.

### Snake

A single-player Snake implementation on the 8x8 grid, using continuous analog joystick input read via an MCP3008 ADC over SPI. The joystick's X/Y axis values are thresholded to detect up/down/left/right, with a direction-lock (changeFrame) preventing the snake from reversing directly into itself. The snake grows by one segment each time it eats a randomly placed food point (food re-rolls if it lands on the snake's own body), and wraps around all four edges of the display rather than ending the game on a wall hit. The Select button (GPIO 26) doubles as the exit button within Snake, quitting immediately back to the menu. Colliding with the snake's own body ends the game and shows a scrolling 'Score: N' message before quitting.

## Setup

### Hardware

- Raspberry Pi (any model with SPI-capable GPIO)
- MAX7219 LED matrix module wired via SPI (port 0, device 0)
- Two push buttons wired to GPIO pins 19 (next) and 26 (select)
- Analog joystick wired to an MCP3008 ADC (SPI) for Snake's directional input

### Software

```bash
pip install luma.led_matrix gpiozero Adafruit_MCP3008
```

### Running

```bash
python3 Main.py
```
