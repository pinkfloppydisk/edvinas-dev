---
description: VR game development is fun and easy with Unity. In this post I talk about my experiences in creating my first VR game called Sandstone.
title: Sandstone (part 1)
date: 2020-06-29
---

{{<
  gallery
  "screenshot.jpg" "Screenshot of the game"
>}}

### Intro
Few months ago I've decided to try out VR game development and get back to Unity. The whole development process was enticing and immersive given the medium. In the end, I spent around one month developing the game, including using mostly custom assets. The final result, including the source can be fetched from [GitHub](https://github.com/Edvinas01/sandstone).

While working on this project, I've encountered quite a few annoyances that I'd like to avoid in the future. In contrast, some experiences were fun and insightful which I would like to talk about as well. The purpose of this post (and upcoming ones) is to document these points and learn from them for future projects.

This first post is mostly shallow observations and an introduction to the upcoming posts. The next posts will be more technical and in-depth.

### Idea
The general idea of the project was an exploration game set in a desert environment. Initially, I just wanted to play around with new Unity features and make something pretty. Later on, I decided to stick to a minimalistic Egyptian theme instead of just going for a generic desert. Since I would have to make quite a few Egyptian themed assets, I also decided to incorporate some historical Ancient Egyptian aspects into the game - a small narrative. For the story, I read up some articles on Ancient Egyptian religion and decided to use the article [Life after Death](https://www.historymuseum.ca/cmc/exhibitions/civil/egypt/egcr04e.html) as a template.

While designing the game, I borrowed an Oculus Rift headset. After playing some demos I noticed that a lot of games with minimalistic design and assets still hold up in VR. The added spatial awareness and immersion makes even the most barren worlds interesting. Thus, I decided to shift focus to VR.

### Software
When choosing the software, I stuck with what I know best, except for Rider IDE being a new try. I ended up using:

* Unity 2019.3
* Git 2.25
* Blender 2.82
* Rider IDE 2019.3
* Audacity 2.3.3
* Paint.NET 4.2.10

#### Windows & Unity
The development was done on Windows and setting up Unity was a breeze. At the moment I don't think it's feasible to use Unit on Linux. I tried setting up few projects on Linux Mint in the past, however each time I would end up on Windows due to some odd driver related issue.

#### Git
 Getting Git to work with Unity was quite an easy task as well. In the past I would use Google Drive or something similar to store Unity projects, due to them taking up 1 GB or more space. This time I tried to avoid downloading bloated assets and managed to fit the project under 200 MB so this wasn't an issue. However, I can see that other issues might arise when working in a team. When editing scenes and other Unity specific settings, Unity does quite a few changes to various generated files. These files are not human readable and are essential to the project. They must be pushed to remote and will appear in pull requests. I'm having a hard time imagining where someone actually reviews them and tries to resolve merge conflicts.

#### Blender
The 2.80 version of Blender had completely revamped their UI and shortcuts. Using Blender was probably the hardest part of the project as I had to spend a lot of time modeling. Having played around with older versions of Blender was a downside as most of what I knew was obsolete (mainly shortcuts). However, the sheer amount of features in this piece of software is amazing. I'd definitely like to spend more time mastering Blender, and I believe the time spent re-learning it was worth it.

#### Rider
Before going with Rider, I've tried setting up Visual Studio Code with a bunch of extensions, so it would integrate nicely with Unity. I got to a point where it was usable and performing quite well, however I found Rider to be miles ahead. The out of the box auto completion and debugging functionality was outstanding. The setup was mostly painless as well. I guess having used JetBrains products for years has made me biased.

#### Audacity & Paint.NET
Using Audacity and Paint.NET was sufficient for this project as the focus was not on textures or sound. I needed only few minor edits here and there in terms of textures, as for sound I only had to make few loops and adjustments. In future projects however, I will probably be using GIMP. To get Paint.NET to perform some functions, I had to install a bunch of plugins from questionable sources.

### Virtual reality
I used an Oculus Rift headset with Touch controllers and two base stations. I found the hardware itself to be sturdy and well-made, and didn't encounter any eyestrain during development and gameplay. The only issue I faced was the tracking going off in some cases, which was mostly due to my table and laptop blocking the view of the base stations.

There were a few annoyances however. The biggest one was that each time I had made a change in the game, it meant that I had to put on the headset (or keep it on my head), stand up, test the game and repeat. This process was physically irritating (specially putting on the headset), and some days it put me off of working on the project. In the end though, I found that making a feature work without the headset and integrating later on was way faster and a more pleasant workflow. I think having a standing desk would reduce this irritation as well.

Another issue was that I always had to keep the Oculus application running in the background. The application on my machine took up 10+ GB. Also, each time I wanted to test my game, I had to wait for the Oculus application to load. The Oculus application is slow and while running in the background simulates a VR environment of its own. This slows down the computer, and the development process.

### Assets
Most of the assets for this project were custom-made. When modeling more complex models however, I downloaded reference models from [TurboSquid](https://www.turbosquid.com) and essentially made low-poly versions of them.

All the sounds are from external sources. I had quite a lot of luck finding what I needed on [Freesound](https://freesound.org). In addition to that I had used some sound files from various GameAudioGDC bundles. A friend of mine also helped out with audio and provided some slick assets, checkout his work at [Sutkus Audio](https://sutkusaudio.com/).

### Closing notes
Overall, working with VR and Unity on this project was a pleasant and refreshing experience. Most issues I stumbled upon fall flat given the immersive development environment which is possible only with VR. It is extremely satisfying being able to naturally pick up and interact with objects and environments that you create. I urge anyone with a VR headset to play around with Unity.

In next post, I'll go into the project setup, VR library choices and other technical details.
