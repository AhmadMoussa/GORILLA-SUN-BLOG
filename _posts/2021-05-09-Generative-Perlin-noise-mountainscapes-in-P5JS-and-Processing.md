---
title: Perlin Noise in P5JS and Processing
author: Ahmad Moussa
categories:
  - p5js
description: The Perlin noise generator in 5js is a fantastic feature that allows us to code up a plethora of fantastic sketches, here's one that simulates colorful mountainscapes.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

I'm super excited about this blog post, because it's the start of a series of posts on Perlin Noise. A wonderful tool to have in your arsenal.

With perlin noise we can create shapes that we wouldn't otherwise be able to make. This series will focus on Perlin Noise, particularly in combination with the curveVertex() function in P5JS, which we will make heavy use of. When starting out in creative coding, it might be difficult to move away from circular and rectangular shapes, as these seem to be the most natural things to code up. 

Whilst drawing freehand on a paper, it's only natural to utilize smooth curves instead of hard edges and perfect circles. Perlin noise allows us to explore this venue through code. In p5js we can invoke perlin noise through the noise() function, which works fundamentally different than the random() function. We'll discuss the  difference between them in a bit. There already are great resources that explain Perlin Noise in great detail, such as this <a href='https://genekogan.com/code/p5js-perlin-noise/'>writeup</a> by Gene Kogan (I also recommend checking out his <a href='https://genekogan.com/'>website</a>. Especially if you want to get into AI art). As well as Daniel Schiffman aka the <a href='https://www.youtube.com/channel/UCvjgXvBlbQiydffZU7m1_aw'>Coding Train</a>'s introductory <a href='https://www.youtube.com/watch?v=8ZEMLCnn8v0&ab_channel=TheCodingTrain'>video on Perlin Noise</a>.

The special thing that will, hopefully, set this tutorial apart from already existing tutorials, is that it will exemplify how to use Perlin Noise in  a number of specific scenarios. Where we'll start gentle and then iteratively increase the difficulty.
