---
layout: project
title: Shader Interface
description: A custom C++ tool that makes shader development faster and less painful.
image: /assets/images/ShaderInterface.jpg
github: https://github.com/yourname/repo
tech: [C/C++, OpenGL, GLSL, ImGui, FFmpeg]
---

## Overview
Shader Interface is a lightweight C++ app for developing and testing GLSL shaders without the usual setup overhead. It provides a simple environment to preview shaders, tweak parameters live, and capture outputs. The focus is fast iteration: write shader code, see results immediately, repeat.

## Key Features
- **Live shader preview** for quick iteration and debugging
- **Uniform controls UI** (ImGui) for real-time parameter tweaking
- **Hot-reload workflow** for shaders (edit → save → update)
- **Capture / export support** using FFmpeg for recordings or demos
- **Minimal engine core** built around OpenGL + GLSL, kept simple on purpose
