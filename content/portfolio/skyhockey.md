---
title: SkyHockey
summary: Air Hockey in Space and VR.
teaser: skyhockey/teaser.jpg
github: SkyHockey
date: "2019-10-05"
slug: skyhockey
---

This is a small air hockey game set in space. Unfortunately the advanced rendering features were not straightforward to port to WebGL, so there is no web version available at this time.

I think the rendering steps are fun to look at, so I will explain them here. If you do want to try the game for yourself the build instructions are on [GitHub](https://github.com/flostellbrink/SkyHockey).

![Game with Overlay](/images/skyhockey/game_with_overlay.png)

## Rendering

The game implements a deferred renderer with the following steps:

1. The Shadow map is rendered from the light's perspective.
   1. 1 Channel: The depth value is normalized and stored.
      ![Depth](/images/skyhockey/deferred_0_depth.png)
   2. 1 Channel: The square of the depth is also normalized and stored.
      ![DepthSquared](/images/skyhockey/deferred_0_depth_squared.png)
   3. The entire texture is blurred to enable smooth shadows with VSM.
2. All objects properties are collected in 4 textures. These are the stored channels:
   1. 1 Channel: The objects ids, for looking up material properties:
      ![Ids](/images/skyhockey/deferred_1_id.png)
   2. 3 Channels: The albedo colors, contain the object textures:
      ![Colors](/images/skyhockey/deferred_1_color.png)
   3. 3 Channels: The worldspace coordinates, for shadow mapping:
      ![Coords](/images/skyhockey/deferred_1_coords.png)
   4. 3 Channels: The surface normals, for lighting:
      ![Normals](/images/skyhockey/deferred_1_normals.png)
   5. 2 Channels: The camera space object velocity, for motion blur:
      ![Velocities](/images/skyhockey/deferred_1_velocity.png)
3. The information from all textures is composited into a single texture.
4. Motion blur is applied to the rendered image.
5. FXAA is applied to hide aliasing artifacts.
6. The blurred image is combined with the sharp background.
7. Finally the performance overlay is drawn ontop of the frame.
