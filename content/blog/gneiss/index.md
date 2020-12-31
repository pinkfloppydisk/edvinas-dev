---
description: TODO
title: "Gneiss: A Misshapen Journey"
date: 2020-12-31

images:
  - /blog/gneiss/game-logo.jpg
---

{{<
  gallery
  "logo.jpg" "Game logo"
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
  "teamcity.jpg" "TeamCity dashboard"
>}}

Initially we spun up GitLab CI by following [Automatically building and deploying Unity applications to itch.io](https://johanhelsing.github.io/2020/05/17/gitlab-docker-itch-deploy.html) post by Johan Helsing. This setup was quite handy and quick to setup, but as our projects grew it became way too slow as we had to wait 20+ minutes for a single build to complete. In addition to that, GitLab updated their CI limits to only allow for 400 minutes per group which we already exceeded.

Afterwards we setup a cheap Linux TeamCity server on DigitalOcean. Setting this up was a lot more difficult as Unity didn't like the fact that it was building on Linux inside Docker containers. We also had a bunch of issues bundling Wwise assets as well due to missing `.dll` errors. For future projects I would use a Windows server or build a dedicated machine just for this.

One think that worked really well with automated builds were Discord messages. After each successful or failed build a message would be posted to specific Discord channels. This brought attention to failing builds from other disciplines and not only QA and programmers. In addition to that, waiting for our bots named _Pinky_ and _Brain_ to post a message on successful development and release builds was quite charming.

#### Git
For managing source code we used GitLab with a bunch of different Git clients. We chose GitLab as it allowed for more space per project - 10GB. For the Git clients, we used GitHub Desktop, Sourcetree and Git Bash. Initially I recommended GitHub Desktop for the Designer, Audio and CG teams, but as we progressed with the production, we encountered a lot of problems with it. I wouldn't recommend using GitHub Desktop on large projects.

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
TODO

#### Unity Events
TODO

#### Setup scene
TODO

#### Core mechanic
TODO

#### Motion Matching
TODO

#### Custom Unity tools
TODO

### Closing notes
TODO
