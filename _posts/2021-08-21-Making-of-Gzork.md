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

1. <a href='#grid'>Creating a Grid</a>
2. <a href='#alternate'>Alternating Points and Circles</a>
3. <a href='#animate'>Animating the Grid</a>
4. <a href='#distance'>Distance Fields</a>
5. <a href='#color'>Adding Color</a>
6. <a href='#transparency'>Background Transparency Trick</a>


<h2><a name='grid'></a>Creating a Grid</h2>

The great Persian Polymath Al-Khawarizmi once said: 'Grids are awesome'! well maybe he didn't, but you can't prove that. That doesn't change the fact that grids are awesome. We'll start off by coding up a grid which we'll base our code on in later steps:
  
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

<h2><a name='alternate'></a>Alternating Points and Circles</h2>

The next step would be alternating between points and circles in our grid, this isn't necessary, we could perfectly do with just points, or circles, but it adds an interesting texture. It's also a little tricky to make the grid alternate between two different shapes. We can do that with if and else statements that check if the current coordinates are even or odd:
  
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

Alternatively, we could compress the entire if statement block into a one liner with ternary operators:
<pre><code>((x / spc) % 2 == 0)?((y / spc) % 2 == 0)?point(x,y):ellipse(x,y,5):((y / spc) % 2 == 0)?ellipse(x, y, 5):point(x,y)
</code></pre>

<h2><a name='animate'></a>Animating the Grid</h2>
We can make this grid much more interesting if we add some motion to it, which will give us something that is very similar to some of my <a href='https://gorillasun.de/blog/Sketch-from-Scratch-1-Colorful-checkerboard-in-P5JS'>earliest sketches from this year</a>. We'll be modulating the strokeWeight of the draw points and circles with a sine function that takes as input their x and y coordinates, allowing us to obtain very interesting patterns:

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

<h2><a name='distance'></a>Distance Fields</h2>
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

Equivalently you could add the distance to multiple points, in as follows:

<pre><code>d1 = dist(x, y, w / 2,w/2 );
d2 = dist(x, y, 100, 100);

sinin = t + d1 / 20 + d2/20;
</code></pre>

Plug this into the sketch and see what you get! Lastly you could also make the distance point be the location of the mouse coordinates:
<pre><code>d1 = dist(x, y, mouseX, mouseY);

sinin = t + d1 / 20 + d2/20;
</code></pre>

<h2><a name='color'></a>Adding Color</h2>
Instead of using premade color palettes, I enjoy plugging sinusoidal color into the RGB components of the stroke and fill functions. My current favorite palette is the following:

<pre><code>stroke(
  127.5 + 127.5 * sin(sinin),
  127.5 + 127.5 * cos(sinin),
  127.5 - 127.5 * cos(sinin)
);
</code></pre>

This essentially bounces the RGB values in a very specific way between 0 and 255, generating pink, purple and green colors, that gradually interleave:

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

Go ahead and try plugging in different things in each one of the sine and cosine functions to see if you get a result that you like!

<h2><a name='transparency'></a>Background Transparency Trick</h2>
Here's a trick that I heavily use for my sketches, exploiting the transparency of the background color, for some pseudo motion blur:

<pre><code>background(0,0,0,20)
</code></pre>

The transparency component also has a range that goes from 0 to 255. The lower you set it the more transparent the background. It's effect is hard to describe, but essentially it makes moxing shapes drawn to the canvas have colored smudgy trails. In other words, it takes stuff drawn to the canvas a little while before it disappears again. I also enjoy dark blue backgrounds over just plain black, since it visually works with the sinusoidal color palette:

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
And that's a wrap! If you enjoyed this post share it or leave me a nice comment! If you have questions also leave me a comment! Otherwise come join me on Twitter for more updates!
