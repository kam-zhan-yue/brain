[See this for better stuff](https://ikorihn.github.io/digitalgarden/note/Keyball44-QMK%E3%82%92%E3%82%AB%E3%82%B9%E3%82%BF%E3%83%9E%E3%82%A4%E3%82%BA%E3%81%99%E3%82%8B)

The processor runs software that is responsible for detecting button presses and informing the computer when keys are pressed. QMK Firmware fills the role of that software, detecting button presses and passing that information on to the host computer. When you build your custom keymap, you are creating an executable program for your keyboard.

## Setting up the Environment

1. Install QMK
2. Run QMK Setup
3. Test the Build Environment

```shell
curl -fsSL https://install.qmk.fm | sh
qmk setup
qmk compile -kb <keyboard> -km default
```

> The keyball is not included in QMK

## Building Firmware

```
# Configure Environment Defaults
qmk config user.keyboard=keyball/keyball44
qmk config user.keymap=kam-zhan-yue
```

Create a new keymap with
```
qmk new-keymap
```

Open `keymap.c`, the layers are below

```
const uint16_t PROGMEM keymaps[][MATRIX_ROWS][MATRIX_COLS] = {
```

## Building Firmware

After making changes to the keymap, we need to build the firmware.
`
```
qmk compile
```

## Flashing Firmware

Running the `flash` command will run a `make` to build the firmware. This is contained in a `.hex` file. We then need to put the Keyboard into DFU (Bootloader) Mode.

```
qmk flash
```

> On the Keyball, we can put it to bootloader by pressing the reset button twice.