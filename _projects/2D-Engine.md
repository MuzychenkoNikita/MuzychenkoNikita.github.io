---
layout: project
title: 2D Engine
description: A C++ custom game engine utilizing OpenGL API.
image: /assets/images/2DEngine.jpg
github: https://github.com/MuzychenkoNikita/2D-Engine
tech: [C++, OpenGL, GLSL, ImGui, Json]
---

## Overview
This project is a custom rendering engine I built to learn OpenGL.

## Key Features
* Bindless descriptors
* Multi-threaded command buffer generation
* GPU-driven culling

![Screenshot of Debug View](/assets/images/debug.jpg)

Here is a code snippet of the pipeline barrier:
```cpp
vkCmdPipelineBarrier(...);