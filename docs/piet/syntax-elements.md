## Numbers
Each non-black, non-white colour block in a Piet program represents an integer equal to the number of codels in that block. Note that non-positive integers cannot be represented, although they can be constructed with operators. When the interpreter encounters a number, it does not necessarily do anything with it. In particular, it is not automatically pushed on to the stack.

The maximum size of integers is notionally infinite, though implementations may vary.

## Black Blocks and Edges
Black colour blocks and the edges of the program restrict program flow. If the Piet interpreter attempts to move into a black block or off an edge, it is stopped and the CC is toggled. The interpreter then attempts to move from its current block again. If it fails a second time, the DP is moved clockwise one step. These attempts are repeated, with the CC and DP being changed between alternate attempts. If after eight attempts the interpreter cannot leave its current colour block, there is no way out and the program terminates.

## White Blocks
White colour blocks are 'free' zones through which the interpreter passes unhindered. If it moves from a colour block into a white area, the interpreter "slides" through the white codels in the direction of the DP until it reaches a non-white colour block. If the interpreter slides into a black block or edge, it is considered restricted, otherwise it moves into the colour block so encountered. Sliding across white blocks into a new colour does not cause a command to be executed. In this way, white blocks can be used to change the current colour without executing a command, which is useful for coding loops.

Sliding across white blocks takes the interpreter in a straight line until it hits a coloured pixel or edge. It does not use the procedure above for determining where the interpreter emerged from non-white coloured blocks.