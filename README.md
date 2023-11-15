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
- [Tricks](#Tricks)

## Programing

## Assets
### Texture
#### Texture import settings
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


### Audio
### Mesh

## Performance
### Object Pooling
### Bake Mesh
### Recyclable Scroll

## Graphics and GPU
### Lighting
### LOD
### Graphics
### Shadow

## Tricks
