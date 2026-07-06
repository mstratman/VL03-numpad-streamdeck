# VL03 — Numpad + Encoder Macropad

A hand-wired **10-key numpad with a rotary encoder**, powered by [ZMK](https://zmk.dev).
It works as a normal numpad *and* as a mini **stream deck** — flip to the second layer and
every key becomes an F13–F21 hotkey you can bind in OBS, Voicemeeter, or anything else.

| As a stream deck | As a numpad |
| :---: | :---: |
| ![VL03 used as a stream deck](Media/pic%20as%20stream%20deck.jpg) | ![VL03 used as a numpad](Media/pic%20as%20numpad.jpg) |

```
  7   8   9   (encoder)
  4   5   6
  1   2   3    0
```

> The encoder **rotates for volume** and you **press it to mute**.

---

## Features

- **10 keys + a clicky rotary encoder** (volume / mute).
- **Three layers** out of the box: a numpad, a row of F-keys (great as a stream deck), and
  an RGB control layer for an optional WS2812B lighting panel.
- **[ZMK Studio](https://zmk.dev/docs/features/studio) support** — change your layout live from a browser, no recompiling.
- **Board-agnostic 3D-printed case** — the controller bay accepts **both** the XIAO **and** the
  SuperMini footprints. Drop in a **Seeed XIAO** (nRF52840 / RP2040 / ESP32-S3), an
  **ESP32-C3 / ESP32-S3 SuperMini**, or a **Waveshare RP2040-Zero** — among other similar-size
  boards. This prebuilt firmware targets the **Waveshare RP2040-Zero** and is **USB-only**
  (the RP2040 has no radio, so there's no Bluetooth on this build).
- Fully open: the [keymap](config/vl03.keymap) is a plain text file you can edit and rebuild.

---

## What you need to build one

- A small dev board (this prebuilt firmware is for the **Waveshare RP2040-Zero**).
- 10 mechanical switches + keycaps.
- 1 x **EC11 rotary encoder** (the kind with a push-button shaft).
- 11 x **1N4148 diodes** (one per switch *and* one for the encoder's push-button).
- Wire, a soldering iron, and the 3D-printed case (below).
- A USB-C cable.

> **New to keyboards?** A "matrix" just means the switches are wired in a grid of **rows**
> and **columns** so a handful of controller pins can read many keys. Each switch needs a
> **diode** so the board can tell which keys are pressed when several are held at once.

---

## 3D-printed case

The case is designed to be **controller-agnostic** — print it once and drop in whichever
supported board you have (XIAO or SuperMini footprint).

MakerWorld: **<https://makerworld.com/en/models/2961074>**

---

## Build video

A full step-by-step build walkthrough:

YouTube: **<https://www.youtube.com/watch?v=3G4t_2rlGYE>**

---

## Wiring (for the prebuilt firmware)

![The hand-wired diode matrix inside the VL03](Media/pic%20of%20handwiring.jpg)

**A note on pin names:** the Waveshare RP2040-Zero's header pads are printed `0`–`29` on the
board itself (its silkscreen calls them `GP0`–`GP29`). Inside the ZMK config files those same
pins are written as `GP0`–`GP29`. The tables below use the **numbers you'll actually see on
the board**.

If you wire it exactly like this, the prebuilt firmware just works — **no compiling needed**.

| Role | RP2040-Zero pads |
| --- | --- |
| **Columns** (4) | `GP0`, `GP1`, `GP2`, `GP3` |
| **Rows** (3) | `GP4`, `GP5`, `GP6` |
| **Encoder** A / B | `GP7` / `GP8` |
| **Encoder** common (middle pin) | `GND` |
| **RGB panel** data (DIN) | `GP9` |
| **RGB panel** power / ground | `5V` / `GND` |

> `GP10` is reserved by the firmware for the RGB panel's driver (a quirk of how
> the RP2040 sends the WS2812 data signal) but isn't wired to anything —
> leave it unconnected.

### Which switch goes where

Each switch connects one **row** pad to one **column** pad (through its diode). The encoder's
push-button is just another key in the matrix (top-right corner).

|              | Col `GP0` | Col `GP1` | Col `GP2` | Col `GP3` |
| ------------ | :-------: | :-------: | :-------: | :----------: |
| **Row `GP4`**  |    `7`    |    `8`    |    `9`    | encoder push |
| **Row `GP5`**  |    `4`    |    `5`    |    `6`    |      –       |
| **Row `GP6`**  |    `1`    |    `2`    |    `3`    |     `0`      |

### Diodes

This build uses a **`COL2ROW`** matrix. Wire each diode so it points **from the column to the
row** — the **stripe (cathode) faces the row** pad. The encoder's push-button needs a diode
too, exactly like the keys.

> If about half your keys don't register, or pressing two keys triggers a third, your diodes
> are likely backwards — flip them.

### Encoder

A standard EC11 has 3 pins on one side (**A · C · B**) and 2 pins on the other (the push-button):

- **A -> pad `GP7`**, **B -> pad `GP8`**, **C (middle) -> `GND`**
- The 2 push-button pins go into the **matrix** at the top-right slot (Row `GP4` to Col `GP3`, with a diode).

### RGB panel (optional)

A **WS2812B 4x4 (16-LED) panel** can be added as a lighting accessory:

- Panel **DIN -> pad `GP9`**.
- Panel **5V -> `5V`**, panel **GND -> `GND`**.
- A bulk capacitor (~470-1000uF) across the panel's 5V/GND, and a ~300-470ohm
  resistor in series on the data line near the first LED, are both cheap
  insurance against flicker and voltage spikes.
- The RP2040 drives the data line at 3.3V, which is under WS2812B's nominal
  spec (~3.5V) but usually works fine over a short wire. If you see flicker
  or stuck pixels, add a logic-level shifter (e.g. 74AHCT125) or power the
  panel from ~4.5V instead of a full 5V.
- 16 LEDs at full brightness can draw close to 1A, more than a USB port
  reliably supplies — the firmware caps brightness at 50% by default
  (`CONFIG_ZMK_RGB_UNDERGLOW_BRT_MAX` in [`config/vl03.conf`](config/vl03.conf)).

---

## Flashing the prebuilt firmware

1. Download the latest **`vl03`** firmware (a `.uf2` file) from this repo's **Actions** tab ->
   newest run -> **Artifacts**. (See the [ZMK guide](https://zmk.dev/docs/user-setup#installing-the-firmware) if you get stuck.)
2. **Hold down the BOOT button** on the RP2040-Zero, then plug it into your computer with USB
   (or hold BOOT and tap RESET if it's already plugged in). A USB drive named **`RPI-RP2`**
   will pop up.
3. **Drag the `.uf2` file onto that drive.** It flashes and reboots itself. Done.

> **Something acting weird after an update?** This repo also builds a `settings_reset`
> firmware. Flash that `.uf2` the same way to wipe stored settings, then flash `vl03` again.

---

## Default layout

### Layer 0 — Numpad
```
 7   8   9   (mute)
 4   5   6
 1   2   3    0
```
Encoder: **rotate = volume down / up**, **press = mute**.

### Layer 1 — Function / Stream-deck
```
 F13  F14  F15  (mute)
 F16  F17  F18
 F19  F20  F21   (Studio unlock)
```
Encoder: **rotate = volume down / up**, **press = mute**.

| Key | 7 | 8 | 9 | 4 | 5 | 6 | 1 | 2 | 3 | 0 |
| --- | - | - | - | - | - | - | - | - | - | - |
| **Sends** | F13 | F14 | F15 | F16 | F17 | F18 | F19 | F20 | F21 | *(unlock)* |

### Layer 2 — RGB (controls the WS2812B panel, if installed)
```
 next     prev     speed+   (toggle)
 speed-   hue-     hue+
 sat-     sat+     bright-  bright+
```
Encoder: **rotate = volume down / up** (same as the other layers — lighting controls
don't take over the encoder).

| Key | 7 | 8 | 9 | (enc push) | 4 | 5 | 6 | 1 | 2 | 3 | 0 |
| --- | - | - | - | - | - | - | - | - | - | - | - |
| **Does** | next effect | prev effect | speed up | on/off | speed down | hue down | hue up | sat down | sat up | bright down | bright up |

### Switching layers

- **Encoder button + `0`** toggles between the **Numpad** and **Function** layers.
- **Encoder button + `7`** toggles between the **Numpad** and **RGB** layers.

Both are chords — press both keys together — and each flips you back the same way.
(To get from Function to RGB or back, pass through Numpad first.)

---

## Use it as a stream deck (OBS, etc.)

The Function layer sends **F13–F21** — keys that don't exist on a normal keyboard, so they
won't collide with anything. That makes them ideal dedicated hotkeys.

In **OBS Studio**: `Settings -> Hotkeys`, click the field for an action (e.g. *Switch to Scene*),
then — while on the Function layer — tap a numpad key. OBS records `F13` (etc.) and you're set.
The same trick works in Streamlabs, Voicemeeter, Discord, and most software with custom hotkeys.

---

## ZMK Studio (edit your layout live)

**[ZMK Studio](https://zmk.dev/docs/features/studio)** is a free app (web: <https://studio.zmk.dev>,
or desktop) that lets you remap keys **live over USB** — no editing files, no recompiling.
This firmware ships with it enabled.

For safety the keyboard boots **locked**, so a stray keypress can't scramble your layout. To
make changes you have to **unlock** it first.

**Unlock key (stock firmware): the `0` key on the Function layer.**

1. Plug in over USB and open <https://studio.zmk.dev> (use Chrome or Edge), then **Connect**.
2. Switch to the **Function layer**: press the **encoder button + `0`** together.
3. Tap the **`0`** key — that runs `studio_unlock`. You can now edit freely in Studio.

The board automatically re-locks when you disconnect or after a period of inactivity.

---

## Different wiring or a different controller?

Used other pads? Building on an **ESP32-C3 / S3 SuperMini** or another board? Don't hand-edit
the config — the easiest path is the **Shield Wizard**, a point-and-click tool that generates a
ready-to-build ZMK repo for *your* exact wiring:

**<https://shield-wizard.genteure.workers.dev/>**

Pick your controller, click the keys to match your wiring, add the encoder, and it spits out
a repo just like this one. (This project was generated with it too.)

---

## Repo layout

| Path | What it is |
| --- | --- |
| [`config/vl03.keymap`](config/vl03.keymap) | Your keymap — layers, combos, encoder. Edit this. |
| [`config/vl03.conf`](config/vl03.conf) | Feature switches (ZMK Studio is enabled here). |
| [`build.yaml`](build.yaml) | Tells GitHub Actions which firmware to build. |
| [`boards/shields/vl03/`](boards/shields/vl03) | The hardware definition (matrix, pins, encoder, layout). |

---

*Built with [ZMK](https://zmk.dev). Edit your keymap with the [ZMK keymap docs](https://zmk.dev/docs/keymaps).*
