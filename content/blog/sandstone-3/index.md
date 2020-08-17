---
description: TODO
title: Sandstone - part 3
date: 2020-07-06

images:
  - /blog/sandstone-3/screenshot.jpg
---

{{<
  gallery
  "screenshot.jpg" "Screenshot of the main scene with exposed internals"
>}}

### Intro
[part 2]({{< relref "blog/sandstone-2" >}})

### Baking lighting
I spent a lot of time trying to get lighting right. Using real-time lighting I was having difficulties illuminating darker areas and the end result was always bland, thus I decided to use real-time lights in combination with baked global illumination.

Unity currently provides a GPU based light-mapping solution called Progressive Lightmapper, which is rather fast. However due to having a large environment I quickly ran into multiple issues.

#### Long baking times
First issue was that baking would never finish, the editor reported that it would take 10+ hours. To fix this, I had to reduce the Scale in Lightmap value under Mesh Renderer component for large objects. For example the scale value for the terrain I used is `0.1`. This greatly reduced the baking duration to around 8 minutes (using RTX 2060) for the main scene.

#### Artifacts
{{<
  gallery
  "artifacts-backface.jpg" "Artifacts caused by using too high Backface Tolerance value"
>}}

Using a lower scale introduced a lot of artifacts. For example some objects such as rocks would be baked incorrectly and their edges would either glow extremely brightly or be too dark while in direct sunlight. The root cause was that Backface Tolerance was set too high under Lightmap Parameters. I've experimented with multiple values for this setting, however in the end I had to use a value of `0` to eliminate most of the artifacts, however some still remained. This also increased the baking time a bit.

#### Overlapping UVs
{{<
  gallery
  "artifacts-uv.jpg" "Artifacts caused by bad UV mapping"
>}}

Most of the models were custom made in Blender. Since I'm terrible at modeling, I stuck to a minimalistic low-poly style. The hardest part in modeling for me was UV mapping. Due to this, I mostly used Smart UV Project functionality which generates a UV map automatically. This was fine until I started baking lighting and had already made half of the models. Having improper UV maps causes UV map overlapping which creates dark areas around the edges of objects. Ultimately this is caused by overlapping UVs.

Unity is able to Generate Lightmap UVs automatically via model import settings. There are a bunch of settings that you can tweak in order to get them to generate correctly. However I could not get any combination of these settings to work. I've tried manually creating UVs for some of the models with a decent island margin, which yielded good results. The takeaway here is - create the UVs by hand.

### Real-time lighting
Using real-time lighting was a must for this project as the player is able to pick up objects which emit light. The biggest problem here was getting the shadows to work right.

#### Shadow quality
{{<
  gallery
  "shadows-low-ql.jpg" "Low quality shadows"
  "shadows-stripes.jpg" "Striped shadows"
>}}

Shadow quality was a major pain to get right. The biggest issue initially was rough, pixelated shadows. I was able to resolve this by enabling Soft Shadows and using Four Cascades.

Another issue was having stripes over the shadows. This one was easy to resolve by playing around with shadow Depth Bias and Normal Bias settings.

#### Leaky shadows
{{<
  gallery
  "shadows-leaky.jpg" "Shadows leaking light"
>}}

Since I was trying to follow a low-poly aesthetic, I found that simply using no smoothing groups on the models yields nice results. However this causes _gaps_ in between the triangles when they're lit by real-time lights. In order to fix this, I edited each model to use  smoothing groups in Blender and instead used a [shader](https://github.com/Edvinas01/sandstone/blob/master/Assets/Shaders/Shader%20Graphs/Low%20Poly.shadergraph) to achieve the low-poly aesthetic.

#### Dynamic objects
{{<
  gallery
  "objects.jpg" "Incorrectly lit dynamic objects"
  "objects-probes.jpg" "Pyramid with light probes"
>}}

When mixing baked and real-time lighting, dynamic objects require additional information in order to be lit correctly. This was due to not using Light Probes. Setting up Light Probes in Unity is painful, to reduce this pain I've used the [Simple Light Probe Placer](https://assetstore.unity.com/packages/tools/simple-light-probe-placer-58290) asset. It was still quite messy even when using the asset as I had to go and tweak each Light Probe Group manually so none of the probes protrude the terrain, however this sped up the the whole process immensely.

### Closing notes
TODO
