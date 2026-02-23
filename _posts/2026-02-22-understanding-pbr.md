---
layout: post
title: Understanding Physicaly Based Rendering From Scratch
---

## 🚧 Under Construction 🚧

![Screenshot 1](/assets/images/blog/under-construction/image01.png)

## Disclaimer
Please be informed that the content presented below is not an original work, but a compilation of multiple sources, rewritten under my own understanding of the topic. The author is not an expert and does not claim professional credentials in this field.

## Motivation
There was quit long time since I was going around lighting topic. Projects I built over last few months didn't require any lighting work at all, so I was't quite ambitious in learning any lighting stuff at this point. But recently something in my head started realize that such a huge topic can't be ignored, since being afraid of building lighting systems in my renderers close many doors for having fun. Therefore I decided to some of the research about what the lighting actualy is. 

That wasn't my first time diving into any lighting, since I used to learn about simplest lighting system known to me, called phong lighting, that I used in one of my raymarching shaders before. But there is not much to learn about phong lighting, since that's pretty unrealistic way to fake perspective of your rendering scene. That's why I kept my eye on something way more complicated and fun to implement, something that is called the industy standard of lighting, the Physicaly Based Rendering.

![Screenshot 1](/assets/images/blog/understanding-pbr/image01.png)

## What is PBR?
PBR (Physically Based Rendering) is a set of rendering techniques that aim to make materials look consistent and believable by following real-world light behavior. A common approach uses the microfacet model to describe how rough surfaces reflect light.

## Rendering Equation
Any calculation towards Physically Based model starts from the Rendering Equation:

$$L_o(\mathbf{x},\omega_o)=L_e(\mathbf{x},\omega_o)+\int_{\Omega^+}f_r(\mathbf{x},\omega_i,\omega_o)\,L_i(\mathbf{x},\omega_i)\,V(\mathbf{x}, \omega_i)\,(\mathbf{n}\cdot \omega_i)\,d\omega_i$$

Where:

$L_o(\mathbf{x},\omega_o)$ - outgoing light, or final color of pixel

$L_e(\mathbf{x},\omega_o)$ - emmited light

$\int_{\Omega^+}f_r(\mathbf{x},\omega_i,\omega_o)\,L_i(\mathbf{x},\omega_i)\,V(\mathbf{x}, \omega_i)\,(\mathbf{n}\cdot \omega_i)\,d\omega_i$ - infinite amount of light on $\mathbf{x}$ point of mesh within hemisphere $\omega_o$.

Where:

$f_r(\mathbf{x},\omega_i,\omega_o)\$ - Bidirectional Reflectance Distribution Function

$L_i(\mathbf{x},\omega_i)\$ - incoming light

$V(\mathbf{x}, \omega_i)\$ - shadowing term

$(\mathbf{n}\cdot \omega_i)\$ - shallow angle light intensity

## Rendering Equation in code

```glsl
vec3 PBR() {
  // main vectors
  vec3 FO = baseReflectance;
  vec3 N = normalize(normal);

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

## GGX/Trowbridge-Reitz Normal Distribution Function

```glsl
float D(float alpha, vec3 N, vec3 H)
{
    float numerator = pow (alpha, 2.0);
    float NdotH = max (dot (N, H), 0.0);
    float denominator = PI * pow (pow(NdotH, 2.0) * (pow(alpha, 2.0) - 1.0) + 1.0, 2.0) ;
    denominator = max (denominator, 0.000001) ;
    return numerator / denominator;
}
```

## Schlick-Beckmann Geometry Shadowing Function

```glsl
float G1 (float alpha, vec3 N, vec3 X)
{
    float numerator = max (dot (N, X), 0.0) ;
    float k = alpha / 2.0;
    float denominator = max(dot (N, X), 0.0) * (1.0 - k) + k;
    denominator = max (denominator, 0.000001) ;
    return numerator / denominator;
}
```

## Smith Model

```glsl
float G(float alpha, vec3 N, vec3 V, vec3 L)
{
    return G1 (alpha, N, V) * G1 (alpha, N, L);
}
```

## Fresnel-Schlick Function

```glsl
vec3 F(vec3 FO, vec3 V, vec3 H)
{
    return FO + (vec3(1.0) - FO) * pow(1 - max(dot (V, H), 0.0), 5.0);
}
```

## Applying Reflection

```glsl
vec4 ApplyReflection(vec4 color, vec3 N)
{
    vec3 V = normalize(fs_in.TangentViewPos - fs_in.FragPos); // view vector
    vec3 R = normalize(reflect(V, N)); // reflection vector
    vec4 RColor = vec4(texture(skybox, vec3(R.x, -R.y, R.z)).rgb, 1.0); // reflected color
    return mix(color, RColor, baseReflectance.r);
}
```

## Main Function

```glsl
void main()
{
    vec4 color = vec4(PBR(), 1.);
    color.rgb = pow(color.rgb, vec3(1.0/2.2));
    color = ApplyReflection(color, normalize(normal));
    FragColor = color;
}
```