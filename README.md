# unity-mobile-optimization

![banner](https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/f895bf67-666e-4415-b276-874341ee8a2f)

👋 **Hi there, I'm HoangVanThu**. This repository discusses various ways to optimize your games on mobile devices, helping enhance overall performance. These techniques aim to improve the gaming experience by optimizing resource usage and performance on mobile platforms.

The article has been synthesized from various sources on the internet. If you find any inaccuracies or have any questions about the content, please submit an issue or provide direct feedback to me.

---

# Table of Contents
- [Reduce Build Size](#reduce-build-size)
  - [Texture](#texture)
  - [Audio](#audio)
  - [Mesh](#mesh)
  - [Animation](#animation)

- [Optimize Performance](#optimize-performance)
  - [Scripting](#scripting)
    - [Clean Code](#clean-code)
    - [Avoid Allocating Memory](#avoid-allocating-memory)
    - [Use Algorithm](#use-algorithm)
  - [Technical](#technical)
    - [Object Pooling](#object-pooling)
    - [Recyclable Scroll](#recyclable-scroll)
  - [Tips and Tricks](#tips-and-tricks)
    - [Staying with Unity LTS](#staying-with-unity-lts)
    - [Use original uncompressed WAV files](#use-original-uncompressed-wav-files-as-your-source-assets-when-possible)
    - [Use the minimum Audio Source you can](#use-the-minimum-audio-source-you-can-in-your-game)
    - [Customize Linear vs Gamma color space](#customize-linear-vs-gamma-color-space)
    - [Incremental GC](#try-using-incremental-gc-garbage-collection)
    - [Using LOD technical](#using-lod-technical)
    - [Bake Mesh](#bake-mesh)
    - [Fake Shadow](#fake-shadow)
    - [Pack texture](#pack-texture)
    - [Enable GPU Instancing](#enable-gpu-instancing)
    - [Disable Vsync](#disable-vsync)
    - [Choose Suitable Graphics API](#choose-suitable-graphics-api)

- [Others](#others)
- [Support](#support)

---

# Reduce Build Size

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
  - **Compress Texture**: For almost every mobile device today, format should be **RGBA Compressed ASTC**. For older devices, you can choose **ET2** or **ETC** compress.
 
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
  - **Small clips (< 200 kb)** should **Decompress on Load**.
  - **Medium clips (>= 200 kb)** should remain **Compressed in Memory**.
  - **Large files (background music)** should be set to **Streaming**.

## Mesh
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/3a624b31-b5f6-4fe8-9c71-eebc8068eaf6">
  <p><b>Audio import settings in Inspector</b></p>
</div>

- Much like textures, **meshes** can consume excess memory if not imported carefully. To minimize meshes’ memory consumption:
  - **Compress the mesh**
  - **Disable Read/Write**
  - **Disable rigs and BlendShapes**
  - **Disable normals and tangents**

## Animation
<div align="center">
	<img width="800" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/b1850f34-c86a-41a9-99a6-a6a3be61a609">
  <p><b>Animation import settings in Inspector</b></p>
</div>

- You can change properties such as **rotation error, position error, and scale error** to reduce file size.
- Be cautious because these parameters introduce animation errors.

---

# Optimize Performance

# Scripting

## Clean Code
- Your game may have many code redundancy, so clean code could increase the performance for your game.
- Here I found a good [clean code topic](https://github.com/thangchung/clean-code-dotnet).

## Avoid Allocating Memory
Every time an object is created, memory is allocated.

### Situation 1: In Unity, the term "string" refers to a data type, not a variable name.
- String concatenation creates garbage.

```diff
- Debug.Log("hello" + " " + "world");
```
- This code above will create 3 difference string and located in the heap memory. So this should be:

```diff
+ Debug.Log("hello world");
```

### Situation 2: Use classes and struct wisely.
- Structs = stack, Classes = heap  
- Structs are faster for small data  
- Classes are better for complex objects  

### Situation 3: Use C# Events instead of UnityEvents.
- UnityEvent is slower and generates garbage.

## Use Algorithm
- The algorithm will significantly improve the performance of the game, apply it whenever possible.

---

# Technical

## Object Pooling
<div align="center">
	<img width="600" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/691394db-f947-43d9-bd13-f65549495f8d">
  <p><b>Example of Object Pooling</b></p>
</div>

- Prevent GC spikes by pooling objects instead of instantiating/destroying.

## Recyclable Scroll
<div align="center">
	<img width="500" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/c925547e-8780-45a9-b427-aeaec7a78cba">
	<img width="500" src="https://github.com/GuardianOfGods/unity-mobile-optimization/assets/52252046/a7d31475-161b-4008-9713-baf97b10d567">
  <p><b>Recyclable scrollview</b></p>
</div>

- Avoid creating 9999 UI items, reuse them instead.

---

# Tips and Tricks

## Staying with Unity LTS
- Avoid Tech Stream for stability.

## Use original uncompressed WAV files as your source assets when possible.
- Avoid double compression quality loss.

## Use the minimum Audio Source you can in your game.
- Every active Audio Source consumes CPU/RAM.

## Customize Linear vs Gamma color space.
- Gamma = **10–30% faster**.

## Try using incremental GC (Garbage collection)
- Smoother GC in some cases.

## Using LOD technical
- Reduce triangles for distant meshes.

## Bake Mesh
- Reduce draw calls by combining meshes.

## Fake Shadow
- Disable real shadows → huge performance gain.

## Pack texture
- Use Sprite Atlas for batching.

## Enable GPU Instancing
- Reduce draw calls for repeated meshes.

## Disable Vsync
- Avoid FPS lock + battery drain.

## Choose Suitable Graphics API
- Use **GLES2 + GLES3** for compatibility.

---

# Others
- Topics:
  - [Unity Interview Questions](https://github.com/GuardianOfGods/unity-interview-questions)
  - [Unity Mobile Developer](https://github.com/GuardianOfGods/unity-mobile-developer)
- Also, I have created a simple game system for mobile platform:
  - [Unity Mobile Gamebase](https://github.com/Laputa-Unity/unity-mobile-gamebase)

---

# Support
- If you like this topic, you can give this repository a star ⭐
- I would greatly appreciate it if you could support me with a cup of coffee

<a href="https://www.buymeacoffee.com/HoangVanThu">
  <img src="https://www.the3rdsequence.com/texturedb/images/donate/buymeacoffee.svg" width="200" height="47"/>
</a>
