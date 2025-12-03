Commands are defined by the transition from one colour block to the next as the interpreter travels across the program. The number of steps along the Hue Cycle and the Lightness Cycle in each transition determine the command to be executed. If the transition between colour blocks occurs via a slide across a white block, no command is executed.

![[piet-commands.png]]

- push: pushes the value of the colour block just exited on to the stack.
- pop: pops the top value off the stack and discards it
- add: pops the top two values off the stack, adds them, then pushes the result back on the stack
- subtract: pops the two top values off the stack, calculates the second top value minus the top value, and pushes the result back on the stack
- multiple: pops the two top values off the stack, multiplies them, and pushes the result back on the stack
- divide: pops the top two values off the stack, calculates the integer division of the second top value by the top value and pushes the result back on the stack
- mod: pops the top tow values off the stack, calculates the second top value modulo the top value, and pushes the result back on the stack. The result has the same sign as the divisor. If the top value is zero, this is a divide by zero error
- not: replaces the top value of the stack with 0 if it is non-zero, and 1 if it is zero
- greater: pops the top two values off the stack and pushes 1 onto the stack if the second top value is greater than the top value, and pushes 0 if it is not greater
- pointer: pops the top value off the stack and rotates the DP clockwise that many steps (anticlockwise if negative)
- switch: pops the top value off the stack and toggles the CC that many times (absolute value if negative)
- duplicate: pushes a copy of the top value on the stack onto the stack
- roll: pops the top two values off the stack and "rolls" the remaining stack entries to a depth equal to the second value popped, by a number of rolls equal to the first value popped. A single roll to depth *n* is defined as burying the top value on the stack *n* deep and bringing all values above it up by 1 place. A negative number of rolls rolls in the opposite direction. A negative depth is an error and the command is ignored.
- in: reads a value from STDIN as either a number or character, depending on the particular incarnation of this command and pushes it onto the stack. If no input is waiting on STDIN, this is an error and the command is ignored.
- out: pops the top value off the stack and prints it to STDOUT as either a number or character, depending on the particular incarnation of this command.

Any operations which cannot be performed are simply ignored.