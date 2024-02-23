---
title: "Fengine: a 2D game engine"
date: 2024-02-15
last_modified_at: 2024-02-15
excerpt: "A personal 2D game engine from way back"
header:
  image: /assets/fengine_menu.jpg
  teaser: /assets/fengine_mit.jpg
sidebar:
  - title: "Keywords"
    text: "game engine, physics, collision detection"
  - title: "Tools"
    text: "C++, SDL2, OpenGL"
gallery:
  - url: /assets/polygonal_wall.jpg
    image_path: assets/polygonal_wall.jpg
    alt: "polygon collision support"
gallery1:
  - url: /assets/fengine_mit_big.jpg
    image_path: assets/fengine_mit_big.jpg
    alt: "Physics based lighting"
gallery2:
  - url: /assets/scripting.jpg
    image_path: assets/scripting.jpg
    alt: "scripting"
---

A game engine project I worked on for 3 years during high school. It's pretty complex with several subsystems such as physics, 2D graphics, entity & events, map & navigation, scripting, and timer systems! 

It is completely written in C++ using SDL2 for cross-platform window handling and OpenGL for custom graphics. For fonts, image loading, and audio, I used the corresponding SDL libraries.

I ended up using it for my MIT maker porfolio in hope that it boosts my chances at MIT. Alas, MIT only wanted IMO gold medalists from Canada!

Anyhow, if you're interesetd, check out the video below to see a demo. A pdf documentation of the engine is also [available](/assets/FengineDoc.pdf).

{% include video id="k_RUfOZKJ8g" provider="youtube" %}

Here are three cool features in case you skipped the video!

## Polygonal collision detection
I, like any beginner, started off with a boring AABB collision detection and quickly realized its limitations. After learning picking up some basic linear algebra (haha, what a crazy time when I didn't even know linear algebra), implemented a basic collision response system capable of detecting polygonal collision detections (using, of course, the separating axis theorem).
{% include gallery caption="Polygonal collision detection" %}

## Physics based lighting
Initially, I wanted to make a suspense based game in which lighting plays an important role. So implemented an [algorithm](https://www.redblobgames.com/articles/visibility/) that lights up the scene based on the environment. Paired with the OpenGL upgrade (SDL does not support any fancy rendering), I got this:
{% include gallery id="gallery1" caption="Cool lighting based on physics" %}

## Baby scripting system
Finally, I made a very basic (baby) scripting system that allows cutscenes to be developed without recompiling any C++ code. The language is extremely simple since I didn't know how parsers work :) To see this in action, just check out the video!
{% include gallery id="gallery2" caption="Very simple scripting support" %}