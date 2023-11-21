# unity-mobile-optimization

**Table of optimization contents:**
- [Programing](#Programing)
  - [Clean code](#Clean-code)
  - [Optimizing](#Optimizing)
- [Assets](#Assets)
  - [Texture](#Texture)
  - [Audio](#Audio)
  - [Mesh](#Mesh)
- [Performance](#Performance)
  - [Object Pooling](#Object-Pooling)
  - [Bake Mesh](#Bake-Mesh)
  - [Recyclable Scroll](#Recyclable-Scroll)
- [Graphics and GPU](#Graphics-and-GPU)
  - [Camera](#Camera)
  - [Lighting](#Lighting)
  - [LOD](#LOD)
  - [Graphics](#Graphics)
  - [Shadow](#Shadow)
- [Tips and Tricks](#Tips-and-Tricks)

# Programing

# Assets
## Texture
### Texture import settings
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/80402648-6e7f-403a-8558-d6d3fb6a6534">
  <p><b>Texture import settings on Inspector</b></p>
</div>

- Most of your memory will likely go to textures, so the import settings here are **critical**. In general, try to follow these guidelines:
  - **Lower the Max Size**: Use the minimum settings that produce visually acceptable results. This is non-destructive and can quickly reduce your texture memory.
  - **Use powers of two (POT)**: Unity requires POT texture dimensions for mobile texture compression formats (PVRCT or ETC).
  - **Atlas your textures**: Placing multiple textures into a single texture can reduce draw calls and speed up rendering. Use the Unity Sprite Atlas or the third-party TexturePacker to atlas your textures.
  - **Toggle off the Read/Write Enabled option**: When enabled, this option creates a copy in both CPU- and GPU-addressable memory, doubling the texture’s memory footprint. In most cases, keep this disabled. If you are generating textures at runtime, enforce this via Texture2D.Apply, passing in makeNoLongerReadable set to true.
  - **Disable unnecessary Mip Maps**: Mip Maps are not needed for textures that remain at a consistent size on-screen, such as 2D sprites and UI graphics (leave Mip Maps enabled for 3D models that vary their distance from the camera).
  - **Compress Texture**: For almost every mobile device today, format sould be **RGBA Compressed ASTC**. For older devices, you can choose **ET2** or **ETC** compress.
 
<div align="center">
	<img src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/248f8c5c-fbce-42c8-8edb-090417c5873f">
  <p><b>Compress texture recommended</b></p>
</div>

### Texture compression format - Player Settings
<div align="center">
	<img src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/7b59e89d-0909-4c9e-987a-70e0784c5b2e">
  <p><b> Texture compression format in Player Settings</b></p>
</div>

- **Texture compression format**: **Player Settings** provide a global default for texture compression, affecting all textures unless **overridden**.
  - **ETC (Default)**: **ETC (Ericsson Texture Compression)** is a common format used on Android devices.
  - **ASTC**: **ASTC (Adaptive Scalable Texture Compression)** is a modern texture compression format suitable for a variety of platforms, including newer mobile devices.
## Audio
### Audio Import settings
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/6c6db88d-373b-4812-92d8-29431e9f2058">
  <p><b>Audio import settings in Inspector</b></p>
</div>

- Alway enable **Force to mono** to reduce the file size, use less memory and many other advantages.
- Reduce the size of your clips and memory usage with compression: 
  - Use **Vorbis** for most sounds (or MP3 for sounds not intended to loop).
  - Use **ADPCM** for short, frequently used sounds (e.g., footsteps, gunshots). This shrinks the files compared to uncompressed PCM, but is quick to decode during playback.
- Sound effects on mobile devices should be **22,050 Hz** at most. Using lower settings usually has minimal impact on the final quality; use your own ears to judge.
- The setting varies by clip size.
  - **Small clips (< 200 kb)** should **Decompress on Load**. This incurs CPU cost and memory by decompressing a sound into raw 16-bit PCM audio data, so it’s only desirable for short sounds.
  - **Medium clips (>= 200 kb)** should remain **Compressed in Memory**.
  - **Large files (background music)** should be set to **Streaming**, or else the entire asset will be loaded into memory at once.

## Mesh
### Mesh import settings
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/3a624b31-b5f6-4fe8-9c71-eebc8068eaf6">
  <p><b>Audio import settings in Inspector</b></p>
</div>

- Much like textures, **meshes** can consume excess memory if not imported carefully. To minimize meshes’ memory consumption:
  - **Compress the mesh**: Aggressive compression can reduce disk space (memory at runtime, however, is unaffected). Note that mesh quantization can result in inaccuracy, so experiment with compression levels to see what works for your models.
  - **Disable Read/Write**: Enabling this option duplicates the mesh in memory, which keeps one copy of the mesh in system memory and another in GPU memory. In most cases, you should disable it (in Unity 2019.2 and earlier, this option is checked by default).
  - **Disable rigs and BlendShapes**: If your mesh does not need skeletal or blendshape animation, disable these options wherever possible.
  - **Disable normals and tangents**: If you are absolutely certain the mesh’s material will not need normals or tangents, uncheck these options for extra savings.

# Performance
## Object Pooling
## Bake Mesh
## Recyclable Scroll

# Graphics and GPU
## Lighting
## LOD
## Graphics
## Shadow

# Tips and Tricks
## Use original uncompressed WAV files as your source assets when possible.
- If you use any compressed format (such as MP3 or Vorbis), Unity will decompress it, then recompress it during build time. This results in two lossy passes, degrading the final quality.
## Use the minimum Audio Source you can in your game.
- Each active audio source requires CPU resources for playback, which can strain the device's processing capabilities.
- Active audio sources consume memory, and mobile devices typically have limited RAM.
## Customize Linear vs Gamma color space.
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/2b7ae850-39e2-41b0-b4dc-2ef3b40f8940">
  <p><b>Linear vs Gamma color space</b></p>
</div>

- **Gamma** has better performance **(~10-30%)** than **Linear**. However, the display is a bit worse. 
- You can find this option in **Player Setting -> Other Settings**.
