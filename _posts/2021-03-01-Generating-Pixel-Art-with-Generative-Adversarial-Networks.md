---
title: Generating Pixel Art with Generative Adversarial Networks.
author: Ahmad Moussa
categories:
  - p5js
description: This blog post describes a course project where I attempted to generate pixel art with Generative Adversarial Networks.
thumbnail_path: 2021-02-17-Linear-Interpolation-in-p5js.png
published: false
---
<div style="width:100%;height:0;padding-bottom:100%;position:relative;"><iframe src="https://giphy.com/embed/3o85xyGspig9UUbHc4" width="100%" height="100%" style="position:absolute; pointer-events:none;" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div>

<h2>Conception</h2>
In September 2019, for the second semester of my Master studies, I signed up for a course called 'Computer Graphics Optimization'. Which sounded interesting to me, since I had taken a course on computer graphics before, where I learned to use OpenGL and implement many computer graphics concepts such as ray-tracing, creating meshes and a lot about the camera object and how illumination is simulated. However, I wasn't ready for this course. Very quickly I found myself knee-deep in convex optimization and trying to understand what the heck a Lagrangian was, among other topics that went way over my head. Ultimately I did pass by the skin of my teeth, the professor being kind enough to adhere to the practice exam that uploaded prior to the actual final. 

The important part of the story is the course project that we were assigned. Not knowing anyone in the class I opted to do it individually. One of the topics that was covered in the class, were neural networks, since they're inherently massive optimization problems. And because I was playing a lot of Celeste at the time, I decided to write in my project proposal, that I wanted to generate pixel art with GANs and/or a mixture of CycleGAN. And you can probably guess, that I've bitten off more than I could chew.


<h2>What are GANs?</h2>
The term 'GAN' is an abbreviation and stands for a type of neural network that was introduced in the 2014 research paper 'Generative Adversarial Networks' by Ian Goodfellow. Their main purpose is that it's capable of replicating a specific type of data when you show it enough examples of it.

<h2>Back to my project</h2>
<p>I soon came to the realization that what I had in mind was not feasible, due to two major reasons. Firstly because I didn't have enough training data, and secondly because I didn't have the required expertise to do it. The first couple of afternoons that I spent on it, I scavenged pinterest for 'pixel art characters', and did indeed find quite a number of usable sprite sheets. I then wrote some python scripts to separate those sheets and save each pixel art character individually. And that's where I started to see the problem with my idea, pixel art is usually created at very specific scales, which usually are 16x16, 32x32, 64x64 large canvases. And generating new images with a GAN requires you to have an extremely clean dataset, finding different pixel art images and rescaling them would usually distort them, in addition to not knowing what the original grid size of the canvas was. I did run a DCGAN on small dataset of around 200 images, and you can see the training results in the embedded youtube video.</p>

<iframe width="100%" height="315" src="https://www.youtube.com/embed/Czkf9bhI4xk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p>What you're seeing is a visualization of the learned latent space of the GAN. Every training epoch I generate an image and then I concatenated all of them into the video you're seeing. I also made some music for it, but that's besides the point. Towards the end of the video you can see a shape emerge that vaguely looks like a sprite, I dont think that it would have gotten any better if I left it training for longer since the dataset was very small, all things considered.</p>

