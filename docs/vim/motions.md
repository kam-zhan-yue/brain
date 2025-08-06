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

## Handy Stuff
- SHIFT + C: Removes all the text from the cursor to the end of the file
- `cib` Change in Bracket!
- `ci"`: Changes the text inside double quotes. Place the cursor anywhere within or on the double quotes, then press `ci"`. The text inside the quotes will be deleted, and Vim will enter insert mode, allowing new text to be typed.
- `ci'`: Changes the text inside single quotes. Similar to double quotes, place the cursor within or on the single quotes and press `ci'`.
- `ci(` or `ci)`: Changes the text inside parentheses.
- `ci[` or `ci]`: Changes the text inside square brackets.
- `ci{` or `ci}`: Changes the text inside curly braces.