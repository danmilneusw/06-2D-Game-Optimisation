# 02 - 2D Culling
This section will guide you on how to make a custom 2D Camera Occluder.

1. When looking at the Scene view in the image below, you can see that there's a lot being rendered even though it doesn't need to be. I recommend you set your Layout to something like 2 by 3 in the dropdown at the top right of the Unity window, this way you can view the scene and game view easily.

<div align="center">
  <a href="Images\Projectiles.png" target="_blank">
    <img src="Images\Projectiles.png" style="height:600px;"/>
  </a>
</div>
<br>

2. If we check the amount of draw calls using the Frame Debugger, we see that we have 47 draw calls. Even if we disabled everything outside of the camera's view, the number of draw calls would still be 47. This is because draw calls only consider what is actually being displayed to the user. They are the information being sent from the CPU to the GPU telling it what to render to the user after all.

<div align="center">
  <a href="Images\Frame Debugger.png" target="_blank">
    <img src="Images\Frame Debugger.png" style="height:600px;"/>
  </a>
</div>
<br>

By the way, here you might notice that there is the potential for GPU Instancing:
<div align="center">
  <a href="Images\GPU Instancing Not Enabled.gif" target="_blank">
    <img src="Images\GPU Instancing Not Enabled.gif" style="height:400px;"/>
  </a>
</div>

3. So what would be the point of implementing culling if draw calls is the same? One example is there may be some computationally expensive logic happening outside of the camera view. We can disable the objects that do this logic while they're not being viewed, saving on computation.
<br><br>
For your own project analysis, I highly recommend you compare using the Profile Analyzer. Below I have recorded using the Unity Profiler. But I can open the Profile Analyzer, select Pull Data to move the Profiler data to the Profile Analyzer, and then can view the Mean time per frame and other statistics over a set number of frames. This can give a more realistic representation of the time per frame.

<div align="center">
  <a href="Images\Profiler 16384.png" target="_blank">
    <img src="Images\Profiler 16384.png" style="height:400px;"/>
  </a>
</div>

<div align="center">
  <a href="Images\Profile Analyzer.png" target="_blank">
    <img src="Images\Profile Analyzer.png" style="height:600px;"/>
  </a>
</div>
<br>

I could then make another Profile Analyzer recording and pull that data in too to make a comparison (we covered this in [week 1](https://github.com/danmilneusw/01-Measuring-Game-Engine-Performance/blob/main/01%20-%20The%20Unity%20Profiler%20Introduction.md)).

4. We need a script that allows us to detect if something is in view and make it active, and if it is not then make it inactive. We can create a list of game objects that we want this condition applied to. Create a script called ObjectVisibilty and copy and paste in the below code:
```
using UnityEngine;
using System.Collections.Generic;

public class ObjectVisibility : MonoBehaviour
{
    public List<GameObject> objectsToControl;

    void Update()
    {
        foreach (GameObject obj in objectsToControl)
        {
            Vector3 screenPoint = Camera.main.WorldToViewportPoint(obj.transform.position);
            bool onScreen = screenPoint.z > 0 && screenPoint.x > 0 && screenPoint.x < 1 && screenPoint.y > 0 && screenPoint.y < 1;

            // Set the object active state
            obj.SetActive(onScreen);
        }
    }
}
```

4. Add this script to the main camera component. Drag in the three house objects to the list and test if they appear and reappear. The culling appears to happen a little too early. One way we could fix this is with a buffer. Rather than check if the object is outside of the view, check if it is outside of the view plus a little bit more. This way we can increase the likelihood of objects not jumping-in. I won't give you the code for this. Try implement it yourself.

<div align="center">
  <a href="Images\Clipping.gif" target="_blank">
    <img src="Images\Clipping.gif" style="height:600px;"/>
  </a>
</div>
<br>

5. After implementing a buffer, you can drag in as many objects as you want into the list. Tip: To do this easily, select the lock icon in the inspector afer selecting the main camera. This will keep you on the main camera inspector view. You can then select multiple objects from the hierarchy and drag them into the list.

6. This script is not well optimised. For example, is there code here that has to run every frame?

Is there a way to create an array of all the positions the player could be in and whether or not each object should be active or inactive before running the game?

In the current method you also need to be careful with what you include in this list. I found that the middle house would disappear when the player walked behind it because the bottom of the house was outside of the camera's view, causing it to be set as inactive.

Also, the game code is setup to destroy the pick-up object when the player collides with it, so we no longer have a reference to our game object in the ObjectVisibility list, which can cause warnings:

<div align="center">
  <a href="Images\GameObject Destroyed.png" target="_blank">
    <img src="Images\GameObject Destroyed.png" style="height:600px;"/>
  </a>
</div>
<br>

It may actually be better to put use a list objects that state what we want to exclude from culling, as opposed to what we want included in culling.

6. Despite all these thoughts, even on this small scene we get improved performance, especially when the camera is positioned away from the enemies. Taking a screenshot actually made the Editor take a performance hit, but my actual results are more like this:

- Without Culling Script: 3.33 ms per frame (300FPS)
- With Culling Script: 2.7 ms per frame (370FPS)

Without culling:
<div align="center">
  <a href="Images\Without Culling.png" target="_blank">
    <img src="Images\Without Culling.png" style="height:600px;"/>
  </a>
</div>

With culling:
<div align="center">
  <a href="Images\Working Culling.png" target="_blank">
    <img src="Images\Working Culling.png" style="height:600px;"/>
  </a>
</div>

<div align="center">
  <a href="https://github.com/danmilneusw/06-2D-Game-Optimisation/raw/main/Images/Working%20Culling.gif" target="_blank">
    <img src="https://github.com/danmilneusw/06-2D-Game-Optimisation/raw/main/Images/Working%20Culling.gif" style="height:600px;"/>
  </a>
</div>
