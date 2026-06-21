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
- **Two layers** out of the box: a numpad and a row of F-keys (great as a stream deck).
- **[ZMK Studio](https://zmk.dev/docs/features/studio) support** — change your layout live from a browser, no recompiling.
- **Board-agnostic 3D-printed case** — the controller bay accepts **both** the XIAO **and** the
  SuperMini footprints. Drop in a **Seeed XIAO** (nRF52840 / RP2040 / ESP32-S3) **or** an
  **ESP32-C3 / ESP32-S3 SuperMini** — among other similar-size boards. This prebuilt firmware
  targets the **Seeed XIAO nRF52840**.
- Fully open: the [keymap](config/vl03.keymap) is a plain text file you can edit and rebuild.

---

## What you need to build one

- A small dev board (this prebuilt firmware is for the **Seeed XIAO nRF52840**).
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

**A note on pin names:** the XIAO's solder pads are printed with plain numbers on the board
itself — `0`, `1`, `2` … `10`. Inside the ZMK config files those same pins are written as
`D0`–`D10`. They are the same thing: pad `0` = `D0`, pad `6` = `D6`, and so on. The tables
below use the **numbers you'll actually see on the board**.

If you wire it exactly like this, the prebuilt firmware just works — **no compiling needed**.

| Role | XIAO pads |
| --- | --- |
| **Columns** (4) | `0`, `5`, `7`, `8` |
| **Rows** (3) | `6`, `9`, `10` |
| **Encoder** A / B | `3` / `4` |
| **Encoder** common (middle pin) | `GND` |

### Which switch goes where

Each switch connects one **row** pad to one **column** pad (through its diode). The encoder's
push-button is just another key in the matrix (top-right corner).

|             | Col `0` | Col `5` | Col `7` | Col `8` |
| ----------- | :-----: | :-----: | :-----: | :----------: |
| **Row `6`**  |   `7`   |   `8`   |   `9`   | encoder push |
| **Row `9`**  |   `4`   |   `5`   |   `6`   |      –       |
| **Row `10`** |   `1`   |   `2`   |   `3`   |     `0`      |

### Diodes

This build uses a **`COL2ROW`** matrix. Wire each diode so it points **from the column to the
row** — the **stripe (cathode) faces the row** pad. The encoder's push-button needs a diode
too, exactly like the keys.

> If about half your keys don't register, or pressing two keys triggers a third, your diodes
> are likely backwards — flip them.

### Encoder

A standard EC11 has 3 pins on one side (**A · C · B**) and 2 pins on the other (the push-button):

- **A -> pad `3`**, **B -> pad `4`**, **C (middle) -> `GND`**
- The 2 push-button pins go into the **matrix** at the top-right slot (Row `6` to Col `8`, with a diode).

---

## Flashing the prebuilt firmware

1. Download the latest **`vl03`** firmware (a `.uf2` file) from this repo's **Actions** tab ->
   newest run -> **Artifacts**. (See the [ZMK guide](https://zmk.dev/docs/user-setup#installing-the-firmware) if you get stuck.)
2. Plug the numpad into your computer with USB.
3. **Double-tap the RESET button** on the XIAO quickly. A USB drive will pop up.
4. **Drag the `.uf2` file onto that drive.** It flashes and reboots itself. Done.

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

### Switching layers

**Press the encoder button + the `0` key at the same time** to toggle between the Numpad
and Function layers. The *same* combo flips you back. (It's a chord, so press both together.)

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
