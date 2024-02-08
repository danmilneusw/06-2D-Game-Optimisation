# 06-2D-Game-Optimisation
<div align="center">
  <a href="Images\Working Culling.gif" target="_blank">
    <img src="Images\Working Culling.gif" style="height:500px;"/>
  </a>
</div>

## Summary
- Apply texture compression and monitor its impact using the Memory Profiler and the Profile Analyzer.
- Apply culling into a 2D game.
- Get additional tips on using the Memory Profiler and Profile Analyzer.

## Tutorial
1. Open **Unity Project - 2D**.
2. Firstly, a quick sidenote: One thing you might notice is that if you fire your projectile outside of the map, it will never hit anything and never be destroyed. Look below, the hierarchy is full of projectiles. Like in [last week's tutorial](https://github.com/danmilneusw/05-Garbage-Collection/), these projectiles could certainly be put in an object pool; when they haven't hit anything after a certain amount of time, they should be deactivated using [SetActive](https://docs.unity3d.com/ScriptReference/GameObject.SetActive.html) = False and reactivated using SetActive = True when the player fires a projectile again.

<div align="center">
  <a href="Images\Projectiles.png" target="_blank">
    <img src="Images\Projectiles.png" style="height:600px;"/>
  </a>
</div>

3. Complete **01 - Texture Compression.md**.
4. Complete **02 - 2D Culling.md**.

## Extra Resources
- [Unity Blog - A lightning round of great tips for 2D games](https://blog.unity.com/games/a-lightning-round-of-great-tips-for-2d-games)
- [Optimize performance of 2D games with Unity Tilemap](https://unity.com/how-to/optimize-performance-2d-games-unity-tilemap)
- [Unity Docs - Mipmaps Introduction](https://docs.unity3d.com/Manual/texture-mipmaps-introduction.html)
- [Unity Blog - Accessing Texture Data Efficiently](https://blog.unity.com/engine-platform/accessing-texture-data-efficiently)
- [Unity Docs - Sprite Packer (Sprite Atlasing)](https://docs.unity3d.com/540/Documentation/Manual/SpritePacker.html) ⭐