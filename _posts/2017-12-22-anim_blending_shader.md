---
title: Animation Blending on the GPU
layout: post
date: 2017-12-22 17:40:00 -0500
author: Brian Baron
selectedurl: Blog
---
This semester I took a course based around the fundamentals of animation programming. The class hugely focused on applying our tools (interpolation algorithms and 3D math)--we started off with speed control over a curve, and went deep into how quaternions work. We learned about forward-kinematics, and how to build a skeleton. The last major topic we took a look at was animation blending and blend trees. Basically applying our interpolation principles to animations and clips, to produce smoother in-between frames. For our final project, my friend Duncan and I decided we wanted to do animation blending on the GPU.

<img src="/assets/images/portfolio/2017_animation_programming/ab_uml.png" width="75%">

Off the bat we knew that we were going to have to learn how to utilize compute shaders in OpenGL. With our initial knowledge, we planned on putting our constant data (number of nodes, and the base-pose channel values for each node) in a uniform buffer. We planned to use a varying for the other data needed (operation identifier, pose arguments, interpolation parameters), and our output since they needed to be modifiable.

With a bit of research, we decided to use <a href="https://www.khronos.org/opengl/wiki/Shader_Storage_Buffer_Object">Shader Storage Buffer Objects</a> (SSBOs) instead. Both compute shaders and SSBOs were added in OpenGL 4.3, which posed no issues with the hardware we were working on. SSBOs allow for us to allocate memory on the GPU that we can reference both in shaders and in our C side application, which provided a means for us to share data easily.

Our final implementation included four blend operations: add, scale, LERP, and BiLerp. This was mostly due to time constraints--if we continue to work on this demo I'd like to add full blend tree support. That being said, we got it working! Our blending works for clips, single poses, and for individual nodes. For this demo, we're using clips and single-frame poses.

All of our operations are done on a single-node level. For a hierarchy/skeleton, we just iterate through all the nodes in the hierarchy and perform the operation per-node.

<img src="/assets/images/portfolio/2017_animation_programming/ab_add.gif" width="75%">

Add (walk and crouch)

<img src="/assets/images/portfolio/2017_animation_programming/ab_scale.gif" width="75%">

Scale (base pose to walk)

<img src="/assets/images/portfolio/2017_animation_programming/ab_lerp.gif" width="75%">

LERP (crouch and walk)

<img src="/assets/images/portfolio/2017_animation_programming/ab_bilerp.gif" width="75%">

BiLERP (crouch, walk, idle, and wobble)

All of this is contained within our compute shaders, and thrown back out to the C application just as we hoped! We ended up with multiple shaders for different operations to avoid conditional logic on the GPU. Overall we're very happy with the result--this project was a good experience to apply and expand our skills from both animation and graphics programming.