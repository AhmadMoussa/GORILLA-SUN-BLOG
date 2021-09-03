---
title: Making of&#58; Parasite
author: Ahmad Moussa
categories:
  - p5js
description: In this post I show you how to make my sketch parasite
thumbnail_path: https://gorillasun.de/assets/images/2021-08-25-Making-of-Gateway/gateway.gif
published: false
---

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-08-25-Making-of-Parasite/parasite.gif" alt="" /></span>

1. <a href='#diag'>Points along the Diagonal</a>
2. <a href='#wiggle'>Making the Points Wiggle</a>
3. <a href='#size'>Varying point Size</a>
4. <a href='#atten'>Attenuating the Movement</a>
5. <a href='#fade'>Fading Strokes in and out</a>
6. <a href='#offset'>Offsetting every other loop</a>
7. <a href='#aesthetic'>Aesthetic Touches</a>

<h2><a name='equi'></a>Points along the Diagonal</h2>
As always, let's start with some boilerplate stuff:

<pre class="javascript"><code>function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw(){
  background(220);
}
</code></pre>

Very basic stuff here, we're setting up the canvas to take as much space as it has available, and ensure that it is always square in shape by setting both it's width and height to the smaller one of the two by using the min() function.

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;

  strokeWeight(4);
}

function draw(){
  background(220);

  for(x = off; x < w-off; x+=spc){
    point(x, x);
  }
}
</script>
<p></p>

Points along the diagonal can simply be drawn with a single for loop. The points' x and y coordinates will both be set to the value of x. Also as usual, we have an offset parameter 'off' and a spacing parameter 'spc' that we declare in the setup function to control how our for loop behaves. Try changing them and see how the drawn points appear!

<h2><a name='wiggle'></a>Making the points wiggle</h2>
Wiggling can generally be achieved with a sine wave, which we'll use to oscillate the position of the points:

<pre><code>t = frameCount/50;

for(x = off; x<w-off; x+=spc){
  offsetAmount = 10;
  point(
    x + offsetAmount*sin(t),
    x - offsetAmount*sin(t)
  )
}
</code></pre>

Doing it like this, you'll notice that all the points are moving side to side at the same time. However we'd like them to move in a serpent/worm like manner, we need to input something in addition to time into the sin() function to offset them differently. And we actually already have something that is unique to each point: it's position!

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){
    offsetAmount = 10;
    point(
      x + offsetAmount*sin(x + t),
      x - offsetAmount*sin(x + t)
    )
  }
}
</script>
<p></p>

Try plugging different things into the two sines and see how the behaviour changes.

<h2><a name='size'></a>Varying point Size</h2>
If you've read either one of my previous two tutorials this trick will be familiar to you. Here we'll increase the size of the points the closer they are to the center of the sketch:

<pre><code>d = dist(x,x,w/2,w/2)
dmap = map(d, 0, w/2+off/2, 1, 0)
strokeWeight(dmap*10)
</code></pre>

We'll make use of the inbuilt dist() function to compute the distance of each specific point towards the center, and then map from that range to a [0 - 1] range.

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){

    d = dist(x,x,w/2,w/2)
    dmap = map(d, 0, w/2+off/2, 1, 0)
    strokeWeight(dmap*10)

    offsetAmount = 10;
    point(
      x + offsetAmount*sin(x + t),
      x - offsetAmount*sin(x + t)
    )
  }
}
</script>
<p></p>

There's a little problem here though. We're not taking into account the distance of the point while it's wiggling side to side with our sin() function. We can fix this by using intermediary variables to store the location:

<pre><code>offsetAmount = 50;

x1 = x + offsetAmount * sin(x + t);
x2 = x - offsetAmount * sin(x + t);

d = dist(x1, x2, w / 2, w / 2);
dmap = map(d, 0, w / 2 + off / 2, 1, 0);
strokeWeight(dmap * 10);

point(x1, x2);
</code></pre>

Now we'll notice that the points' size varies ever so slightly while they pass back and forth over the center of the sketch:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){
    modifier = x*5
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 10);

    point(x1, x2);
  }
}
</script>
<p></p>

We also added a variable 'modifier' to easily control the oscillating motion of the points.

<h2><a name='atten'></a>Attenuating the movement</h2>
We can also repurpose the distance from the center to control the amount of movement that a single point will make. The closer to the center, the further out it will swing. The further from the center, the less distance it'll travel side to side.

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){
    modifier = x*5
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);
  }
}
</script>
<p></p>

We simply recalculate x1 and x2 but also multiply by the dmap parameter. Since it is in the range of [0 - 1], if it closer to the center the value will be closer to 1 and we're oscillating the point near it's max offset amount. If it's closer to 0, we're moving the point side to side only at a fraction of it's max range.

<h2><a name='joints'></a>Positioning Wireframe Joints</h2>
Calling it a wireframe is a generous description. Since we're essentially still in 2D this part of the sketch will simply give the illusion of being 3D.

For now we'll simply position some dots equidistantly on a circle that spans around each of the dots that we've already positioned so far:

<pre><code>rad = 40*dmap;
strokeWeight(5*dmap)

for(a = 0 ; a< TAU; a+=TAU/3){
  px = x1 + rad*cos(a);
  py = x2 + rad*sin(a);

  point(px,py)
}
</code></pre>

We're also going to make these points spin in the following manner:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){
    modifier = x*5
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);

    rad = 40*dmap;
    strokeWeight(5*dmap)
    for(a = t; a<TAU + t; a+=TAU/3){
      px = x1 + rad*cos(a);
      py = x2 + rad*sin(a);

      point(px,py)
    }
  }
}
</script>
<p></p>

Neat right? We already have some very interesting motion going on!

<h2><a name='connections'></a>Drawing the wireframe connections</h2>
First we'll draw the lines that connect the rotating points:

<pre><code>
rad = 40*dmap;
for(a = t; a< TAU+t; a+=TAU/3){
  strokeWeight(5*dmap)
  px = x1 + rad*cos(a);
  py = x2 + rad*sin(a);

  point(px,py)

  prevx = x1 + rad*cos(a+TAU/3);
  prevy = x2 + rad*sin(a+TAU/3);

  strokeWeight(1)
  line(px,py,prevx,prevy)
}
</code></pre>

We can simply do so by offsetting the angle and drawing a line between these two points. More neatly we can write it like this:

<pre><code>
rad = 40*dmap;
div = TAU/3
for(a = t; a< TAU+t; a+=div){
  strokeWeight(5*dmap)
  px = x1 + rad*cos(a);
  py = x2 + rad*sin(a);

  point(px,py)

  prevx = x1 + rad*cos(a+div);
  prevy = x2 + rad*sin(a+div);

  strokeWeight(1)
  line(px,py,prevx,prevy)
}
</code></pre>

Try changing the div parameter in the following code and see what shapes you can get!

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4)
}

function draw(){
  background(220);

  t = frameCount/50;

  for(x = off; x<w-off; x+=spc){
    modifier = x*5
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);

    rad = 40*dmap;
    div = TAU/4;
    for(a = t; a< TAU+t; a+=div){
      strokeWeight(5*dmap)
      px = x1 + rad*cos(a);
      py = x2 + rad*sin(a);

      point(px,py)

      prevx = x1 + rad*cos(a+div);
      prevy = x2 + rad*sin(a+div);

      strokeWeight(1)
      line(px,py,prevx,prevy)
    }
  }
}
</script>
<p></p>

Next up will be connecting joints in between different segments, for which we'll need to remember the position of the previously drawn point:
<pre><code>rad = 40 * dmap;
div = TAU / 4;
for (a = t; a < TAU + t; a += div) {
  strokeWeight(5 * dmap);
  px = x1 + rad * cos(a);
  py = x2 + rad * sin(a);

  point(px, py);

  prevx = x1 + rad * cos(a + div);
  prevy = x2 + rad * sin(a + div);

  strokeWeight(1);
  line(px, py, prevx, prevy);

  connectx = prevx1 + rad * cos(a);
  connecty = prevx2 + rad * sin(a);

  line(px,py,connectx, connecty)
}

prevx1 = x1;
prevx2 = x2
</code></pre>

You can see that at this point we're already starting to run out of meaningful names for our variables. The struggles of the creative coder. Either way, doin it like this we'll notice a number of problems. There is an ugly segment drawn straight across the sketch, and the lines don't allign cleanly with the previous joints:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4);
}


prevx1 = 0;
prevx2 = 0;
function draw() {
  background(220);

  t = frameCount / 50;

  for (x = off; x < w - off; x += spc) {
    modifier = x * 5;
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);

    rad = 40 * dmap;
    div = TAU / 3;
    for (a = t; a < TAU + t; a += div) {
      strokeWeight(5 * dmap);
      px = x1 + rad * cos(a);
      py = x2 + rad * sin(a);

      point(px, py);

      prevx = x1 + rad * cos(a + div);
      prevy = x2 + rad * sin(a + div);

      strokeWeight(1);
      line(px, py, prevx, prevy);

      connectx = prevx1 + rad * cos(a);
      connecty = prevx2 + rad * sin(a);

      line(px,py,connectx, connecty)
    }

    prevx1 = x1;
    prevx2 = x2
  }
}
</script>
<p></p>

The ugly line can simply be fixed with the addition of a counter that checks if we're in the first iteration of the loop or not. Or alternatively we can check if x is equal to to 'off', that would mean that we're still in the first iteration of the loop. In that case we skip drawing the line.

Additionally, we need to remember the previous radius, to make the lines connect to the joints exactly. This can be remedied by an additional variable that stores the radius:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4);
}

prevx1 = 0;
prevx2 = 0;
prevrad = 0;
function draw() {
  background(220);

  t = frameCount / 50;
  for (x = off; x < w - off; x += spc) {
    modifier = x * 5;
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);

    rad = 40 * dmap;
    div = TAU / 3;
    for (a = t; a < TAU + t; a += div) {
      strokeWeight(5 * dmap);
      px = x1 + rad * cos(a);
      py = x2 + rad * sin(a);

      point(px, py);

      prevx = x1 + rad * cos(a + div);
      prevy = x2 + rad * sin(a + div);

      strokeWeight(1);
      line(px, py, prevx, prevy);

      connectx = prevx1 + prevrad * cos(a);
      connecty = prevx2 + prevrad * sin(a);

      if(x != off){
        line(px,py,connectx, connecty)
      }
    }

    prevx1 = x1;
    prevx2 = x2;
    prevrad = rad;
  }
}

</script>
<p></p>

<h2><a name='final'></a>Final Touches</h2>

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  off = 50;
  spc = 15;
  strokeWeight(4);
}

prevx1 = 0;
prevx2 = 0;
prevrad = 0;
function draw() {
  background(255,0,0,80);

  t = frameCount / 50;
  for (x = off; x < w - off; x += spc) {
    modifier = x * 5;
    offsetAmount = 50;

    x1 = x + offsetAmount * sin(modifier + t);
    x2 = x - offsetAmount * sin(modifier + t);

    d = dist(x1, x2, w / 2, w / 2);
    dmap = map(d, 0, w / 2 + off / 2, 1, 0);
    strokeWeight(dmap * 30);

    x1 = x + offsetAmount * sin(modifier + t) * dmap;
    x2 = x - offsetAmount * sin(modifier + t) * dmap;

    point(x1, x2);

    rad = 40 * dmap;
    div = TAU / 4;
    for (a = t; a < TAU + t; a += div) {
      strokeWeight(5 * dmap);
      px = x1 + rad * cos(a);
      py = x2 + rad * sin(a);

      point(px, py);

      prevx = x1 + rad * cos(a + div);
      prevy = x2 + rad * sin(a + div);

      strokeWeight(1);
      line(px, py, prevx, prevy);

      connectx = prevx1 + prevrad * cos(a-div);
      connecty = prevx2 + prevrad * sin(a-div);

      if(x != off){
        line(px,py,connectx, connecty)
      }
    }

    prevx1 = x1;
    prevx2 = x2;
    prevrad = rad;
  }
}
</script>

<p></p>