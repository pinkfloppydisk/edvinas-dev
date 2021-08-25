---
summary: Building a VR simulation with Unity XR while following archaeological research to represent Neanderthal tool making legacy via Levallois knapping technique.
title: Neanderthal Tools
publishDate: 2021-08-13

draft: true
images:
- /posts/neanderthal-tools/simulation.jpg
---

{{<
  gallery
  "simulation.jpg" "Main location in the simulation with the guide character"
>}}

[Frederik Stief]: https://www.linkedin.com/in/frederikstief "Frederiks LinkedIn page"
[Rokas Sutkus]: https://sutkusaudio.com "Rokas personal website"
[Gneiss]: /posts/gneiss/ "Post about Gneiss"

[Neanderthal Tools]: https://github.com/Edvinas01/neanderthal-tools "Project GitHub repository"
[SceneBootstrapLoader.cs]: https://github.com/Edvinas01/neanderthal-tools/blob/master/Assets/Scripts/Scenes/Editor/SceneBootstrapLoader.cs "Script for opening multiple scenes at once"
[SceneLoader.cs]: https://github.com/Edvinas01/neanderthal-tools/blob/master/Assets/Scripts/Scenes/SceneLoader.cs "Script for loading scenes"

[COST Action (CA19141)]: https://www.cost.eu/cost-action/integrating-neandertal-legacy-from-past-to-present "COST action page"
[Medialogy]: https://www.en.aau.dk/education/master/medialogy "Medialogy programme at Aalborg University"

[Unity XR Interaction Toolkit]: https://docs.unity3d.com/Packages/com.unity.xr.interaction.toolkit@1.0 "Unity XR Interaction Toolkit 1.0 package documentation"
[OpenXR Plugin]: https://docs.unity3d.com/Packages/com.unity.xr.openxr@1.2/manual/index.html "UnityXR plugin documentation"
[Multi-Scene editing]: https://docs.unity3d.com/Manual/MultiSceneEditing.html "Unity Multi-Scene editing documentation"

### Intro
This year I attained my Master's degree in [Medialogy]. For the final project, me and my colleague [Frederik Stief] have decided to investigate learning aspects in VR without using text or speech. During the project, we collaborated with [COST Action (CA19141)] representatives and expert archeologists when designing the simulation. Here we attained information and feedback in regard to various processes on how Neanderthals made their tools and gathered materials for those tools.

This post will focus only on some technical aspects of the project. If you're just interested in the end result and source code, see [Neanderthal Tools] (we couldn't come up with a good name).

### Tooling
For the simulation, we utilized [Unity XR Interaction Toolkit]. So far this is the best solution I have used in Unity for interfacing and working with VR headsets. The components are well-designed and are extensible. The best part is that it works nicely with the new input system Unity provides, which makes the process of rebinding and adding new inputs a breeze.

However, during development we faced some challenges when deploying for different VR headsets, where we had to utilize the [OpenXR Plugin]. We had no issues with Oculus Rift S or Oculus Quest headsets. However, building for SteamVR (we also targeted HTC Vive) posed a lot of problems where the hands would be offset differently, input bindings would not work or weird overlay textures would appear. We resolved some of these problems by installing Beta versions of the Oculus and SteamVR runtimes. However, to fully fix them, we had to create two different builds which was not a pleasant experience. As it stands now, the plugin is not yet ready for production when targeting multiple vendors.

### Multi-scene setup
When working with VR in Unity, an essential feature is smoothly transitioning between scenes. Simply loading a scene via `SceneManager.LoadScene` after fading out the screen, introduces a noticeable stutter which in some cases can prompt the VR runtime to display a loading overlay which ruins the gameplay experience.

The usual approach for this is to utilise `SceneManager.LoadSceneAsync` with `LoadSceneMode.Additive` in addition to always keeping one scene open which acts as an intermediate scene for storing the player prefab. For this project we did just that. However, it was quite annoying having to start the game in a specific scene just to test the entire flow.

For this project we followed a similar approach to the one used in [Gneiss]. This time we utilised [Multi-Scene editing] feature which made the experience of editing two scenes at the same time a lot smoother. The only challenge was to make sure that each time after opening a `.scene` file, the appropriate scenes would load. To solve this we utilised `InitializeOnLoad` and `OnOpenAsset` attributes to hook into appropriate callbacks. You can see this in action in [SceneBootstrapLoader.cs].

We also wanted to ensure that after hitting play, the scenes would activate in the correct order. To do so, we always make sure that the first scene is active via `EditorSceneManager.RestoreSceneManagerSetup` as seen in [SceneBootstrapLoader.cs]. Then, we iterate all loaded scenes one by one and activate them. See `Start` method in [SceneLoader.cs] for an example.

The multi scene setup has been an essential part of my workflow after using it in [Gneiss]. For future projects I might create a small package to avoid having to type out the boilerplate for each project.

### Camera stacking

### Importance of audio

### Moving virtual hands

### Motion capture
