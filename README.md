# X0X37: X0X for the M-Vave SMK-37

**X0X37** is a port of **X0X**, the groovebox firmware for the M-Vave FM-1 by
[Charles Vestal](https://github.com/charlesvestal/fm1-x0x), to the **M-Vave SMK-37** keyboard (SMK-37 Pro, Elite or MKE-P37; same
JieLi AC791N chip as the FM-1, a different panel). X0X turns the instrument into a groovebox: a **909**, an
**808**, two **303s** with the TB-3PO line generator, and a **breakbeat player**, all playing at once, with
a shared reverb, tape delay and master compressor.

Unofficial: not affiliated with or endorsed by M-Vave or the X0X author.

> **Status: experimental.** Tested on an SMK-37 Pro. Install it only if you can recover the keyboard
> (see [Safety](#safety) and [Install and roll back](#install-and-roll-back)), at your own risk.

Everything about X0X itself (the parts, patterns, song, motion, the screens, the manual) is X0X's: read
the upstream [README-UPSTREAM.md](README-UPSTREAM.md) and the [manual](docs/MANUAL.md). This page covers
what is different on the SMK-37.

## Credits

- **X0X** by Charles Vestal: <https://github.com/charlesvestal/fm1-x0x> (manual, web installer and browser
  emulator for the FM-1 at <https://charlesvestal.github.io/fm1-x0x/>). The engines come from 9W9 and
  8W8 (athousanddetails), ER-99 (Matthew Cieplak), schwung-303 (Open303 by Robin Schmidt), schwung-tb3po
  (after the Phazerville TB_3PO applet) and BB Gen (mestela); see [LICENSING.md](LICENSING.md).
- **Felucca** by Leo Kuroshita (Hügelton Instruments): <https://github.com/hugelton/Felucca>, the FM-1
  platform X0X is built on (hardware layer, USB, update loader, storage).
- **SMK-37 board layer**: the board patch series of [Snoop37](https://github.com/genkigenki/Snoop37)
  (branch `smk37-board`), and the porting guide
  [genkigenki/fm1-to-smk37](https://github.com/genkigenki/fm1-to-smk37) (hardware facts, flash map, update
  path, backup and recovery). Port by genkigenki.

## What changed for the SMK-37

Every change is behind one build switch, `BOARD_SMK37` (`BOARD=smk37`). The normal FM-1 build of this
tree is byte for byte the same as upstream X0X's (`build/x0x.bin`, `build/loader/ota.bin`,
`build/x0x.fwsc`).

- Input: the SMK's key matrix, 37 velocity keys, 18 buttons, 8 knobs, 16 pads, 4 faders, 2 wheels
  (`firmware/hal/smk_input.h`).
- Display and pads: the SMK's LCD wiring and init, the 16 RGB pad LEDs (`fm1_lcd_hw.h`, `lcd.c`).
- Flash: the SMK flash map with an explicit allow-list and compile-time checks
  (`firmware/hal/fm1_flash_map.h`); X0X's store, the update staging and two sample slots moved into the
  SMK's free areas.
- Update path: the SMK identities (`SMK-37 Pro_900`, loader `ota-SMK-37 Pro_900`) and package layout.
- No MIDI on the TRS jack: on the SMK the jack is MIDI OUT (not driven yet), and the FM-1's MIDI IN pin is a
  key-matrix row there. USB MIDI and USB audio are X0X's, unchanged.
- X0X's user interface (`firmware/src/app/ui.c`) is unchanged; the SMK controls are mapped onto the
  FM-1's (below).

## Controls on the SMK-37

The SMK-37's controls stand in for the FM-1's, so X0X's manual applies as written. Read the FM-1 name
in the manual, press the SMK control in the right-hand column.

### Keys and pads

| SMK-37 | X0X (FM-1 name) | What it does in X0X |
|---|---|---|
| Keys F3 to G5 (the middle 27) | the FM-1's 27 keys | White keys: steps (HOME: patterns). Black keys: drum tracks, break slices (HOME: part mutes). With REC on a 303: a keyboard. |
| 16 pads | the 16 white keys | The steps 1-16 of the page (HOME: patterns). Top row = steps 1-8, bottom row = 9-16 (a 303 with REC, a keyboard: the low notes on the bottom row). A pad sets the same key as the keybed's white key. |
| Pad lights | the white keys' lights | Bright: on / chosen; dim: there, not chosen (as the manual's key-light table); in the colour of the part on screen. |
| Keys C3 to E3, G#5 to C6 (the outer 10) | (none) | Not used. |

The keybed has no lights: what the FM-1 shows on its **black keys** (the selected drum track, slices, part
mutes) is shown on the screen only.

### Buttons

| SMK-37 button | X0X (FM-1 name) | What it does in X0X |
|---|---|---|
| PATCH | HOME | The pattern (again: the song); in a list: back; a question: no |
| SEQ | SEQ | The part's steps (again: next page) |
| PARA | EDIT | The part's sound (again: next page) |
| ARP | ARP | TB-3PO (303 only) |
| FX | FX | The effects |
| NOTE REPEAT | LFO | The mixer and the master; held + keys: slide |
| CHORD | ENV | Held + keys: accent |
| GLOBE | GLO | The settings |
| SCALE | SEL | The screen as a list; in a list: run; a question: yes |
| BT | SAVE | Save everything |
| SEQ PLAY | PLAY | Start / stop (PATCH + SEQ PLAY: redo) |
| SEQ REC | REC | Recording on / off (PATCH + SEQ REC: undo) |
| OCTAVE- / OCTAVE+ | OCT- / OCT+ | Step pages 1-16 ... 49-64 (HOME, SONG: patterns 1-16 / 17-32; 303 keyboard: octave) |
| FADER BANK | (SMK only) | Switches the faders between bank A and bank B (its light: bank B) |
| `<` / `>` | (SMK only) | One step of SELECT back / forward: the page, a list's row (not with PATCH held, where SELECT is the tempo) |
| KNOB BANK | (none) | Not used (X0X has no hidden settings menu: GLOBE opens the settings) |

Button lights are on or off (the SMK has no dim level, so X0X's "glow" setting has no effect).

### Knobs, faders, wheels

| SMK-37 | X0X (FM-1 name) | What it does in X0X |
|---|---|---|
| K1 | MASTER | Volume (a knob here, not a pot: it starts at the FM-1's power-on level) |
| K2 | SELECT | Page; list row; PATCH held + K2: tempo |
| K3 | ALGORITHM | The part: 909, 808, 303A, 303B, BREAK |
| K4 | PRESETS | The next pattern |
| K5 to K8 | KNOB 1 to 4 | The four values at the bottom of the screen |
| Faders 1-4, bank A | (SMK only) | Levels of 909, 808, 303 A, 303 B |
| Faders 1-4, bank B | (SMK only) | BREAK level, reverb return, delay return, master FILTER (middle: off) |
| Pitch wheel | (SMK only) | The 303 on screen: its TUNE, about +-1.5 semitones, while held |
| Mod wheel | (SMK only) | The 303 on screen: its CUTOFF, opened from the set value to the top, while held |

The faders set the same values as the knobs (they are saved with the project, and moved while REC is on
they record knob motion). A fader takes over only once it is moved, also after a bank change, so nothing
jumps. The wheels never change what is saved: let go (or pick another part) and the 303 sounds as set.

### Power-on and emergency combos

| Hold | When | Does |
|---|---|---|
| OCTAVE- + OCTAVE+ | at power-on | HARDWARE CALIBRATION: teaches each FM-1 button and knob name to an SMK control (all but K1) |
| OCTAVE- + OCTAVE+ | 5 s, while running | The chip's update mode (UBOOT), for a recovery tool |
| SEQ PLAY | in SAFE MODE | Tries X0X again (after two failed starts X0X opens SAFE MODE: no sound, USB on) |

### Velocity

The keys and pads measure velocity with the SMK's own factory calibration (read, never written). X0X does
not use velocity: a key or pad is a step or a track, as on the FM-1.

## Build

The toolchain and SDK files are the same as for X0X: see [BUILDING.md](BUILDING.md). Linux x86-64 (or
WSL2) builds natively; macOS uses Docker, as upstream.

```sh
tools/get_toolchain.sh                 # the JieLi toolchain, once
./build.sh                             # FM-1: build/x0x.fwsc (identity FM-1_900), unchanged from upstream
BOARD=smk37 ./build.sh                 # SMK-37 Pro: build/x0x-smk37.fwsc (identity SMK-37 Pro_900)
BOARD=smk37 ./build.sh --release 0.1   # a release: build/x0x-smk37-0.1.fwsc
tests/run_tests.sh                     # X0X's tests plus the SMK-37 board tests
```

Build options for the SMK: `FELUCCA_DATA_FAMILY` (default `X0X`) and `FELUCCA_DATA_UNTAGGED_OK` (default
`0`) set the store owner (below); `X0X_SMK_LCD_BAUD=n` sets the LCD's SPI clock to 60 MHz / (n + 1)
(default 4 = 12 MHz, the stock firmware's rate; the FM-1 build uses 60 MHz).

## Safety

Read the guide's [update and recovery chapter](https://github.com/genkigenki/fm1-to-smk37/blob/main/docs/02-update-and-recovery.md)
before you install anything.

1. **Make a full flash backup first**, before any custom firmware is installed (the guide, section 3,
   Method A): the whole 1 MiB read twice in the chip's update mode (UBOOT) with
   [jl-uboot-tool](https://github.com/kagaimiq/jl-uboot-tool), both reads identical, offset `0xC1224`
   reading `trimdat`. Keep it in two places. Parts of it are unique to your unit and cannot be downloaded
   anywhere.
2. **Never write these ranges**, with any tool, unless you restore them from your own backup because they
   were damaged:

   | Range | What it is |
   |---|---|
   | `0x00000-0x03FFF` | the boot head (SPL, chip configuration) |
   | `0xC0000-0xC0FFF` | BTIF: Bluetooth pairing |
   | `0xC1000-0xC1FFF` | USRTRIM: the factory key, pad and wheel calibration |
   | `0xF4000-0xFDFFF` | USR: the stock firmware's resources (needed for a return to stock) |
   | `0xFF000-0xFFFFF` | key_mac: the unit's keys and addresses |

   X0X37 never writes them: its flash driver has an allow-list with compile-time checks and a second
   guard, and the update loader writes only the app area. What X0X37 writes: the app `0x04000-0x9CFFF`
   (through the update loader), its store `0x9D000-0xAEFFF` and the store owner tag `0xAF000`, the update
   staging `0xBB000-0xBFFFF`, and the two sample slots `0xC2000-0xE9FFF`.
3. **Store owner.** Several FM-1 firmwares are ported to the SMK-37 with this same board layer and
   the same store area. X0X37 tags its store as `X0X`; at power-on a store written by another port, or an
   untagged one, is **erased** before X0X37 reads it (the screen says OLD DATA CLEARED). Save what you
   want to keep from another firmware before you install X0X37. Samples, calibration, Bluetooth pairing
   and key_mac are never touched.
4. Do not unplug the keyboard while an update runs.

## Install and roll back

**Install** (command line; needs Python 3 with `mido` and `python-rtmidi`; close every other program that
uses the keyboard's MIDI port):

```sh
python3 tools/fm1_install.py --info                      # the connected keyboard's identity, e.g. SMK-37 Pro_016
python3 tools/fm1_install.py build/x0x-smk37.fwsc        # install; the keyboard restarts into X0X37
```

The installer only sends an SMK package to an SMK-37 (and an FM-1 package to an FM-1). X0X's web
installer and break-loop page are made for the FM-1 and are not adapted. If an install is cut off,
power-cycle the keyboard and run the same command again: the update loader resumes.

**Break loops**: the SMK has **two** user sample slots (the FM-1 has three); upload with
`tools/upload_breaks.py --slots 1,2 ...` (slot 3 is refused).

**Roll back** to the stock firmware: install M-Vave's official package for your model, either with M-Vave's
updater or with `python3 tools/fm1_install.py --force OFFICIAL.fwsc` (`--force`: it has no X0X loader;
the installer handles its 36-block layout). Your calibration is untouched. The stock firmware then finds
X0X37's data in its VM and sample areas; for a clean state write those ranges back from your backup, or
erase them to `0xFF`, in UBOOT (the guide, sections 5 and 6).

**Recovery**: after two failed starts X0X opens SAFE MODE (no sound, USB on: install again); after four
it drops into UBOOT by itself. OCTAVE- + OCTAVE+ held 5 s also enters UBOOT. From UBOOT a backup can be
written back with jl-uboot-tool; the guide, section 5, has the rules (whole 4 KiB sectors, never the
ranges above).

None of these steps has been tried with X0X37 on a real unit yet.

## License

GPL-3.0-only, as X0X and Felucca: see [LICENSE](LICENSE) and [LICENSING.md](LICENSING.md) (the origin
and license of every part). The SMK-37 changes are under the same license. This repository contains
no stock firmware, no flash dumps and no vendor code: the official SMK-37 firmware comes from
M-Vave, and the JieLi toolchain and SDK files are downloaded by the build tools.
