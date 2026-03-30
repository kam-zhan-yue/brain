2026-03-18: Cat Coaster
- Learned how to use the subtract modifier to create pupils in eyes
2026-03-19: Potion
- Learned how to use the bevel tool to create curved corners
2026-03-21: Lamp
- Learned how to use the bezier curve to create curved tubes
- Experimented with lighting in rendering
2026-03-22: Mini Derek
- Learned how to use proportional editing to turn a sphere into an egg shape
- Used a lot of bevelling to create the beak and feet
2026-03-23: Pokeball
- Used bool modifier to create the centre indent
- Used solidify modifier to make the shell
- Used shade smooth and shade auto smooth to balance out the solidify modifier
2026-03-24: Sean
- Used sculpting tool and a subdivided cube to make the sea slug body
- Used a combination of array and curve modifiers and tilted them to make the dress
2026-03-25: Gameboy SP
- Used bool modifier and difference to create indents
- Extruded a rectangular prism from a half cylinder to create the hinge
- The 'Blender' way is to do things backwards, start from the most complicated
2026-03-26: Froggie
- Extrusion, subdivisions, material allocation
2026-03-28: iPhone
- No new concepts
2026-03-29: Ping Pong Bat
- No new concepts

## Shortcuts
- There is a search menu in F3
- Had to turn on 'tab to open pie menu'
- When selecting a loop in edit mode, pressing L will select half
- CTRL+T when selecting vertices will tilt the vertices
- When using the knife tool press C before cutting to enable cut through
- Doing CMD + Drag will deselect, but it is necessary to turn off Emulate Numpad
- U to open UV wrapping options

## Tips
- When selecting face loops, the position of the cursor on the face will determine if it selects horizontally or vertically
- To make things like belts, we can make a face loop, duplicate, then separate to make a new object.
- Doing inset + CTRL + drag will inset while simultaneously adding height/depth
- When texture painting, if the paint is going behind the texture, it is likely that the face orientations are off. Go into the Show Overlays Menu and click 'Face Orientations'. Blue faces are front-facing, and red faces are back-facing. Inverted normals throw off bump maps and smoothing. To solve this, go to Edit Mode, select everything and Shift+N to recalculate faces.
- With low poly assets, it is best to create a texture atlas that contains all of the colours in the scene. Then, UV unwrap the textures and assign their faces to a colour on the atlas
