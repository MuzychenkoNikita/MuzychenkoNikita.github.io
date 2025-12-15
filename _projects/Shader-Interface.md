---
layout: project
title: Shader Interface
description: A custom C++ software to make your shader development less painfull.
image: /assets/images/ShaderInterface.jpg
github: https://github.com/yourname/repo
tech: [C/C++, OpenGL, GLSL, ImGui, FFmpeg]
---

## Overview
This project is a custom rendering engine I built to learn Vulkan.

## Key Features
* Bindless descriptors
* Multi-threaded command buffer generation
* GPU-driven culling

![Screenshot of Debug View](/assets/images/debug.jpg)

Here is a code snippet of the pipeline barrier:
```cpp
vkCmdPipelineBarrier(...);