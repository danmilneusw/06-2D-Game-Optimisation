# 01 - Texture Compression
1. If you select a Texture2D from the Project window, you'll get many options on how to modify it:

<div align="center">
  <a href="Images\Texture2D Inspector.png" target="_blank">
    <img src="Images\Texture2D Inspector.png" style="height:600px;"/>
  </a>
</div>

Generate Mipmaps can be a useful tool, but this is primarily used in 3D projects. It is an optimisation technique that creates compressed versions of a texture. It is similar to LOD, even using a similar naming convention where the least compressed texture is called Mip 0, the next more compressed texture is called Mip 1 and so on. They do have some downsides though; like mesh LODs, every Mip of the texture is stored in memory, meaning this is something you don't want to do if you're developing for a device low on RAM. The performance benefit trade-offs are certainly less when compared to LODs and their meshes, which save on vertex/face count.

<div align="center">
  <a href="Images\Unity Mipmaps.png" target="_blank">
    <img src="Images\Unity Mipmaps.png" style="height:200px;"/>
  </a>
</div>
<div align="center">
  <a href="https://docs.unity3d.com/Manual/texture-mipmaps-introduction.html">
  source
  </a>
</div>

There's a lot here so I won't go into all of it. Of most importance is the settings at the end. Unity let's you set the max size and compression type.

Max size is just like setting the resolution of an image. The lower it is the less space the texture will use in memory, but the lower the quality of it. It's worth finding a happy medium between compression and quality. Don't just use the highest max size: You need about 1 gigabyte of memory to create a texture with a size of 16384, because the amount of data is 4 bytes per pixel × 16384 × 16384 = 1.07374182 gigabytes [(ref)](https://docs.unity3d.com/ScriptReference/SystemInfo-maxTextureSize.html#:~:text=Unity%20only%20supports%20textures%20up,if%20you%20have%20enough%20memory.).

Confusingly named is the Compression drop down. They mean different things depending on the platform you're exporting to. I recommend you learn more about them [here](https://docs.unity3d.com/Manual/class-TextureImporterOverride.html) as the option you choose will affect the size of the texture in memory as they used different formats. Here is a segment from the compression table:

| Platform | Color model | None | Normal quality (default) | High quality | Low quality (higher performance) |
| --- | --- | --- | --- | --- | --- |
| Windows, Linux, macOS | RGB | RGB 24 bit | RGB Compressed DXT1 | RGB(A) Compressed BC7 | RGB Compressed DXT1 |

It's also worth looking into understanding the format. More info can be found at the [same link](https://docs.unity3d.com/Manual/class-TextureImporterOverride.html).

<div align="center">
  <a href="Images\Texture Format.png" target="_blank">
    <img src="Images\Texture Format.png" style="height:300px;"/>
  </a>
</div>
<br>

If we select all the textures from the Environment and UI folder and set their Max size to 16384 (the highest value) and set Compression to None. You could assess performance using the Profile Analyzer, but for such a simple 2D I found very low benefits when altering texture compression. But what we can do is assess the impact on memory using the Memory Profiler. 

<div align="center">
  <a href="Images\Selecting Textures.png" target="_blank">
    <img src="Images\Selecting Textures.png" style="height:600px;"/>
  </a>
</div>
<br>

2. Go to the Package Manager (Window > Package Manager) and install the Memory Profiler. Make sure you are searching in the Unity Registry.

<div align="center">
  <a href="Images\Memory Profiler Install.png" target="_blank">
    <img src="Images\Memory Profiler Install.png" style="height:300px;"/>
  </a>
</div>
<br>

3. Open the Memory Profiler at Windows > Analysis > Memory Profiler. When using the Memory Profiler, you will get wildly different results when using the Memory Profiler in the Editor compared to outside of it. So we need to Memory Profile a build. In my example, a build used less than 10% of the memory of an in-Editor measurement:

<div align="center">
  <a href="Images\Memory Profiler Build vs Editor.png" target="_blank">
    <img src="Images\Memory Profiler Build vs Editor.png" style="height:300px;"/>
  </a>
</div>
<br>

4. Go to File > Build Settings. Make sure you have Autoconnect Profiler selected to allow you to use the Memory Profiler on the build. 

<div align="center">
  <a href="Images\Build Settings.png" target="_blank">
    <img src="Images\Build Settings.png" style="height:500px;"/>
  </a>
</div>
<br>

5. You should see a message saying something like 'Profiler connected...' if you have set the build settings correctly (see image below). When the game is running, select Capture New Snapshot. Close the build and set the textures you set to Max size to the lowest size and run another build. Your game will look terrible, but this is just an example.

<div align="center">
  <a href="Images\Profiler Connected to Device.png" target="_blank">
    <img src="Images\Profiler Connected to Device.png" style="height:500px;"/>
  </a>
</div>
<br>

6. Select Capture New Snapshot on this build and quit the game. You should now have two Session captures. You can already see that the second capture uses less memory overall.

<div align="center">
  <a href="Images\Memory Profiler Captures.png" target="_blank">
    <img src="Images\Memory Profiler Captures.png" style="height:500px;"/>
  </a>
</div>
<br>

7. If you select the first snapshot and inspect further, you will see the total allocated memory to Texture2Ds is 16.6 MB.

<div align="center">
  <a href="Images\Memory Profiler Uncompressed Textures.png" target="_blank">
    <img src="Images\Memory Profiler Uncompressed Textures.png" style="height:500px;"/>
  </a>
</div>
<br>

8. The second snapshot will show that with the compressed textures, the total allocated memory to Texture2Ds is now only 5 MB or about a 70% reduction ( ((16.6-5)/16.6) * 100 = 69.8% ).

<div align="center">
  <a href="Images\Memory Profiler Compressed Textures.png" target="_blank">
    <img src="Images\Memory Profiler Compressed Textures.png" style="height:500px;"/>
  </a>
</div>
<br>

9. If you select Compare Snapshots and select both snapshots, you can have the Texture2D difference calculated for you (see under Top Unity Objects Categories).

<div align="center">
  <a href="Images\Memory Profiler Compare Snapshots.png" target="_blank">
    <img src="Images\Memory Profiler Compare Snapshots.png" style="height:500px;"/>
  </a>
</div>