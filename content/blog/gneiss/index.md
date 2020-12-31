---
description: TODO
title: "Gneiss: A Misshapen Journey"
date: 2020-12-31
draft: true

images:
  - /blog/gneiss/game-logo.jpg
---

{{<
  gallery
  "game-logo.jpg" "Game logo"
>}}

### Intro
This year I attended a [DADIU](http://www.dadiu.dk) programme as a lead programmer where we built two small games over the course of two weeks one big game - Gneiss: A Misshapen Journey with a team of 23 students. In this post I'll talk about process and technical challenges that we faced when building this game using the Unity game engine.

This time around I cannot share the source code, but uou can watch the trailer on [YouTube](https://www.youtube.com/watch?v=P-agk3Bt0Dk) and download the finished game from [Itch.io](https://dadiu.itch.io/gneiss).

If you're interested in the smaller games, you can download [mini-game-1](https://blackmadhatter.itch.io/mini-game-1) and [mini-game-2](https://blackmadhatter.itch.io/mini-game-2) as well.

### Production process
Working in a team of 23 people is an extremely difficult task. Everyone has a lot of ideas and different skill sets. Coming to this programme I expected that technical issues would be the most challenging part, however communication is key. From a technical perspective building this game was not that hard, however making sure that assets get in, get properly integrated, tested and convey the idea that we're going for is the hard part.

#### Pipeline
Working on this project did not differ _that much_ from regular software development processes apart from the asset pipeline. Rather than just focusing on making sure the code works there was an extra step, we had to make sure that other teams are able to properly quickly get their assets into the game. Due to this we had to develop custom tools (more on that later) and help out other disciplines with various Unity issues. As a lead programmer, this was pretty much my job the entire time rather than working on features. Personally I enjoyed a lot as after developing a tool or teaching someone on the team on how to use some specific Unity functionality to integrate their assets provided a tight feedback loop.

#### Structure
For our first two games we tried splitting up into feature teams where each team would focus on a specific feature, e.g. enemies, main character, etc. Each team would have a certain number of programmers, designers and CG artists. Using this approach the biggest issue was integrating everything together and keeping an overview as this approach required a lot of communication for which we were not ready for. In addition to that, we encountered a lot of bottlenecks from the programming side as other disciplines were not comfortable/experienced in using Unity which meant that most asset integration relied on the programmers.

For the final game we dropped feature teams and followed a Scrum model. Each day, we would have a daily meeting in the morning with the whole team where we'd discuss outstanding issues. Then we would split it into disciplines where we had our stand-up meetings. We would work like this on a weekly basis, where at the end of the week we would publish a release build. Afterwards, we would do a gameplay demo with the whole team and then discuss the goals for the next sprint. Afterwards we'd split up into disciplines and do a retrospective meeting followed by a sprint planning. This model worked quite well as it was a lot easier to keep an overview of the whole project.

Following this model we also managed to resolve the previously mentioned bottleneck on the programmer side. I think this was partially due to the fact that people got more familiar with Unity and Git, but also that we outlined deliverables that discipline must add to the gem by themselves on each task during the sprint planning sessions.

#### Platforms
Initially we used Codecks for managing tasks in our first mini game. It became apparent that in such a large team this platform was not the right fit. It was difficult to split up tasks between disciplines and keep an overview of what is going on. For the next games we switched to Trello and stuck with that.

For communication, we used Discord. Given how COVID-19 was spiraling out of control during this programme, I think this was the perfect platform. We prepared a bunch of text and voice channels and aggressively chatted over them. To share documents, etc, we used Google Drive which worked quite well.

### Tools
When setting up tooling to manage the source code and project

#### Automation
{{<
  gallery
  "teamcity.png" "TeamCity dashboard"
>}}

Initially we spun up GitLab CI by following [Automatically building and deploying Unity applications to itch.io](https://johanhelsing.github.io/2020/05/17/gitlab-docker-itch-deploy.html) post by Johan Helsing. This setup was quite handy and quick to setup, but as our projects grew it became way too slow as we had to wait 20+ minutes for a single build to complete. In addition to that, GitLab updated their CI limits to only allow for 400 minutes per group which we already exceeded.

Afterwards we setup a cheap Linux TeamCity server on DigitalOcean. Setting this up was a lot more difficult as Unity didn't like the fact that it was building on Linux inside Docker containers. We also had a bunch of issues bundling Wwise assets as well due to missing `.dll` errors. For future projects I would use a Windows server or build a dedicated machine just for this.

One think that worked really well with automated builds were Discord messages. After each successful or failed build a message would be posted to specific Discord channels. This brought attention to failing builds from other disciplines and not only QA and programmers. In addition to that, waiting for our bots named _Pinky_ and _Brain_ to post a message on successful development and release builds was quite charming.

#### Git
For managing source code we used GitLab with a bunch of different Git clients. We chose GitLab as it allowed for more space per project - 10GB. For the Git clients, we used GitHub Desktop, Sourcetree and Git Bash. Initially I recommended GitHub Desktop for the designer, audio and CG teams, but as we progressed with the production, we encountered a lot of problems with it. I wouldn't recommend using GitHub Desktop on large projects.

We used a _simplified_ Git flow model where we had `master`, `develop` and various feature branches. After reviewing and merging features branches to `develop`, we'd merge `develop` into `master`, then tag `master` with a release version, which would then trigger an automated release to Itch.io.

We decided that every discipline on our team must use Git. This is where we had a ton of problems and merge conflicts as very few people had prior experience with Git. This was the first project where I saw a merge request with 1000+ modified files and 100+ conflicts. Nearing the end of production we managed to resolve most of the issues as everyone got more comfortable using Git. Frequent commits, short-lived branches and proper `.gitignore` with `.gitattributes` configurations helped out the most.

The `.gitattributes` configuration that we used is pretty standard, apart from the _Terrain_ section:
```
# Unity.
*.cs diff=csharp text
*.cginc text
*.shader text
*.mat merge=unityyamlmerge eol=lf
*.anim merge=unityyamlmerge eol=lf
*.unity merge=unityyamlmerge eol=lf
*.prefab merge=unityyamlmerge eol=lf
*.physicsMaterial2D merge=unityyamlmerge eol=lf
*.physicMaterial merge=unityyamlmerge eol=lf
*.asset merge=unityyamlmerge eol=lf
*.meta merge=unityyamlmerge eol=lf
*.controller merge=unityyamlmerge eol=lf
*.unitypackage filter=lfs diff=lfs merge=lfs -text

# git-lfs.

# Image.
*.jpg filter=lfs diff=lfs merge=lfs -text
*.jpeg filter=lfs diff=lfs merge=lfs -text
*.png filter=lfs diff=lfs merge=lfs -text
*.gif filter=lfs diff=lfs merge=lfs -text
*.psd filter=lfs diff=lfs merge=lfs -text
*.ai filter=lfs diff=lfs merge=lfs -text

# Audio.
*.mp3 filter=lfs diff=lfs merge=lfs -text
*.wav filter=lfs diff=lfs merge=lfs -text
*.ogg filter=lfs diff=lfs merge=lfs -text

# Video.
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.mov filter=lfs diff=lfs merge=lfs -text

# 3D Object.
*.FBX filter=lfs diff=lfs merge=lfs -text
*.fbx filter=lfs diff=lfs merge=lfs -text
*.blend filter=lfs diff=lfs merge=lfs -text
*.obj filter=lfs diff=lfs merge=lfs -text

# Wwise.
*.bnk filter=lfs diff=lfs merge=lfs -text

# ETC.
*.a filter=lfs diff=lfs merge=lfs -text
*.exr filter=lfs diff=lfs merge=lfs -text
*.tga filter=lfs diff=lfs merge=lfs -text
*.pdf filter=lfs diff=lfs merge=lfs -text
*.zip filter=lfs diff=lfs merge=lfs -text
*.dll filter=lfs diff=lfs merge=lfs -text
*.aif filter=lfs diff=lfs merge=lfs -text
*.ttf filter=lfs diff=lfs merge=lfs -text
*.rns filter=lfs diff=lfs merge=lfs -text
*.reason filter=lfs diff=lfs merge=lfs -text
*.lxo filter=lfs diff=lfs merge=lfs -text

# Terrain
*Terrain.asset filter=lfs diff=lfs merge=lfs -text
LightingData.asset filter=lfs diff=lfs merge=lfs -text
```

The `.gitignore` is pretty standard as well, except for the _Streaming assets_ section which focuses on cleaning a lot of noise generated by Wwise:
```
# This .gitignore file should be placed at the root of your Unity project directory.
#
# Get latest from https://github.com/github/gitignore/blob/master/Unity.gitignore
#
/[Ll]ibrary/
/[Tt]emp/
/[Oo]bj/
/[Bb]uild/
/[Bb]uilds/
/[Ll]ogs/
/[Uu]ser[Ss]ettings/

# MemoryCaptures can get excessive in size. They also could contain extremely sensitive data.
/[Mm]emoryCaptures/

# Asset meta data should only be ignored when the corresponding asset is also ignored.
!/[Aa]ssets/**/*.meta

# Uncomment this line if you wish to ignore the asset store tools plugin.
# /[Aa]ssets/AssetStoreTools*

# Autogenerated Jetbrains Rider plugin.
/[Aa]ssets/Plugins/Editor/JetBrains*

# Rider
.idea/

# Visual Studio cache directory.
.vs/

# Gradle cache directory.
.gradle/

# Autogenerated VS/MD/Consulo solution and project files.
ExportedObj/
.consulo/
*.csproj
*.unityproj
*.sln
*.suo
*.tmp
*.user
*.userprefs
*.pidb
*.booproj
*.svd
*.pdb
*.mdb
*.opendb
*.VC.db

# Unity3D generated meta files.
*.pidb.meta
*.pdb.meta
*.mdb.meta

# Unity3D generated file on crash reports.
sysinfo.txt

# Builds.
*.apk
*.aab
*.unitypackage
Builds/

# Crashlytics generated file.
crashlytics-build.properties

# Packed Addressables.
/[Aa]ssets/[Aa]ddressable[Aa]ssets[Dd]ata/*/*.bin*

# Temporary auto-generated Android Assets.
/[Aa]ssets/[Ss]treamingAssets/aa.meta
/[Aa]ssets/[Ss]treamingAssets/aa/*

# Streaming assets.
/Assets/StreamingAssets/*
!/Assets/StreamingAssets/Audio*

/Assets/StreamingAssets/Audio/*
!/Assets/StreamingAssets/Audio/GeneratedSoundBanks*

/Assets/StreamingAssets/Audio/GeneratedSoundBanks/*
!/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Windows*
!/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Mac*

/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Windows/*
/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Mac/*
!/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Windows/*.bnk*
!/Assets/StreamingAssets/Audio/GeneratedSoundBanks/Mac/*.bnk*

# Mac.
.DS_Store

# Files which are constatly edited by Unity.
/Packages/packages-lock.json
```

### Programming
Working with a Unity project and maintaining structure is difficult. Doing so in a team of 7 programmers is even harder. Even though a lot of features in the game are quite simple, having this many programmers made it even harder as it is very hard to keep track of changes in Unity - a single change can break so many unexpected things. Nevertheless, I think we managed to develop a decent enough workflow, if there project were to be re-done, I think most of the issues would be avoided.

#### Prefabs
Having so many people working on a Unity project you can't edit the scenes as it creates a ton of conflicts which are pretty much impossible to resolve. To avoid this, we tried to make it so that each feature could stand on its own as a prefab. I think this is the only way to work with Unity in a large team. We managed to achieve this to a certain degree on the programming team, however we had a lot of issues though with the level designers as prefabs were just dropped into the scenes with random connections. The level design could be improved here by splitting up the levels into smaller section prefabs or by using additive scene loading.

#### Trigger prefabs
{{<
  gallery
  "triggers.png" "Trigger prefabs"
  "triggers-wwise.png" "Trigger prefab used with a Wwise component"
>}}

To avoid further bottlenecks on the programmer team we decided to build a set of simple prefabs for the level and audio designers instead of adding all functionality via code. We setup a simple system where each component would expose a set of `UnityEvent` fields which would correspond to a specific actions, e.g. a player entered a trigger.

Using this system we then made a set of nested prefabs and prefab variants where a number of these components would be chained to achieve certain behaviour. For example if we wanted to play audio via Wwise when a player enters a zone, all we had to do was add a `PlayerTrigger` prefab with and slot in a `WwiseEventAdapter` into the `PlayerTrigger` components `UnityEvent` field. This approach removed the bottleneck completely - so much so that the level designers made too many levels with various interactions, we had to cut some as there was not enough time to decorate them.

This was also allowed us to easily abstract interactions with Wwise. Instead of hard-coding communication with Wiwwse via code, we created a bunch of adapter components which would plug into the `UnityEvent` based trigger system.

#### Game Events
When using prefabs we noticed that it is a chore to wire everything up in the scenes, not to mention that it creates conflicts later on. To avoid this we implemented a system where prefabs would be able to communicate with each other using `ScriptableObject` assets by sending _events_. This system worked great for certain scenarios where we had to trigger some global behaviour, for example player dies - show the game over screen. Using events in some cases meant that the prefabs did not require any setup other than dragging them into the scene as `.asset` files live outside of scene scope. We ported this system to a package [Unity Scriptable Objects](https://github.com/chark/unity-scriptable-objects) with some other things that we used for communication between prefabs.

#### Setup scene
Using our event system it made no sense to use singletons. Due to this, we created a scene which gets injected before the current scene by using additive scene loading. This scene contains manager objects responsible for the game state, UI, etc. Our setup was far from ideal as it meant that during development the project loading times while working from within the editor would be longer.

The core component that enables this is the `RuntimeInitializeOnLoadMethod` attribute which allows to execute code before a scene is loaded:
```
public static class SetupSceneInjector
{
    [RuntimeInitializeOnLoadMethod(RuntimeInitializeLoadType.BeforeSceneLoad)]
    private static void InjectSetupScene()
    {
        var sceneIndex = Scenes.ActiveSceneBuildIndex;
        if (sceneIndex.IsSetupScene())
        {
            return;
        }

        InjectSetupScene(sceneIndex);
    }

    private static void InjectSetupScene(int previousSceneIndex)
    {
        SceneManager
            .LoadSceneAsync(Scenes.SetupSceneBuildIndex)
            .completed += operation => OnSetupSceneInjected(previousSceneIndex);
    }

    private static void OnSetupSceneInjected(int previousSceneIndex)
    {
        Scenes.SetupSceneBuildIndex.SetActiveScene();

        SceneManager
            .LoadSceneAsync(previousSceneIndex, LoadSceneMode.Additive)
            .completed += operation => previousSceneIndex.SetActiveScene();
    }
}
```

In adittion to that, the setup scene contains a `GameObject` with a component that loads the next scene if the current scene is the setup scene:
```
public class SceneHandler : MonoBehaviour
{
    private void OnEnable()
    {
        SceneManager.sceneLoaded += OnSceneLoaded;
    }

    private void OnDisable()
    {
        SceneManager.sceneLoaded -= OnSceneLoaded;
    }

    private void OnSceneLoaded(UnityEngine.SceneManagement.Scene scene, LoadSceneMode mode)
    {
        scene.SetActiveScene();
        sceneLoadedEvent.RaiseGameEvent();

        // We're on the setup scene, load the next scene (menu scene). In the editor we don't
        // want this to happen, as it might be useful to test the setup scene as it is.
#if !UNITY_EDITOR
        var buildIndex = scene.buildIndex;
        if (buildIndex.IsSetupScene())
        {
            StartCoroutine(LoadScene(menuSceneIndex, false));
        }
#endif
    }
}
```

This will work if the setup scene is the first one in the scene build order. Note that this will cause `Awake` and `Start` methods to fire twice while within the editor as it loads the current scene, unloads it and then loads it back in after the setup scene is injected.

#### Core mechanic
{{<
  gallery
  "core-mechanic.png" "Core mechanic with a buggy mesh"
>}}

To implement the core mechanic we had to generate a mesh procedurally based on what the player draws on the screen. Getting mesh generation to work correctly is rather tricky. So much so that only managed to get it working decently nearing the end of production. Even then we had to use an external library [UnityPlumber](https://github.com/federicocasares/unity-plumber) to generate the mesh.

#### Motion Matching
TODO: insert webm here!

One of the requirements from DADIU is that the game must use a [Motion Matching](https://www.gdcvault.com/play/1023280/Motion-Matching-and-The-Road) system. At the time of writing Unity does not have a working system for this, apart from some paid assets. Due to the fact that we had little experience in the programmer team this task was a huge hurdle. We couldn't implement a working system during our first two mini games at all. While working on our final game we still had issues with this until we found a repository [MotionMatching](https://github.com/nashnie/MotionMatching) with a semi working solution which we decided to integrate. The final result was poor, but it worked.

Personally I think using such complex animation system makes no sense in a small game like this. It adds extreme amounts of complexity while offering little control over your animations.

#### Optimization
TODO: insert birds eye view here!

Even though some of the levels were quite huge, we did not have that many performance problems. The only two issues we encountered were that our custom shader generated a lot of draw calls, did not support batching and also `MeshCollider` optimization problems. Shader related performance problems were resolved by removing reflection probe data while collider problems were resolved by using primitive `BoxCollider` and `SphereCollider` components over most meshes also marking them `Static` where possible.

#### Command console
TODO: insert command console

The `develop` builds which would be published frequently were mostly used by QA. To aid in testing we implemented a command console which provides a set of commands to switch between scenes, fly around, trigger game events. During initial phases of the project the console was not used that much except during public presentations. However, later on as the features piled up it was quite a valuable tool as you could no longer get to specific parts of the game quickly in order to test them, and also not everyone wanted to open up Unity just to test something where we had an `.exe` sitting in our drive for the latest build.

I think if such a tool were to be used to its fullest potential it has to be implemented from the get go. I think having commands which would allow to setup testing scenarios would be invaluable where one could type a command to receive a string that could be pasted on a Trello ticket to quickly get to a bug.

#### Custom Unity tools
TODO: insert pic of prefab creator, object culling

One of the best features that Unity provides in my opinion is that its extremely easy to extend. For example, during first few weeks of production we noticed that for the CGs in order to create prefabs they have to do a lot of manual work by adding collider components, marking objects as `Static`, etc. We were able to automate this process by adding custom context menus which would require a single click to setup prefabs in bulk.

Another issue we encountered was that at some point some the audio designers couldn't work on the project due to the scenes containing to many objects. They couldn't open up the scenes at all due to the computer dying immediately after opening one of them. We narrowed this issue down to particles and solved it by creating a custom context menu that would disable the particles before opening a scene and re-enable them when saving it to avoid accidental content loss.

For future projects I will try to explore the extensibility provided by Unity even more and I encourage everyone to do so. There is no better feeling that seeing 10+ prefabs setup automatically with a single click where it would normally take lots of manual labour.

### Closing notes
TODO
