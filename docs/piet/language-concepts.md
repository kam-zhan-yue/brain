[See documentation here](https://www.dangermouse.net/esoteric/piet.html)
## Colours
Piet uses 20 distinct colours. The 18 colours in the first 3 rows of the table are related cyclically in the following two ways:
- Hue Cycle: red -> yellow -> green -> cyan -> blue -> magenta -> red
- Lightness Cycle: light -> normal -> dark

"Light" is considered to be one step "darker" than "dark" and vice versa. White and black do not fall into either cycle.

## Codels
Piet code takes the form of graphics made up of the recognised colours. Individual pixels of colour are significant in the language, so it is common for programs to be enlarged for viewing so that the details are easily visible. In such enlarged programs, the term "codel" is used to mean a block of colour equivalent to a single pixel of code.

## Colour Blocks
The basic unit of Piet is the colour block. A colour block is a contiguous block of any number of codels of one colour, bounded by blocks of other colours or by the edge of the program graphic. Blocks of colour adjacent only diagonally are not considered contiguous. A colour block may be any shape and may have "holes" of other colours inside it, which are not considered part of the block.

## Stack
Piet uses a stack for storage of all data values. Data values exist only as integers, though they may be read in or printed as Unicode character values with appropriate commands. The stack is notionally infinitely deep, but implementations may elect to provide a finite maximum stack size. If a finite stack overflows, it should be treated as a runtime error.

## Program Execution
The Piet language interpreter begins executing a program in the colour block which includes the upper left codel of the program. The interpreter maintains a Direction Pointer (DP), initially pointing to the right. The DP may point either right, left, down or up. The interpreter also maintains a Codel Chooser (CC), initially pointing left. The CC may point in either left or right.

The directions of the DP and CC will often change during program execution.

As it executes, the interpreter traverses the colour blocks of the program under the following rules:
1. The interpreter finds the edge of the current colour block which is furthest in the direction of the DP
2. The interpreter finds the codel of the current colour block on that edge which is furthest to the CC's direction of the DP's direction of travel
3. The interpreter travels from that codel into the colour block containing the codel immediately in the direction of the DP