# unity-mobile-optimization
👋 **Hi there, I'm HoangVanThu**. This repository discusses various ways to optimize your games on mobile devices, helping enhance overall performance. These techniques aim to improve the gaming experience by optimizing resource usage and performance on mobile platforms.

The article has been synthesized from various sources on the internet. If you find any inaccuracies or have any questions about the content, please submit an issue or provide direct feedback to me.

**Table of optimization contents:**
- [Programing](#Programing)
  - [Object Pooling](#Object-Pooling)
  - [Recyclable Scroll](#Recyclable-Scroll)
- [Assets Import](#Assets-Import)
  - [Texture](#Texture)
  - [Audio](#Audio)
  - [Mesh](#Mesh)
- [Tips and Tricks](#Tips-and-Tricks)

# Programing
## Object Pooling
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/691394db-f947-43d9-bd13-f65549495f8d">
  <p><b>Example of Object Pooling</b></p>
</div>

- To prevent **Garbage Collector** issues (CPU Spikes) in games with many spawning and destroying objects, a method called **Object Pooling** can be used. **Object Pooling** refers to creating all necessary objects beforehand and disabling/enabling them when it necessary, instead of instantiating (Instantiate() function) and destroying (Destroy() function) objects during runtime. 
- These objects can also be spawned beforehand during a loading screen and kept hidden until needed. This way they won’t cause performance issues when spawned during gameplay.

## Recyclable Scroll

<div align="center">
	<img width="500" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/c925547e-8780-45a9-b427-aeaec7a78cba">
	<img width="500" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/a7d31475-161b-4008-9713-baf97b10d567">
  <p><b>Rycyclable scrollview</b></p>
</div>

- By creating a **scrollview with 9999 items** it causes a significant performance degradation for the game. So **rycyclable scrollview** is used to significantly increase the game's performance. Instead of creating 9999 items in a scrollview, **recyclable scrollview** creates a certain number of items that need to be displayed on the screen and reuses them.

# Assets Import
## Texture
<div align="center">
	<img width="400" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/80402648-6e7f-403a-8558-d6d3fb6a6534">
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
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/3a624b31-b5f6-4fe8-9c71-eebc8068eaf6">
  <p><b>Audio import settings in Inspector</b></p>
</div>

- Much like textures, **meshes** can consume excess memory if not imported carefully. To minimize meshes’ memory consumption:
  - **Compress the mesh**: Aggressive compression can reduce disk space (memory at runtime, however, is unaffected). Note that mesh quantization can result in inaccuracy, so experiment with compression levels to see what works for your models.
  - **Disable Read/Write**: Enabling this option duplicates the mesh in memory, which keeps one copy of the mesh in system memory and another in GPU memory. In most cases, you should disable it (in Unity 2019.2 and earlier, this option is checked by default).
  - **Disable rigs and BlendShapes**: If your mesh does not need skeletal or blendshape animation, disable these options wherever possible.
  - **Disable normals and tangents**: If you are absolutely certain the mesh’s material will not need normals or tangents, uncheck these options for extra savings.

## Animation
<div align="center">
	<img width="800" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/b1850f34-c86a-41a9-99a6-a6a3be61a609">
  <p><b>Animation import settings in Inspector</b></p>
</div>

- By clicking on a model containing animation in the project, you can open the **animation import settings**.
- You can change properties such as **rotation error, position error, and scale error** to reduce file size. However, keep in mind that these parameters introduce errors to the animation, so you should be cautious when adjusting them. You can refer to the [documentation](https://www.techarthub.com/animation-compression-unity/) for more details on compression and animation

# Tips and Tricks
## Use original uncompressed WAV files as your source assets when possible.
- If you use any compressed format (such as **MP3** or **Vorbis**), Unity will decompress it, then recompress it during build time. This results in two lossy passes, degrading the final quality.
## Use the minimum Audio Source you can in your game.
- Each **active audio source** requires CPU resources for playback, which can strain the device's processing capabilities.
- **Active audio sources** consume memory, and mobile devices typically have limited RAM.
## Customize Linear vs Gamma color space.
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/2b7ae850-39e2-41b0-b4dc-2ef3b40f8940">
  <p><b>Linear vs Gamma color space</b></p>
</div>

- **Gamma** has better performance **(~10-30%)** than **Linear**. However, the display is a bit worse. 
- You can find this option in **Player Setting -> Other Settings**.
## Try using incremental GC (Garbage collection)
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/fa54bd08-fb26-4890-addd-07e382f7cb9a">
  <p><b>Use incremental GC settings</b></p>
</div>

- Sometimes enable **use incremental GC** option can improve game's performance and sometimes it's not, you should see profiler for the impact. You can find it in **Player Setting**.

## Using LOD technical

<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/1c980b4f-8977-4c80-b3f4-d8256680c45e">
  <p><b>Level of details example</b></p>
</div>

- **Level of detail (LOD)** is a technique that reduces the number of GPU operations that Unity requires to render distant meshes.
- When a **GameObject** in the Scene is far away from the Camera, you see less detail compared to when the GameObject is close to the **Camera**. However, by default, Unity uses the same number of triangles to render it at both distances. This can result in wasted GPU operations, which can impact performance in your Scene.

## Bake Mesh

<div align="center">
  <video src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/74429c89-0723-4994-a9eb-1c64a83aeaf5" width="400" />
</div>

<div align="center">
	<p><b>Youtube short for example of baking mesh</b></p>
</div>

- **Bake mesh** is the process of combining meshes to reduce draw calls, which also means increasing game performance. Unity does not provide mesh baking, however there are quite a few assets on the store that provide mesh baking, such as **Mesh Baker** or **ProBuilder**.


## Fake Shadow
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/bbc9702d-6bef-49d2-b0a2-bb9ccb3cf1bf">
  <p><b>Lighting properties in Mesh Renderer</b></p>
</div>


- The computation of shadows in Unity incurs a significant performance cost, especially on mobile devices. It is advisable to **disable shadow** casting and receiving if not absolutely necessary, substituting them with **fake shadows** as an alternative to improve performance.
- You can also create **fake shadows** using a **blurred texture** applied to a simple mesh or quad underneath your characters. Otherwise, you can create blob shadows with **custom shaders**.

## Disable Vsync

<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/87111f15-8f13-4509-9957-3b736c755745">
  <p><b>Linear vs Gamma color space</b></p>
</div>

- **Enable Vsync** in mobile game might cause **lag**,  **faster battery drain** and also **limit the frame rate** to the monitor's refresh rate.

## Choose Suitable Graphics API
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/9f16dd3b-3924-4f7e-b4c3-a1560529be76">
  <p><b>Graphics API</b></p>
</div>

- In **Player Setting**. You can check the list of graphics APIs supported by the current version of Unity by unchecking **Auto Graphics API** and clicking on the plus sign to view all API.
- For **Android**, there are 3 API graphics: **GLES2, GLES3 and Vulkan** base on Unity version.
  - **GLES3** is the best choise for popular mobile devices that you shouldn't remove.
  - If you want to target on old devices, you should have **GLES2**. 
  - **Vulkan** is good option for modern devices but sometime it's causing crash and lag for lower devices.
- My recommend is using **GLES2, GLES3** for optimizing.

# Support
- If you like this topic, you can give this repository a star ⭐
- I would greatly appreciate it if you could support me with a cup of coffee
<a href="https://www.buymeacoffee.com/HoangVanThu">
  <img src="https://www.the3rdsequence.com/texturedb/images/donate/buymeacoffee.svg" width="200" height="47"/>
</a>
