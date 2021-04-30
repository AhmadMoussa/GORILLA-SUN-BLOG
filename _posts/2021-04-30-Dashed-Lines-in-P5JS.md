---
title: Dashed Lines in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: Achieving dashed lines in P5JS was more difficult than I thought it would be, here's a tutorial on how to do it.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

Drawing a line in P5JS is very easy and can be done with a single line of code, into which you pass the coordinates of the starting point and the end point. 

<pre><code>
line(0,0,100,100)
</code></pre>

Drawing a dashed line in P5JS is also relatively easy, however requires a somewhat of a hack to be achieved.

<pre><code>

</code></pre>

Drawing custom dashed lines, from scratch that have an animated behaviour, requires a lot of code. What follows is my attempt at making animated dashed lines in P5JS, but is no way the optimal approach to doing so. Again we will heavily rely on Object Oriented programming by modeling the dahsed line as an Object and giving it behaviour through functions and parameters.

<h2>Drawing a line between two points</h2>
First order of business will be drawing a line manually between two sets of coordinates. This involves finding the angle that the line forms with the x axis, which can be done with the following formula:
<pre><code>
// slope of line going from point (x1,y1) to point (x2,y2)
slope = atan2(y2 - y1, x2 - x1)
</code></pre>

We will also require the length of the segment between the two points, the formula for that is:
<pre><code>
// length of line going from point (x1, y1) to point (x2, y2)
slope = this.L = sqrt(pow((x1 - x2), 2) + pow((y1 - y2), 2));
</code></pre>

The result given by atan2 defines the angle of inclination of our line, which we will have to rotate by that angle. Essentially, we will center a circle on the point (x1, y1) with a radius equal to the length of the line (which we just calculated) and use the angle with sin() and cos() to slant the line. If you haven't already done so, go and read my post on different types of rotation in P5JS here. 

So far we would have something that looks like this:
<pre><code>
class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));

    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
  }

  display() {
    line(this.x1, this.y1,
      this.x1 + this.L * cos(this.S),
      this.y1 + this.L * sin(this.S))
  }
}

function setup() {
  createCanvas(400, 400);
  sl = new SlopeLine(200, 200, 300, 200);
}

function draw() {
  background(220);
  sl.display();
}
</code></pre>

Not very impressive yet, because it's essentially just doing the same thing that you could do with the line() function. But I thought that it was cool to see what it takes to simply draw a line.

<h2>Dashed Style</h2>
Now we'll tackle the main problem, achieving the dashed line style. Similarly to drawingContext hack, we want to be able to have variable dash lengths and variable length dash gaps. We'll need to first figure out the overall length of the line.

Next we'll have to split up the line into segments of a specific length, with spaces in between, also of a specific length.
<pre><code>
this.numSegments = this.dist / (this.segmentLength + this.spacing);
</code></pre>

At this point we encounter our first problem, how do we split up a segment of fixed length into a number of sub-segments, whose combined length might not exactly add up to the total length of the entire segment?

In actuality, we have to consider two cases, the segment and space length either add up exactly n times to fill out the segment, or they simply don't. If they do add up, great!

If they don't, we'll have to write some code to handle that case. Let's first have a look at what it looks like without handling that case: 
<pre><code>

class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));

    // calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
    
    // calculate number of segments
    this.numS = this.L / (this.segmentLength+this.spaceLength)
    console.log(this.numS)
  }

  display() {
     for(let i = 0; i < this.numS; i++){
       console.log(i)
       line(
         this.x1 + (this.segmentLength + this.spaceLength)*i*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*i*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*(i+1)*cos(this.S)-this.spaceLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*(i+1)*sin(this.S)-this.spaceLength*sin(this.S),
           )
     }
  }
}

function setup() {
  createCanvas(400, 400);
  sl = new SlopeLine(100, 200, 300, 200, 18,15);
}

function draw() {
  background(220);
  stroke(0)
  strokeWeight(3);
  sl.display();
  
  // Additionally  we're gonna draw two red points to see where the line should start and end
  strokeWeight(5);
  stroke(255,0,0)
  point(100,200);
  point(300,200);
}
</code></pre>


1. Does it add up exactly if we discard the final trailing space?
2. If not, by how much do we need to truncate the final 'solid' segment?

add up we'll make it so that the number of segments and spaces always overshoots the length of the line. If they don't, we have to look at another set of cases:



