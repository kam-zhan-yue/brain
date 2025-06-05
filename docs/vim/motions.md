## Macros
- `q` to start a macro
- Then the next letter determines which register it is stored
- `q` to end the macro
- `@q` to do the macro again

## Windows
`^W` to select a window.
- `v` to paste the window
- `hjkl` to navigate
- `c` to close the window

These will open buffers
- `:w` to save a buffer

## Yanking and Registers
Each time you yank, or delete, or change, that gets put in a register.
- `:reg` to see all of the registers
- `"<number>p` to paste the text held at that register
- `"+y` to yank into system clipboard

## Navigation

### Line Numbers
- `:<linenumber>` or `<linenumber>G`

## Find and Replace
- Enter visual mode
- Press `:` to enter command mode
- Type the substitution command `s/old/new/g`
- Press `Enter` to execute
