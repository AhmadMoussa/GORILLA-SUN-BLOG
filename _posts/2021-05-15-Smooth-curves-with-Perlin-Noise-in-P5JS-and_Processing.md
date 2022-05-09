---
layout: default
title: Smooth cruves with Perlin Noise in P5JS and Processing
author: Ahmad Moussa
categories:
  - p5js
description: In this part of the perlin noise tutorial series we will have a look at creating smooth curves with Perlin Noise in P5js and processing.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

<pre><code>
function setup() {
  windowSize = min(windowWidth,windowHeight)
  createCanvas(windowSize, windowSize);
}

let numVertices = 20;
let midPoint = 150;
let endPoint = 150;
function draw() {
  background(220);

  beginShape();
  for (i = 0; i < numVertices; i++) {
    var n = noise(50 + (midPoint / numVertices) * i,
                  millis() / 4000);
    n = (map(n, 0, 1, 0, 200) * i) / numVertices;
    curveVertex(
      50 + (midPoint / numVertices) * i,
      windowSize / 2 - n
    );
  }

  for (i = 0; i < numVertices + 1; i++) {
    var n = noise(
      50 + midPoint + (endPoint / numVertices) * i,
      millis() / 2000 
    );
    n = map(n, 0, 1, 0, 200) * (1 - i / numVertices);
    curveVertex(
      50 + midPoint + (endPoint / numVertices) * i,
      windowSize / 2 - n
    );
  }

  endShape();
}

</code></pre>
