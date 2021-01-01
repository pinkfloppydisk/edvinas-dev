---
description: Long post about game development in a large student team. This post focuses on process related and technical challenges and takeaways that I learned while working on this game.
title: "Gneiss: A Misshapen Journey"
date: 2021-01-01

images:
  - /blog/gneiss/game-logo.jpg
---
TODO: remove unused images
TODO: add new images
TODO: add webms

{{<
  gallery
  "game-logo.jpg" "Game logo"
>}}

### Intro
This year I attended [DADIU](http://www.dadiu.dk) programme as a lead programmer where we built two small games over the course of two weeks and one big game - "Gneiss: A Misshapen Journey" over the course of six weeks. In this post I'll talk about the production process and technical challenges that we faced when building this game using the Unity game engine.

Links:
- The game itself - [Gneiss: A Misshapen Journey](https://dadiu.itch.io/gneiss)
- Mini game 1 - [muClayTion](https://blackmadhatter.itch.io/mini-game-1)
- Mini game 2 - [muClayTion 2](https://blackmadhatter.itch.io/mini-game-2)

### Production process
Our team consisted of 23 people: director, art director, producer, 2 QAs, 2 sound designers, 4 game designers, 5 CG artists and 7 programmers. Working in such a large team is an extremely difficult task. Everyone has a lot of ideas and different skill sets. Coming to this programme I expected that technical issues would be the most challenging part, however making sure that assets get in, get properly integrated, tested and convey the idea that we're going for is the hard part.

#### Pipeline
Working on this project did not differ _that much_ from regular software development processes apart from the asset pipeline. Rather than just focusing on making sure the code works there was an extra step, we had to make sure that other teams are able to properly quickly get their assets into the game. Due to this we had to develop custom tools (more on that later) and help out other disciplines with various Unity issues. As a lead programmer, this was pretty much my job the entire time rather than working on features. Personally I enjoyed a lot as after developing a tool or teaching someone on the team on how to use some specific Unity functionality to integrate their assets provided a tight feedback loop.

#### Structure
For our first two games we tried splitting up into feature teams where each team would focus on a specific feature, e.g. enemies, main character, etc. Each team would have a certain number of programmers, designers and CG artists, etc. Using this approach the biggest issue was integrating everything together and keeping an overview as this approach required a lot of communication for which we were not ready for. In addition to that, we encountered a lot of bottlenecks from the programming side as other disciplines were not comfortable/experienced in using Unity which meant that most asset integration relied on the programmers.

For the final game we dropped feature teams and followed a Scrum model. Each day, we would have a daily meeting in the morning with the whole team where we'd discuss outstanding issues. Then we would split it into disciplines where we had our stand-up meetings. We would work like this on a weekly basis, where at the end of the week we would publish a release build. Afterwards, we would do a gameplay demo with the whole team and then discuss the goals for the next sprint. Afterwards we'd split up into disciplines and do a retrospective meeting followed by a sprint planning. This model worked quite well as it was a lot easier to keep an overview of the whole project.

Following this model we also managed to resolve the previously mentioned bottleneck on the programmer side. I think this was partially due to the fact that people got more familiar with Unity and Git, but also that we outlined deliverables that discipline must add to the gem by themselves on each task during the sprint planning sessions.

#### Platforms
Initially we used Codecks for managing tasks in our first mini game. It became apparent that in such a large team this platform was not the right fit. It was difficult to split up tasks between disciplines and keep an overview of what is going on. For the next games we switched to Trello and stuck with that.

For communication, we used Discord. Given how COVID-19 was spiraling out of control during this programme, I think this was the perfect platform. We prepared a bunch of text and voice channels and aggressively chatted over them. To share documents, etc, we used Google Drive which worked quite well.

### Project setup

#### Unity
For the project we used Unity 2019.4 (LTS) with Universal Render Pipeline (URP) template.

#### Automation
{{<
  gallery
  "teamcity.png" "TeamCity dashboard"
>}}

Initially we spun up GitLab CI by following [Automatically building and deploying Unity applications to itch.io](https://johanhelsing.github.io/2020/05/17/gitlab-docker-itch-deploy.html). This setup was quite handy and quick to execute, but as our projects grew it became way too slow as we had to wait 20+ minutes for a single build to complete. In addition to that, GitLab updated their CI limits to only allow for 400 minutes per group which we already exceeded by the time we got the e-mail.

Afterwards we a Linux TeamCity server on DigitalOcean. Setting this up was a lot more difficult as Unity didn't like the fact that it was building on Linux inside Docker containers. We also had a bunch of issues bundling Wwise assets as well due to missing `.dll` errors. For future projects I would use a Windows server or use a dedicated machine just for builds.

One think that worked really well with automated builds were Discord messages. After each successful or failed build a message would be posted to specific Discord channels. This brought attention to failing builds from other disciplines and not only QA and programmer teams. In addition to that, waiting for our bots named _Pinky_ and _Brain_ to post a message on successful development and release builds was quite charming.

#### Git
For managing the source code we used GitLab with a bunch of different Git clients. We chose GitLab as it allowed for most space per project compared to other platforms (10GB). For Git clients, we used GitHub Desktop, Sourcetree and Git Bash. Initially I recommended GitHub Desktop for the design and CG teams, but as we progressed with production we encountered a lot of problems with it. I wouldn't recommend using GitHub Desktop on large projects.

We used a _simplified_ Git flow model where we had `master`, `develop` and various feature branches. After reviewing and merging feature branches to `develop`, we'd merge `develop` into `master` and tag `master` with a release version, which would then trigger an automated release to Itch.io.

We decided that every discipline on our team must use Git. This is where we had a ton of problems and merge conflicts as very few people had prior experience with it. This was the first project where I saw a merge request with 1000+ modified files and 100+ conflicts. Nearing the end of production we managed to resolve most of the issues as everyone got more comfortable using Git. Informing everyone about frequent commits, using short-lived branches and setting up proper `.gitignore`, and `.gitattributes` configurations seemed to have helped a bit as well.

The `.gitattributes` configuration that we used is pretty standard, apart from the _Binary assets_ section which:
```gitignore
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

# Binary assets.
*Terrain.asset filter=lfs diff=lfs merge=lfs -text
LightingData.asset filter=lfs diff=lfs merge=lfs -text
```

The `.gitignore` is pretty standard as well, except for the _Streaming assets_ section which focuses on cleaning a lot of noise generated by Wwise:
```gitignore
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
Working with a Unity project and maintaining structure is difficult on its own. Doing so in a team of 7 programmers is even harder. Even though a lot of features in the game are quite simple, having this many programmers made it even harder as it is very hard to keep track of changes in Unity - a single change can break so many unexpected things. Nevertheless, I think we managed to develop a decent enough workflow, if there project were to be re-done, I think most of the issues would be avoided.

#### Prefab workflow
Having so many people working on a Unity project we couldn't directly edit the scenes as it created a ton of conflicts which were pretty much impossible to resolve. To avoid this, we tried to make it so that each feature could stand on its own as a prefab. We managed to achieve this to a certain degree on the programming team, however the level design and CG teams had some issues where a lot of objects were not converted into prefabs and could not be edited outside scenes.

I was recently informed that this workflow can be achieved and improved without using prefabs as Unity provides [Multi-Scene editing](https://docs.unity3d.com/Manual/MultiSceneEditing.html) feature. This actually looks like a better approach for when working with level specific objects. The only issue of by using the scene approach is that one cannot make connections between objects in different scenes. However, this can be worked around by establishing connections between objects using `ScriptableObject` assets.

#### Trigger prefabs
To avoid bottlenecks from the programmer team we decided to build a set of simple prefabs for the design teams instead of adding all functionality via code. We created a simple system where each component would expose a set of `UnityEvent` fields which would get triggered on specific actions, e.g. a player entered a trigger.

Using this system we then made a set of nested prefabs and prefab variants where a number of these components would be chained together to achieve certain behaviours. For example if we wanted to play audio via Wwise when a player enters a zone, all we had to do was add a `PlayerTrigger` prefab with and slot in a `WwiseEventAdapter` into the `PlayerTrigger` components `UnityEvent` field. This approach removed the bottleneck completely - so much so that the level designers made too many levels with various interactions, we had to cut some as there was not enough time to decorate them. This also allowed us to easily abstract interactions with Wwise. Instead of hard-coding communication with Wiwse via code, we created a bunch of adapter components which would plug into the `UnityEvent` based trigger system.

Example of a trigger component:
```c#
[RequireComponent(typeof(Collider))]
public class ColliderTrigger : MonoBehaviour
{
    #region Editor Fields

    [SerializeField]
    [Tooltip("Should this trigger only if a specific collider enters it")]
    private bool useWhitelist = default;

    [SerializeField]
    [ShowIf("useWhitelist")]
    [Tooltip("Colliders which are allowed to interact with this trigger")]
    private List<Collider> whitelist = default;

    [SerializeField]
    private UnityEvent onTriggerEnter = default;

    [SerializeField]
    private UnityEvent onTriggerExit = default;

    #endregion

    #region Unity Event Methods

    private void Awake()
    {
        GetComponent<Collider>().isTrigger = true;
    }

    private void OnTriggerEnter(Collider other)
    {
        if (IsTrigger(other))
        {
            onTriggerEnter.Invoke();
        }
    }

    private void OnTriggerExit(Collider other)
    {
        if (IsTrigger(other))
        {
            onTriggerExit.Invoke();
        }
    }

    #endregion

    #region Private Methods

    private bool IsTrigger(Collider other)
    {
        return !useWhitelist || whitelist.Contains(other);
    }

    #endregion
}
```

Example Wwise component:
```c#
public class WwiseEventAdapter : MonoBehaviour
{
    #region Editor Fields

    [SerializeField]
    [Tooltip("Wwise event that will be triggered")]
    private AK.Wwise.Event wwiseEvent = default;

    #endregion

    #region Public Methods

    public void PostEvent()
    {
        PostEvent(gameObject);
    }

    public void PostEvent(GameObject obj)
    {
        wwiseEvent.Post(obj);
    }

    public void StopEvent()
    {
        StopEvent(gameObject);
    }

    public void StopEvent(GameObject obj)
    {
        wwiseEvent.Stop(obj);
    }

    #endregion
}
```

Surprisingly such a simple approach worked really well as it turns out that most interactions in our game didn't need anything more complex. I personally think it also greatly benefited the sound designers - initially they wanted to add some questionable code snippets from Wwise documentation into various places of the code, but this was avoided using this approach.

#### Game Events
In some cases prefabs require communication with each other. This requires one to insert the prefabs into a scene and establish those connections via the editor. This is error prone as level designers were not always aware of all the connections that need to be made. To avoid this we implemented a system where prefabs would be able to communicate with each other using `ScriptableObject` assets by sending _events_ over those assets. Due to that fact that in Unity `.asset` files live outside the scope of scenes we could create prefabs which would reference the said event assets and wouldn't require any other connections. This essentially made it so that we could take a prefab, drop it into a scene, hit play without any worries.

This system worked great for certain scenarios where we had to trigger some global behaviour, e.g. player dies - show the game over screen. In addition to that, this system put a lot of power into the level designers hands - if they wanted to load the next scene or do something more complex, all they had to do was drop in a `PlayerTrigger` prefab and slot in a `LoadNextScene` event asset.

We ported most of the code that we used for this event system into a package [Unity Scriptable Objects](https://github.com/chark/unity-scriptable-objects). The system that we used and ported over is based on [Unite2017](https://github.com/roboryantron/Unite2017) repository. I highly recommend taking a look at it as it has some other useful suggestions on how `ScriptableObject` assets could be used.

#### Setup scene
Using Game Event system it felt counterintuitive using singletons as it would go against the injectable nature of `ScriptableObject` assets. To avoid singletons we created a scene `SetupScene` which gets loaded before the current scene by using additive scene loading. This scene contains manager objects responsible for the game state, UI, managers, etc. I think this was a really nice feature as it made it easier to reason about the global game state and allowed to completely avoid singletons. Our setup was not from ideal as it had a flaw - the loading times while working from within the editor would be longer, as in order to inject the scene we have to load the scene that the user wants to play, unload it, load the `SetupScene` and then additively load back in the unloaded scene. This is not a problem in a production build though.

The core component that enables this is the `RuntimeInitializeOnLoadMethod` attribute which allows execution of code before a scene is loaded:
```c#
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

In addition to that, the `SetupScene` contains a `GameObject` with a component that loads the next scene if the active scene is the `SetupScene`. This is required for the final build as the `SetupScene` is the first one to load in the build order:
```c#
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

Also, to get this to work, a utility method class is required as well:
```c#
public static class Scenes
{
    public const int SetupSceneBuildIndex = 0;

    public static int ActiveSceneBuildIndex => ActiveScene.buildIndex;

    public static UnityEngine.SceneManagement.Scene ActiveScene =>
        SceneManager.GetActiveScene();

    public static bool IsSetupScene(this int buildIndex)
    {
        return buildIndex == SetupSceneBuildIndex;
    }

    public static void SetActiveScene(this int buildIndex)
    {
        var scene = SceneManager.GetSceneByBuildIndex(buildIndex);
        SceneManager.SetActiveScene(scene);
    }

    public static void SetActiveScene(this UnityEngine.SceneManagement.Scene scene)
    {
        SceneManager.SetActiveScene(scene);
    }
}
```

Note that this will cause lifecycle methods such as `Awake` and `Start` to fire twice when playing the game from the editor. This is due to the problem that I've mentioned previously.

#### Core mechanic
TODO: add webm showing the core mechanic

To implement the core mechanic we wanted to generate a mesh procedurally based on what the player draws on the screen. Getting mesh generation to work correctly was rather tricky. We couldn't get it to work properly until the final moments of the production. In the end, we did a bit of searching and used [UnityPlumber](https://github.com/federicocasares/unity-plumber) which worked rather nicely, even though it still has flaws. The takeaway here is that we should have done more research in existing solutions. There are a lot of assets/libraries which could have been used to implement this.

#### Motion Matching
TODO: add webm showing the animation

One of the requirements from DADIU is that the game must use a Motion Matching system. Motion Matching is a way of playing animations using large data sets of motion capture data instead of using state machines or blend trees. The benefit of using such a system is that one can achieve extremely realistic animations and transitions between animation frames. The downside is that it can sometimes feel unresponsive. I'd recommend to watch [Motion Matching and The Road to Next-Gen Animation](https://www.gdcvault.com/play/1023280/Motion-Matching-and-The-Road) to get more information on this topic.

At the time of writing this, Unity does not have a fully functioning system for this. There are some paid assets, but I have not tested them. Due to this, implementing Motion Matching was a huge challenge as none of us wanted to build a game which revolves around character animations (this is the reason why we use it only in the first level). We couldn't implement a working system during our first two mini games. While working on our final game we still didn't have a working system until we stumbled upon [MotionMatching](https://github.com/nashnie/MotionMatching) GitHub repository - thank you [nashnie](https://github.com/nashnie)! Using the said repository, we managed to port it over to our game and use our own custom Rokoko animations in combination with animations from Mixamo. The end result was still buggy, but it sufficed.

Personally I think using such complex animation system doesn't make much sense in a _small_ game like this. It adds extreme amounts of complexity while offering little control over your animations. However, for large _AAA_ games I think Motion Matching could be an invaluable tool.

#### Performance
TODO: add pic showing particles and modules

For rendering we used URP. This was decided as the art direction for the game did not require that many fancy features. I think this was the right decision as we didn't have that many performance issues related to rendering. We did encounter some problems with our custom shader during the last week of production, however most of them were minor issues. The biggest challenge related to performance was the VFX Graph. We went a bit overboard with some particle systems which tanked performance pretty badly. We didn't want to reduce the particles too much as some scenes relied on having huge emission rates, e.g. clouds. If we had more time however, I think this could be resolved by adding graphics options menu.

Another challenge we faced in was related to colliders. Most static objects in our scenes are made out of modular prefabs. For example, if I want to create a skyscraper, I'd stack up a bunch of house prefab modules and call it a day. The issue with this approach was that each module had its own `MeshCollider` component. This tanked the performance pretty badly, so much so that some scenes would take 30+ seconds to load while in the editor. The solution for this was to manually go through each prefab and replace `MeshCollider` components with a set of primitive collider components and make sure they are marked as `Static`.

#### Command console
To help out when testing development and release builds for QA we implemented a developer console (try it out by pressing `F12` on your keyboard while in-game). However, it wasn't as useful as expected as QAs would have the editor open most of the time when testing. Yet, we encountered some tricky situations during presentations where the game would bug out, or we'd have to showcase a specific area, this is where the command console shined the most.

I believe if such a tool were to be used to its fullest potential it has to be implemented as early as possible. I think having commands which would allow pointing out bugs would be invaluable, specially for bigger projects. For example a command to receive a string that could be pasted into a ticket management system to describe a bug, then the same string could be re-used to trigger that bug later on.

#### Custom Unity editor tools
Due to the sheer amount of assets we had to automate some steps, such as prefab creation. It is quite easy to build custom tooling for the Unity editor, and the payoff is immense. The tool that I wish we would have implemented was automated composite convex hull creation, that would have saved a lot of time. As creating colliders by using Unity's primitive colliders is hard and time-consuming for some props, for example rocks. For future projects I will try to explore the extensibility provided by Unity's editor even more. In addition to the automated convex hull creation I'd like to create some utilities which would allow settling of dynamic objects.

### Closing notes
All in all the programme was extremely fun and challenging. Honestly, I chose this programme as a safe way to see if game development is something I would like to do, and it did not disappoint. Having met so many awesome and creative students and professionals is an experience I'll never forget. I'll take note of all the technical challenges that we faced for future projects and will write more posts when I lear something new. Thank you for reading and have a nice year 2021!
