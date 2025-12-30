Each of the surface parameters we need for a PBR pipeline can be defined or modelled by textures. Using textures gives us per-fragment control over how each specific surface point should react to light: whether that point is metallic, rough or smooth, or how the surface responds to different wavelengths of light.

![[43-5-pbr-pipeline.png]]

- Albedo: specifies the colour for each texel of the surface, or the base reflectivity if that texture is metallic. This is similar to the diffuse texture, but all lighting information is extracted from the texture. Diffuse textures often have slight shadows or darkened crevices
- Normal: specifies the normal for each fragment
- Metallic: specifies whether the texel is metallic or not. Based on how the PBR engine is setup, artists can author metalness as a grayscale value or as binary black or white
- Roughness: specifies how rough a texel is. The sampled roughness value influences the statistical microfacet orientations of the surface. A rougher surface gets wider and blurrier reflections, while a smooth surface gets focused and clear reflections. Some PBR engines expect a smoothness map instead of a roughness map.
- Ambient Occlusion: the extra shadowing factor of the surface and potentially surrounding geometry. If we have a brick surface, the albedo texture should have no shadowing information inside the brick's crevices. The AO map specifies these darkened edges as it's more difficult for light to escape. Taking ambient occlusion in account at the end of the lighting stage can significantly boost the visual quality of your scene.

Artists set and tweak these physically based input values on a per-texel basis and can base their texture values on the physical surface properties of real-world materials. This is one of the biggest advantages of a PBR render pipeline as these physical properties of a surface remain the same, regardless of environment or lighting setup, making life easier for artists to get physically plausible results.

Surfaces authored in a PBR pipeline can easily be shared among different PBR render engines, will look correct regardless of the environment they're in, and as a result look much more natural.