### Blender Preparations
- In order to have the textures exported alongside the model, you need to do File > External Data > Pack Resources
- To avoid having whacky transformations, do CMD + A > All Transforms
- If you have textures, make sure they are plugged into a Principled BSDF

### Exporting
- File > Export > FBX
- Change Path Mode to Copy
- Select the Embed Textures Icon next to Path Mode

### Unity
- Click on the FBX menu, select extract textures and extract materials