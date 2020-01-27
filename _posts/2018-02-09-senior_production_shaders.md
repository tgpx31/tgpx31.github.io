---
title: Senior Production&#58; Shader Week
layout: post
date: 2018-02-09 20:40:00 -0500
author: Brian Baron
selectedurl: Blog
---
Last week I didn't get to do nearly as much work as I wanted to--early on I caught the flu, and was in bed for the rest of the week. This week though, I plan to get back into the works. I've started off today by writing a simple shader our artists requested.

<strong>Color Replacement Shader</strong>

<img src="/assets/images/portfolio/capstone/color_rp_og.png" width="25%">
<img src="/assets/images/portfolio/capstone/color_rp_masked.png" width="25%">

The idea is pretty simple: take a color that you want to mask, and replace it with a new one. This is the same idea that allows us to do effects such as green-screening. To fit our artists' needs, this shader needed these to be exposed to the editor.

<img src="/assets/images/portfolio/capstone/editorwindow.png" width="55%">

Editor view of the shader

On the inside, the code is pretty straightforward. We sample the texture for our UV coordinates, and compare the color to our targeted color to mask. If it's within the tolerance range, we set it to the replacement color.

```
void surf (Input IN, inout SurfaceOutputStandard o)
{
    // Albedo comes from a texture tinted by color

    fixed4 texColor = tex2D(_MainTex, IN.uv_MainTex);
    float diff_color = abs(texColor.r - _ColorToMask.r) + abs(texColor.g - _ColorToMask.g) + abs(texColor.b - _ColorToMask.b) / 3;

    float isMasked = step(_Tolerance, diff_color);
    texColor *= isMasked;
    texColor += (1 - isMasked) * _Color;

    fixed4 c = texColor;

    o.Albedo = c.rgb;
    // Metallic and smoothness come from slider variables
    o.Metallic = _Metallic;
    o.Smoothness = _Glossiness;
    o.Alpha = c.a;
}
```

The part I'm happiest about is being able to accomplish this without any conditionals. This is achieved using step, a popular glsl function. If our difference in color is less than the tolerance, we get zero back. Otherwise we get 1. That allows us to multiply our color by the result--if it's not masked, there's no change. If it is masked, we remove all color, and then refill it with the new color.

Later this week I'll be working on a shader that allows for objects blocking the camera to become transparent, allowing us more flexibility on camera angles.