---
title: Scrolling Perlin Noise Textures and Tricks
author: Ahmad Moussa
categories:
  - p5js
description: In this part of the perlin noise tutorial series we will have a look at creative applications of Perlin Noise and using it to texture.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

This is the second part of the explorative series on Perlin Noise in P5JS and Processing, and focuses on applying Perlin Noise to a 2 dimensional grid of items on the canvas and how we can obtain interesting textures in this manner.

We've already had a look at scrolling perlin noise across the canvas by increasing one or multiple noise parameters over time. However, creativity doesn't stop there!

<h2>Quantizing the noise values</h2>
<pre><code>
function setup() {
  createCanvas(301, 301);
  t = rez = c = n = 0.005;
  strokeWeight(3);
}
function draw() {
  background(0);
  noStroke();
  for (i = 0; i < height; i += 3) {
    for (j = 0; j < width; j += 3) {
      n = noise(i * rez, j * rez + t) * 3;
       n > 3
        ? (c = 3)
        : n > 2
        ? (c = 2)
        : n > 1
        ? (c = 1)
        : (c = 0);
      fill(c*100);
      rect(i, j, 3);
    }
  }
  t += 0.01;
}
</code></pre>


<pre><code>
function setup() {
  createCanvas(301, 301);
  t = rez = c = n = 0.02;
  strokeWeight(3);
}
function draw() {
  background(0);
  noStroke();
  for (i = 0; i < height; i += 3) {
    for (j = 0; j < width; j += 3) {
      n = noise(i * rez, j * rez + t);
       if(n > 0.5){
               fill(0);
       }else{
         fill(127.5*127.5*sin(i*0.02+millis()/1000),
              127.5*127.5*cos(j*0.02+millis()/1000),
              127.5)
       }
      rect(i, j, 3);

    }
  }
  t += 0.08;
}
</code></pre>
