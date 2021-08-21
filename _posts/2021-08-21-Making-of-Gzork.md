---
title: Making of: Gzork
author: Ahmad Moussa
categories:
  - p5js
description: recreating my sketch titled Gzork
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

<h2>Creating a Grid</h2>

<pre><code>
function setup(){
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);
  off = 50
  spc = 10
}

function draw(){
    background(220)
for (x = off; x < w - off; x += spc) {
   for (y = off; y < w - off; y += spc) {
      point(x,y)
   }
 }
}
</code></pre>

<h2>Alternating Points and Circles</h2>

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay>
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  off = 50;
  spc = 10;

  noFill();
  frameRate(50)
}

function draw() {
  background(0, 0, 100, 20);

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {

      if ((x / spc) % 2 == 0) {
        if ((y / spc) % 2 == 0) {
          point(x, y);
        } else {
          ellipse(x, y, 5);
        }
      } else {
        if ((y / spc) % 2 == 0) {
          ellipse(x, y, 5);
        } else {
          point(x, y);
        }
      }
    }
  }
}
</script>
<p></p>

Alternatively, you could compress the entire if statement block into a one liner with ternary operators:
<pre><code>
((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
</code></pre>

<h2>Animating the Grid</h2>
We can make this grid much more interesting if we add some motion to it, which will give us something that is very similar to some of my earliest sketches from this year. We'll be modulating the strokeWeight of the draw points and circles with a sine function that takes as input their x and y coordinates, allowing us to obtain very interesting patterns:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay>
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  off = 50;
  spc = 10;

  noFill();
  frameRate(50)
}

function draw() {
  background(0, 0, 100, 20);

  t = frameCount

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {

      strokeWeight()
      if ((x / spc) % 2 == 0) {
        if ((y / spc) % 2 == 0) {
          point(x, y);
        } else {
          ellipse(x, y, 5);
        }
      } else {
        if ((y / spc) % 2 == 0) {
          ellipse(x, y, 5);
        } else {
          point(x, y);
        }
      }
    }
  }
}
</script>
<p></p>
