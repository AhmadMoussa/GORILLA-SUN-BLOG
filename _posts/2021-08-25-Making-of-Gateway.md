---
title: Making of: Gateway - Recreating a beesandbombs Sketch
author: Ahmad Moussa
categories:
  - p5js
description: recreating beesandbombs' sketch with my own twist
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

This sketch is based on a sketch from Dave Whyte aka beesandbombs, that I discovered through the concinnus Twitter bot. Aesthetically the sketch is very simple, but geometrically there's a lot going on. After pondering for a while I found that recreating it was quite tricky. But here's my attempt at it:

1. <a href='#grid'>Creating a Grid</a>
2. <a href='#alternate'>Alternating Points and Circles</a>
3. <a href='#animate'>Animating the Grid</a>
4. <a href='#distance'>Distance Fields</a>
5. <a href='#color'>Adding Color</a>
6. <a href='#transparency'>Background Transparency Trick</a>

<h2>Drawing equidistant points on a circle</h2>
The best way to describe it would be infinitely expanding hexagonal patterns that fade into existence and then disapear after their lifespan expires.

The first thing we'll start with is creating a set of points that seemingly appear in the center of the sketch, and then disappear at some distance from the center. And then re-appear and disappear again and again.

As always, let's start with some boiler plate stuff:
<pre><code>
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
  strokeWeight(4)
}

function draw(){
  background(220);
  translate(w/2,w/2)
}

</code></pre>

Here we're just setting up our canvas in the setup function, to make it fill the size it has available, and in the draw function we're making use of the translate function. This will basically shift the origin of the sketch towards the center of the canvas. So basically, the (0,0) coordinate is now located at the center of the canvas, which will make dealing with coordinates a little easier along the way.

The next step will be drawing 6 points, that are located equidistantly on a circle. Basically we'll draw the corners of a hexagon:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
  strokeWeight(8)
}

let div = 6
let radius = 100
function draw(){
  background(220);
  translate(w/2,w/2)
  for(a = 0; a<TAU; a+=TAU/div){

    x = radius*cos(a)
    y = radius*sin(a)

    point(x,y)
  }
}
</script>
<p></p>

Pretty straight forward so far. Notice how I wrote the for loop that draws the points. Changing the div parameter allows us to draw a different number of points, that are always placed equidistantly from each other. Try it!

<h2>Looping points</h2>

Now for the looping aspect of these points. We'll have to gradually increase the radius of the drawn points until a certain limit, and then make them jump back towards the origin and repeat. We'll need a couple of things for that:

<pre><code>
let rMax = 150
let completionPercentage = 0
let rate = 0.003
</code></pre>

A maximum radius 'rMax', a variable that tracks how far along the way we already are, and a rate that specifies how fast we are moving. Then we'll actually have to increment and reset the completion percentage:

<pre><code>
completionPercentage += rate
if(completionPercentage > 1){
  completionPercentage = 0
}

radius = map(completionPercentage, 0, 1, 0, rMax);
</code></pre>

We can achieve a looping behaviour with the above logic. The variable completionPercentage will always loop from 0 to 1, where we set it's speed with the rate variable. To actually get the corresponding radius, we'll map that [0 - 1] range to the [0 - rMax] range. That way we can simply change the rMax variable if we want our maximum radius to be larger. And it practice this would look as follows:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(8);
}

let div = 6;
let radius = 100;

let rMax = 150;

let completionPercentage = 0;
let rate = 0.003;
function draw() {
  background(220);
  translate(w / 2, w / 2);
  for (a = 0; a < TAU; a += TAU / div) {
    completionPercentage += rate;
    if (completionPercentage > 1) {
      completionPercentage = 0;
    }

    radius = map(completionPercentage, 0, 1, 0, rMax);

    x = radius * cos(a);
    y = radius * sin(a);

    point(x, y);
  }
}
</script>

Try changing the rMax and rate variables!

<h2>Multiple sets of looping points</h2>
Next, we'll want to have multiple set of points looping, that are differently offset from each other. We'll need to wrap the code we've written so far in another loop, and make some modifications. I'm not certain if beesandbombs does it like that (they probably don't), but I found that making use of some extra memory makes this pretty easy. We'll create an array in the setup function that'll hold the offset of the points:

<pre><code>
completionPercentages = []
for(n = 0; n<N; n++){
   rateOffset = map(n,0,N,0,1)
   completionPercentages.push(rateOffset)
}
</code></pre>

Plugging this into our setup function, we'll essentially offset the points before they actually ever get drawn. In this manner our points will also always be equidistantly offset from each other, and we can control how many sets of points we want by setting the global variable N. The entire code would then look like this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(8);

  completionPercentages = []
  for(n = 0; n<N; n++){
   rateOffset = map(n,0,N,0,1)
   completionPercentages.push(rateOffset)
  }
}

let N = 2

let div = 6;
let radius = 100;

let rMax = 150;

let completionPercentage = 0;
let rate = 0.0015;
function draw() {
  background(220);
  translate(w / 2, w / 2);

  for(n = 0; n<N; n++){


    for (a = 0; a < TAU; a += TAU / div) {
      completionPercentages[n] += rate;
      if (completionPercentages[n] > 1) {
        completionPercentages[n] = 0;
      }

      radius = map(completionPercentages[n], 0, 1, 0, rMax);

      x = radius * cos(a);
      y = radius * sin(a);

      point(x, y);
    }
  }
}
</script>

We're kind of halfway there already. Well not really, but for the impatient amongst you, we are :).

<h2>Drawing Pseudo Hexagons</h2>
It would be quite easy to simply draw expanding hexagonal shapes now, but since there are gaps in between we'll have to think of something else. To achieve the lines I used vectors! We'll want to draw two lines that are oriented towards the two adjacent corners/points in the hexagon:

<pre><code>
//vector that defines the position of the point to the right
vRight = createVector(
         radius * cos(a + TAU/div),
         radius * sin(a + TAU/div)
      );

// The angle from the current point to the right point  
// we do this with the atan2 function    
angleRight = atan2(vRight.x-x, vRight.y-y);

// length of the line drawn, we'll change this later      
strokeLength = 20

// the strokelength determines how far along the line towards the next point we are
// basically scaling the distance
vecRight = createVector(x+strokeLength*sin(angleRight),
                        y+strokeLength*cos(angleRight))

// And finally draw the line
line(x, y, vecRight.x, vecRight.y);
</code></pre>

I hope the comments make sense. We also have to repeat these steps for the left point. We basically find a vector that is oriented towards the two points that are right and left from the current point, and draw lines oriented towards them.

The atan2() function allows us to find the angle between two sets of points, which we'll need to correctly slant the drawn lines.

Try going trough it step by step, it'll make more sense that way. The code would then look like this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  completionPercentages = []
  for(n = 0; n<N; n++){
   rateOffset = map(n,0,N,0,1)
   completionPercentages.push(rateOffset)
  }
}

let N = 3

let div = 6;
let radius = 100;

let rMax = 150;

let completionPercentage = 0;
let rate = 0.0005;
function draw() {
  background(220);
  translate(w / 2, w / 2);

  for(n = 0; n<N; n++){


    for (a = 0; a < TAU; a += TAU / div) {
      completionPercentages[n] += rate;
      if (completionPercentages[n] > 1) {
        completionPercentages[n] = 0;
      }

      radius = map(completionPercentages[n], 0, 1, 0, rMax);

      x = radius * cos(a);
      y = radius * sin(a);

      point(x, y);

      vRight = createVector(
         radius * cos(a + TAU/div),
         radius * sin(a + TAU/div)
      );

      angleRight = atan2(vRight.x-x, vRight.y-y);

      strokeLength = 20
      vecRight = createVector(x+strokeLength*sin(angleRight),
                              y+strokeLength*cos(angleRight))

      line(x, y, vecRight.x, vecRight.y);

      vLeft = createVector(
         radius * cos(a - TAU/div),
         radius * sin(a - TAU/div)
      );

      angleLeft = atan2(vLeft.x-x, vLeft.y-y);

      strokeLength = 20
      vecLeft = createVector(x+strokeLength*sin(angleLeft),
                              y+strokeLength*cos(angleLeft))

      line(x, y, vecLeft.x, vecLeft.y);
    }
  }
}
</script>

Doesn't look very pretty yet, but we're basically 90% done at this point. The hardest part is behind us.

<h2>Fading the lines</h2>

Yet again, we recurr to one of my favorite tricks: the Distance to the center! We'll essentially want to shrink the lines, the further they are from the center. Very simply we can do this with the inbuilt dist function:

<pre><code>
d = dist(x,y,0,0)
dWeight = map(d, 0, rMax, 5, 0)
strokeWeight(dWeight)
</code></pre>

We'll add this little snippet before we draw the points and lines, and we'll get smoothly fading lines! But you'll notice that we also don't want them to immediately pop out of nothingness in the center. We'll have to add another short if statement to remedy that:

<pre><code>
maxStrokeWeight = 5

d = dist(x,y,0,0)
dWeight = map(d, 0, rMax, maxStrokeWeight, 0)
strokeWeight(dWeight)

minDist = 10
if(d < minDist){
    dWeight = map(d, 0, minDist, 0, maxStrokeWeight)
    strokeWeight(dWeight)
}
</code></pre>

We'll set an arbitrary distance, in which the strokeWeight will be grown from 0 until it reaches it's maximum and then shrink again. We'll make heavy use of the map function again:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  completionPercentages = []
  for(n = 0; n<N; n++){
   rateOffset = map(n,0,N,0,1)
   completionPercentages.push(rateOffset)
  }
}

let N = 3

let div = 6;
let radius = 100;

let rMax = 150;

let completionPercentage = 0;
let rate = 0.0005;
function draw() {
  background(220);
  translate(w / 2, w / 2);

  for(n = 0; n<N; n++){


    for (a = 0; a < TAU; a += TAU / div) {
      completionPercentages[n] += rate;
      if (completionPercentages[n] > 1) {
        completionPercentages[n] = 0;
      }

      radius = map(completionPercentages[n], 0, 1, 0, rMax);

      x = radius * cos(a);
      y = radius * sin(a);

      d = dist(x,y,0,0)
      dWeight = map(d, 0, rMax, 5, 0)
      strokeWeight(dWeight)

      if(d < 10){
        dWeight = map(d, 0, 10, 0, 5)
        strokeWeight(dWeight)
      }

      point(x, y);

      vRight = createVector(
         radius * cos(a + TAU/div),
         radius * sin(a + TAU/div)
      );

      angleRight = atan2(vRight.x-x, vRight.y-y);

      strokeLength = 20
      vecRight = createVector(x+strokeLength*sin(angleRight),
                              y+strokeLength*cos(angleRight))

      line(x, y, vecRight.x, vecRight.y);

      vLeft = createVector(
         radius * cos(a - TAU/div),
         radius * sin(a - TAU/div)
      );

      angleLeft = atan2(vLeft.x-x, vLeft.y-y);

      strokeLength = 20
      vecLeft = createVector(x+strokeLength*sin(angleLeft),
                              y+strokeLength*cos(angleLeft))

      line(x, y, vecLeft.x, vecLeft.y);
    }
  }
}
</script>
<p></p>

Finally, we want to repeat the same steps for the strokeLength of the drawn lines! We can use the same code for the length of the strokes:

<pre><code>maxStrokeWeight = 5;
maxStrokeLength = 10

d = dist(x, y, 0, 0);
dWeight = map(d, 0, rMax, 1, 0);
strokeWeight(dWeight*maxStrokeWeight);
strokeLength = dWeight*maxStrokeLength


minDist = 10;
if (d < minDist) {
  dWeight = map(d, 0, minDist, 0, 1);
  strokeWeight(dWeight*maxStrokeWeight);
  strokeLength = dWeight*maxStrokeLength
}
</code></pre>

And the entire code would look something like this:
<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  strokeWeight(4);

  completionPercentages = [];
  for (n = 0; n < N; n++) {
    rateOffset = map(n, 0, N, 0, 1);
    completionPercentages.push(rateOffset);
  }
}

let N = 3;

let div = 6;
let radius = 100;

let rMax = 150;

let completionPercentage = 0;
let rate = 0.0005;
function draw() {
  background(220);
  translate(w / 2, w / 2);

  for (n = 0; n < N; n++) {
    for (a = 0; a < TAU; a += TAU / div) {
      completionPercentages[n] += rate;
      if (completionPercentages[n] > 1) {
        completionPercentages[n] = 0;
      }

      radius = map(completionPercentages[n], 0, 1, 0, rMax);

      x = radius * cos(a);
      y = radius * sin(a);

      maxStrokeWeight = 5;
      maxStrokeLength = 10

      d = dist(x, y, 0, 0);
      dWeight = map(d, 0, rMax, 1, 0);
      strokeWeight(dWeight*maxStrokeWeight);
      strokeLength = dWeight*maxStrokeLength


      minDist = 10;
      if (d < minDist) {
        dWeight = map(d, 0, minDist, 0, 1);
        strokeWeight(dWeight*maxStrokeWeight);
        strokeLength = dWeight*maxStrokeLength
      }




      point(x, y);

      vRight = createVector(
        radius * cos(a + TAU / div),
        radius * sin(a + TAU / div)
      );

      angleRight = atan2(vRight.x - x, vRight.y - y);

      vecRight = createVector(
        x + strokeLength * sin(angleRight),
        y + strokeLength * cos(angleRight)
      );

      line(x, y, vecRight.x, vecRight.y);

      vLeft = createVector(
        radius * cos(a - TAU / div),
        radius * sin(a - TAU / div)
      );

      angleLeft = atan2(vLeft.x - x, vLeft.y - y);

      vecLeft = createVector(
        x + strokeLength * sin(angleLeft),
        y + strokeLength * cos(angleLeft)
      );

      line(x, y, vecLeft.x, vecLeft.y);
    }
  }
}


</script>
