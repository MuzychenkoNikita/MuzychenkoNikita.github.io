---
layout: post
title: Understanding Physicaly Based Rendering From Scratch
---

## Disclaimer
Please be informed that the content presented below is not an original work, but a compilation of multiple sources, rewritten under my own understanding of the topic. The author is not an expert and does not claim professional credentials in this field.

![Screenshot 1](/assets/images/blog/understanding-pbr/image01.png)

## What is PBR?
PBR (Physically Based Rendering) is a set of rendering techniques that aim to make materials look consistent and believable by following real-world light behavior. A common approach uses the microfacet model to describe how rough surfaces reflect light.

Here is some code:

```cpp
vec3 PBR() {
  // main vectors
  vec3 FO = baseReflectance;
  vec3 N = normalize(normal);
    
  // For directional light
  // vec3 L = normalize(fs_in.TangentLightPos);
    
  // For point light and spot light
  vec3 L = normalize(fs_in.TangentLightPos - fs_in.TangentFragPos);
  vec3 H = normalize(V + L);
    
  // rendering equation
  vec3 Ks = F(FO, V, H);
  vec3 Kd = (vec3 (1.0) - Ks) * (1.0 - metalic);
  vec3 lambert = albedoMesh / PI;
  vec3 cookTorranceNumerator = D(alpha, N, H) * G(alpha, N, V, L) * F(FO, V, H) ;
  float cookTorranceDenominator = 4.0 * max (dot (V, N), 0.0) * max(dot (L, N), 0.0) ;
  cookTorranceDenominator = max(cookTorranceDenominator, 0.000001) ;
  vec3 cookTorrance = cookTorranceNumerator / cookTorranceDenominator;
  vec3 BRDF = Kd * lambert + cookTorrance;
  vec3 outgoingLight = emissivityMesh + BRDF * lightColor * max (dot (L, N), 0.0) ;
  return outgoingLight;
}
```

```math
E = m c^2
```

$$L_o(\mathbf{x},\omega_o)=L_e(\mathbf{x},\omega_o)+\int_{\Omega^+}f_r(\mathbf{x},\omega_i,\omega_o)\,L_i(\mathbf{x},\omega_i)\,V(\mathbf{x}, \omega_i)\,(\mathbf{n}\cdot \omega_i)\,d\omega_i$$

$$
L_o(\mathbf{x}, \omega_o)=L_e(\mathbf{x}, \omega_o)+\int_{\Omega^+}f_r(\mathbf{x}, \omega_i, \omega_o)\,L_i(\mathbf{x}, \omega_i)\,(\mathbf{n}\cdot \omega_i)\,d\omega_i
$$

Inline: $L_o(\mathbf{x}, \omega_o)$

$L_o(\mathbf{x}, \omega_o)=L_e(\mathbf{x}, \omega_o)+\int_{\Omega^+}\dots$
