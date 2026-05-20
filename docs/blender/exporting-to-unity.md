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

# Animtions
- Create the animation using keyframes, etc
- Make multiple animations using Action Editor
- If you want to see all the animations and adjust things like reversing, speed, etc, you can open the Nonlinear Animation window.
- To delete animations, go to the Outliner window (top right hierarchy-like window), change ht the view to Blender File, then remove from Actions
- Go to the Dope Sheet and