---
description: In this post, I go into some details of Unity project setup with VR, library choices and few other technical details.
title: Sandstone - part 2
date: 2020-08-17

images:
  - /blog/sandstone-2/screenshot.jpg
---

{{<
  gallery
  "screenshot.jpg" "Screenshot of the game"
>}}

### Intro
TODO: finish
[part 1](/blog/sandstone-1)

### Universal Render Pipeline
When starting this project I set out to experiment with Universal Render Pipeline (URP). This was a great decision as I was able to experiment with shader graphs. Usually I avoid making shaders, however working with shader graphs was a bliss.

The biggest downside to using current version of URP is inability to use real-time shadows with point lights. I didn't pay enough attention to documentation and skipped this step. Due to this I had to deal with _leaking_ point lights through walls and found no way around it.

### VR library
Firstly I played around with few VR libraries to get a grip of what is available. I'm sure there are more out there, however I wanted to get this up and running quickly. Thus, here my findings.

#### VRTK
Initially I started out with VRTK. This library has quite a few pre-built assets which cover common interactions such as picking up objects, pushing buttons etc. Setting up simple interactions with this library is a quick task, however when trying to set up something more complex it becomes a pain.

The whole toolkit is a huge mess of nested prefabs, debugging issues is a pain as you have to go down through levels of nested components. Documentation is also out of date and misleading as the current version (VRTK v4) is still in beta and missing most of the features. The older version (VRTK v3) is completely unsupported and has various issues when setting up with current versions of Unity. Due to these issues I've decided to move on and look for other libraries.

#### Unity XR
Unity provides its own VR integration. I've only played around with example scenes and didn't go too much in-depth. One thing I noticed that the hand tracking was lagging behind a bit in the sample scenes. I was hoping that it would include more prefabs for various hand interactions same as VRTK does, however this part was lacking.

In addition to that, in order to work with Unity XR I had to use Steam VR on top of Oculus App. This was a bit annyoing when testing out the sample scenes, and I didn't want to deal with this throughout the whole project.

Recently there have been quite a few updates to this. I'm guessing in the future Unity XR will improve and will be the go to set up for when working VR in Unity, but for now I skipped it.

#### Oculus Integration
Since I had to install Oculus Integration for when working with VRTK and Unity XR, I've decided to stick with it. This asset comes with quite a few sample scenes of its own where hand interactions and locomotion are already built-in.

The biggest downside of this asset is that the sample scripts are utter garbage and are barely working. I made a mistake of relying on them early on and trying to build upon them until it was too late to switch. Next time if I pick up Oculus Integration, for sure I'll be re-implementing every interaction.

Later on in the development process I wanted experiment a bit with custom hand interactions. After reading [An improved way of grabbing objects in VR with Unity3D](https://www.gamasutra.com/blogs/DanielEstrella/20190205/335847/An_improved_way_of_grabbing_objects_in_VR_with_Unity3D.php), I decided to go with FixedJoint route. This resolved the issue of being able to move objects through walls. The scripts for that can be seen [here](https://github.com/Edvinas01/sandstone/tree/master/Assets/Scripts/Grab) and usage can be seen in the [PrototypeScene](https://github.com/Edvinas01/sandstone/blob/master/Assets/Scenes/PrototypeScene.unity).

One more thing that caused some issues was the usage of URP. In some cases shaders and particle effects would only render for one eye. I was able to work around this by using Single Pass rendering.

### Testing scenes
{{<
  gallery
  "mini_island.jpg" "Mini island scene"
>}}

For this project I was building a large world. Initially I would test everything in the main scene which was massive. To reach certain areas and test features it would take minutes. To solve this issue I brought all the interactions right next to the spawn point in order to speed up the process, however as the project continued I quickly noticed that I would break things in the main scene and would not notice that.

Later on I made the [MiniIslandScene](https://github.com/Edvinas01/sandstone/blob/master/Assets/Scenes/MiniIslandScene.unity) for testing all the games features. It is essentially the same main scene, except greatly scaled down. During development process I would constantly break various features in this scene without noticing, however I always could fall-back to the main scene and revert if such an event occurred.

When testing out smaller features, I started to set up even smaller scenes which would not require me to use a VR headset at all. For example the [PrototypeScene](https://github.com/Edvinas01/sandstone/blob/master/Assets/Scenes/PrototypeScene.unity). This also sped up the development process quite a lot as it is quite time-consuming and annoying having to put on the head-set each time you make a tiny change.

### Organizing
{{<
  gallery
  "hierarchy_objects.jpg" "Hierarchy and inspector view"
>}}

For any type of project I find that organizing code (naming, style) and assets (folder naming and structure) is the most important part. For Unity, I find this to be even more true as a lot of work has to be done through the UI.

#### Prefabs
For this project the most important aspect when working with Unity is using were Prefabs and Prefab Variants. For example for wooden intractable objects I defined a generic [Wood_Grabbable](https://github.com/Edvinas01/sandstone/blob/master/Assets/Prefabs/Interractables/Wood_Grabbable.prefab). In this generic prefab I added components which enable grabbing, impact sounds, fire interactions and such. Then this would be used as a Prefab Variant to implement more concrete objects, for example a [Log_01_Grabbable](https://github.com/Edvinas01/sandstone/blob/master/Assets/Prefabs/Interractables/Log_01_Grabbable.prefab).

#### Scenes
When working in scenes, I would create empty game objects centered at world origin, which would then be used as folders for other appropriate objects. For example all grass would go under a game object named Grass and so on. This approach allows to quickly disable and enable groups of objects when testing, for example when testing hand interactions, I can disable everything apart from the ground and intractable objects.

#### Naming
When working with code I would simply follow the naming suggestions made by Rider IDE. I think the more interesting part is naming assets. For this I adopted some suggestions made in (UnityStyleGuide)[https://github.com/stillwwater/UnityStyleGuide]. In the future though, I would like to create a more explicit set of rules for asset naming, as at the moment it is quite loose.

### Closing notes
After looking at few VR libraries I was quite disappointed with their quality. Yet, keeping in mind the ability to prototype I think that can be looked over. For more serious projects, these tools should only be used as a MVP and later on re-worked.

In terms of development, I found that spending some time organizing the project gives great benefits, which is even more important when working with Unity as it is too easy to break things without noticing.

In next part, I'll dive in technical issues that gave me the most head-aches.
