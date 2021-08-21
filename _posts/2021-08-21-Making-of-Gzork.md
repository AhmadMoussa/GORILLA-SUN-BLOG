---
title: Making of&#58; Gzork
author: Ahmad Moussa
categories:
  - p5js
description: recreating my sketch titled Gzork
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-21-12-Making-of-Gzork/Gzork2.gif" alt="" /></span>


In this blog post we'll recreate my Processing 20-th anniversary fundraiser sketch, step-by-step.
<strong>If you're viewing this on your mobile, hit the stop button for the interactive widgets to view the code!</strong>
<h2>Creating a Grid</h2>

<pre><code>function setup(){
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
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  off = 50;
  spc = 10;

  noFill();
}

function draw() {
  background(220);

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
<pre><code>((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
</code></pre>

<h2>Animating the Grid</h2>
We can make this grid much more interesting if we add some motion to it, which will give us something that is very similar to some of my earliest sketches from this year. We'll be modulating the strokeWeight of the draw points and circles with a sine function that takes as input their x and y coordinates, allowing us to obtain very interesting patterns:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
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
  background(220);

  t = frameCount/20

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {

      strokeWeight(4 + 4*sin(x/20+y/20+t))
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

The important line here is strokeWeight(4 + 4&#42;sin(x/20+y/20+t)) which is doing all the heavy lifting. Here we're dividing by an arbitrary number to attenuate the effect of plugging in the x and y coordinates into the sine function. Try changing the numbers. Also try plugging in different combinations of x and y like x&#42;y for example and see what patterns you get!

<h2>Modulating with respect to another coordinate</h2>
In addition to plugging a specific value or coordinate into the modulating sine function, we could plug in a distance. For example, the distance of each point to the center of the sketch:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
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
  background(220);
  t = frameCount / 20;

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {
      d = dist(x, y, w / 2,w/2 );

      sinin = t + d / 20;
  
      strokeWeight(5 + 5 * sin(sinin));
      
      ((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
    }
  }
}
</script>
<p></p>

<h2>Adding Color</h2>
Instead of using premade color palettes, I enjoy plugging sinusoidal color into the RGB components of the stroke and fill functions. My current favorite palette is the following:

<pre><code>stroke(
  127.5 + 127.5 * sin(sinin),
  127.5 + 127.5 * cos(sinin),
  127.5 - 127.5 * cos(sinin)
);
</code></pre>

This eesentially bounces the RGB values in a very specific way between 0 and 255, generating pink, purple and green colors, that gradually interleave:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
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
  background(0);
  t = frameCount / 20;

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {
      d = dist(x, y, w / 2,w/2 );

      sinin = t + d / 20;
      
      stroke(
        127.5 + 127.5 * sin(sinin),
        127.5 + 127.5 * cos(sinin),
        127.5 - 127.5 * cos(sinin)
      );
      
      strokeWeight(5 + 5 * sin(sinin));
  
      ((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
    }
  }
}
</script>
<p></p>

<h2>Background Transparency Trick</h2>
Here's a trick that I heavily use for my sketches, exploiting the transparency of the background color, for some pseudo motion blur:

<pre><code>background(0,0,0,20)
</code></pre>

The transparency component also has a range that goes from 0 to 255. The lower you set it the more transparent the background. It's effect is hard to describe, but essentially it makes moxing shapes drawn to the canvas have colored smudgy trails. I also enjoy dark blue backgrounds over just plain black:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
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
  background(0,0,100,20);
  t = frameCount / 20;

  for (x = off; x < w - off; x += spc) {
    for (y = off; y < w - off; y += spc) {
      d = dist(x, y, w / 2,w/2 );

      sinin = t + d / 20;
      
      stroke(
        127.5 + 127.5 * sin(sinin),
        127.5 + 127.5 * cos(sinin),
        127.5 - 127.5 * cos(sinin)
      );
      
      strokeWeight(5 + 5 * sin(sinin));
  
      ((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
    }
  }
}
</script>
<p></p>
