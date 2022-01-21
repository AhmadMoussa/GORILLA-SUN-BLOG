---
title: An algorithm for polygons with rounded corners
author: Ahmad Moussa
categories:
  - p5js
description: this blog post describes an approach to turn pointy polygons into smooth shapes with round corners
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
exclude_rss: true
---

<h2>Shapes with rounded corners</h2>

If you spent some time doing creative coding, you'll very quickly come to the realization that anything, which has shape that is little more complicated than your average rectangle or circle, quickly starts requiring a fair amount of code to be summoned onto your canvas.

This post/tutorial is dedicated to a snippet of code I found on stackoverflow, and that has unlocked many shapes and sketches for me, that I otherwise wouldn't have been able to make. In essence the algorithm allows you to create arbitrarily shaped polygons, with any number of vertices while at the same time being able to control the roundness (or curvature) of each vertex.

I came across this code when I attempted a sketch in which I wanted to round off the corners of some triangles.

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/1.png" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/2.png" alt="">
		</span>
	</div>
</div>

In p5js there is no out-of-the-box method to doing so, as opposed to rectangular shapes where the 5th to 8th parameters can be used for that purpose. You could technically do it with a series of curveVertex() calls, however that strategy doesn't offer much control. The next two sketches, that I made during #GENUARY2022, make use of this algorithm:

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/SolLeWitt.png" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/Sand.png" alt="">
		</span>
	</div>
</div>

You can notice that you can even mix between pointy corners and round corners, which is another thing that isn't possible with pure p5js (without writing a lot of additional code towards that end). This came in clutch for two Genuary sketches

The remainder of this post will walk you through this really cool and useful algorithm, step by step.

<h2>Credit where credit's due</h2>
The code discussed in this post stems from this <a href="https://stackoverflow.com/a/44856925">stackoverflow answer</a> by SO user <a href="https://stackoverflow.com/users/3877726/blindman67">Blindman67</a>. (Go give him some upvotes on his answer XD) Blindman67 explains his strategy in detail in his answer, and is more than sufficient for you to start using his code, but for the sake of REALLY understanding the algorithm, we'll recreate it from scratch solely based off of his explanation.




<h2>An algorithm for rounded corners</h2>
A corner, which essentially is an angle, can generally be defined by three points. And since we'll be drawing our shapes through the positioning of vertices at specific coordinates, we can be certain that we have these coordinates (a given).

The idea behind what we're trying to do is simple. We're going to take an imaginary circle of a certain radius, and push it as far as possible into the corner that we're trying to round. Now we erase the lines that form the pointy corner, starting from the place where they are tangential to the imaginary circle, and close the shape again by tracing the outwards facing portion of our circle.

And voila, we have a rounded corner with a specific radius. Seems straightforward? Right, but there are some caveats and some calculations to be done.

<h2>Breaking down the algorithm</h2>
Given a specific radius, the difficulty lies within finding where to exactly position the circle that rounds the corner to that radius. If we can exactly locate where this circle is positioned, we can also determine where it is tangential to the lines that form the pointy corner. Those two points will determine the start and end of the arc that will form the rounded corner.

Overall, given three points A, B and C that form a corner, as well as a given radius, the steps are:

1 - Calculate the angle that is formed by A, B and C by computing the cross product of the vectors BA and BC
2 - Calculating the dot product of the vectors BA and the line at 90 degrees from BA
3 - Finding the distance from B to F by solving it with R and the angle FBD or FBE
4 - Draw an arc from E to F (or the other way around) by tracing a portion of the circle

There are other cases that need to be handled, but in essence these are the main steps.

<h2>2 Vectors from 3 Points</h2>
First we'll need to find the angle that is formed by three points. To do so we first need to compute the vectors formed by these three points. For three points A,B and C we can find the vectors BA and BC by using the following function:

<pre><code>
// p1 -> first point
// p2 -> second point
// v -> container that we will fill
// we could also not pass v and simply return it
function toVec(p1, p2, v) {

    // center the vector based on the coordinates of B
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;

    // compute length of vector
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);

    // normalize based on length
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;

    // calculate the angle
    v.ang = Math.atan2(v.ny, v.nx);
}
</code></pre>

The comments should be sufficient to understand what is happening, we also use the atan2() function to calculate the angle of the vectors with respect to the zero angle. Here's a quick working example:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);

  // make origin at center of canvas
  translate(w/2,w/2)

  p1 = {x: 100, y: 0}
  p2 = {x: 0, y: 0}
  p3 = {x: 0, y: -100}

  strokeWeight(5)
  point(p1.x,p1.y)
  point(p2.x,p2.y)
  point(p3.x,p3.y)

  v1 = {}
  v2 = {}

  toVec(p1,p2,v1)
  toVec(p3,p2,v2)

  noFill()
  strokeWeight(1)
  arc(0,0,v1.len*2,v1.len*2,v1.ang+PI,v2.ang+PI)

  noLoop()
}

// p1 -> first point
// p2 -> second point
// v -> container that we will fill
function toVec(p1, p2, v) {
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
}
</script>
<p></p>

Now that we have converted our 3 points into vector form, we can compute the intermediary angle between the two vectors!

<h2>Finding the angle using the cross product</h2>

Getting the halfway angle is probably the trickiest part of the procedure. It is important to mention here that the order of the points is crucial! When dealing with a closed polygonal shape the order of the points is such that the formed angle is pointing inwards. Given 3 points the formed angle is ambiguous, in the previous example the angle forms an acute wedge but could equivalently be interpreted as an obtuse fan. Keep this in mind for now, it will be relevant in a bit!

The next part of blindman67's code was a little tricky to decipher, and will require us to freshen up on our linear algebra a little bit, specifically the cross product of two vectors. Essentially, we will be exploiting the cross product of the two vectors to find the value of the angle between them.

What the cross product actually represents algebraically, is a bit outside of the scope of this post. A perfect resource for understanding it can be found in the form of <a href="https://www.youtube.com/watch?v=eu6i7WJeinw&ab_channel=3Blue1Brown">this video</a> by <a href="https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw">3Blue1Brown</a> (and I think there is no need for me to introduce the channel XD).

The important part here is how the cross product can help us find the angle between two vectors. Generally the formula for finding the cross product is the multiplying the magnitudes of the two vectors at hand with the sine of the angle that they form. More concretely:


<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Vert BA \Vert * \Vert BC \Vert * sin( \Theta ) \) </p>
</div>


This means that, finding the cross product requires us to have the angle... which is the thing that we're trying to find. In this sense, we haven't made any progress on finding the angle between the two vectors. However, if we were to somehow already have the numerical value of the cross product, we could solve for \( sin(\Theta) ), since we also have the magnitudes of the two vectors concerned:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Vert BA \Vert * \Vert BC \Vert * sin( \Theta ) \) </p>
</div>

The cross product of two vectors is actually equal to the determinant of the 2x2 matrix formed by these vectors. This means computing this determinant will allow us to find the angle! Computing the determinant of a matrix is done with the following formula:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( v_1x * v_2y - v_2x * v_1y \) </p>
</div>

Then the value of the angle can be calculate as follows:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Theta = sin^(-1)(\frac{(v_1x * v_2y - v_2x * v_1y)}{\Vert BA \Vert * \Vert BC \Vert} \) </p>
</div>

Additionally, this can be further simplified, since our vectors are already normalized \( \Vert BA \Vert * \Vert BC \Vert \), they will simply evaluate to 1, leaving us with:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( sin^(-1) = (det) \)</p>
</div>

Let's have a look at the following lines of code by Blindman67:

<pre><code>
// compute vectors
asVec(p2, p1, v1);
asVec(p2, p3, v2);

// Cross product of the two vectors
sinA = v1.nx * v2.ny - v1.ny * v2.nx;

// Cross product of v2 and the line at 90 degrees to v1
// This will help determine the orientation of the angle
sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;

// Clamping the value of sinA to avoid NaNs
angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
</code></pre>

The trickiest part here is the double ternary check inside the asin function. By simply expanding it, it visually makes more sense:

<pre><code>
if(sinA<-1){
  angle = Math.asin(-1)
}else if(sinA>1){
  angle = Math.asin(1)
}else{
  angle = Math.asin(sinA)
}
</code></pre>

Go ahead a try putting values lesser than -1 or greater than 1 into the inverse sine function. It'll yield a NaN value. To avoid such thing, we simply clamp the value and can be done compactly as a one liner. And if you noticed, in actuality, we have already obtained the value of the angle!

<h2>Finding the correct orientation of the angle</h2>

Next up we have this if/else flurry to determine the orientation of our angle:

<pre><code>radDirection = 1;
drawDirection = false;
if (sinA90 < 0) {
  if (angle < 0) {
    angle = Math.PI + angle;
  } else {
    angle = Math.PI - angle;
    radDirection = -1;
    drawDirection = true;
  }
} else {
  if (angle > 0) {
    radDirection = -1;
    drawDirection = true;
  }
}
</pre></code>

There's quite a lot going on here. We'll go through it step by step. The important thing to grasp here is that the sign of the cross product also tells us if the angle is oriented clockwise or counter clockwise, which we'll use to find the half angle that splits our angle in two. And remember, order is important!

Keeping in mind that sinA90 is the angle formed the perpendicular (to v1) and v2 (in that order), and sinA is the angle formed by v1 and v2 (in that order).

If the value of sinA90 is less than 0 then this means that the angle sinA90 has a counter-clockwise orientation. the opposite is also true. In essence there's 4 different possibilities depending on how the vectors v1 and v2 are situated with respect to each other. Have a look at the badly drawn schematic:

If sinA90 > 0, then the angle formed by the perpendicular (to v1) and v2 is oriented clockwise. In this case there is no need to do anything, and we can simply halve the value of angle to get the correct angle.

If sinA90 is < 0, then this angle is oriented counterclockwise. In this case we must distinguish between v1 being before or past v2. If the angle is less than 0 then v1 is past v2, and we have to add PI to the angle formed, else we need to subtract the angle from PI. Halving this value then yields the correct mid-way angle.

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);

  // make origin at center of canvas
  translate(w/2,w/2)

  randAng = random(TAU)
  p1 = {x: 100*cos(randAng), y: 100*sin(randAng)}
  p2 = {x: 0, y: 0}
  p3 = {x: -100, y: 100}

  strokeWeight(10)
  point(p1.x,p1.y)
  point(p2.x,p2.y)
  point(p3.x,p3.y)

  text('A',p1.x+5,p1.y)
  text('B',p2.x+5,p2.y)
  text('C',p3.x+5,p3.y)

  strokeWeight(1)
  line(p1.x,p1.y,p2.x,p2.y)
  line(p3.x,p3.y,p2.x,p2.y)

  v1 = {}
  v2 = {}

  toVec(p2,p1,v1)
  toVec(p2,p3,v2)

  //coordinates of perpendicular vector
  perpx1 = v1.len*cos(v1.ang-PI/2)
  perpy1 = v1.len*sin(v1.ang-PI/2)

  perpx2 = v1.len*cos(v1.ang+PI/2)
  perpy2 = v1.len*sin(v1.ang+PI/2)

  stroke(255,0,0)
  strokeWeight(10)
  point(perpx1,perpy1)
  point(perpx2,perpy2)
  strokeWeight(1)
  drawingContext.setLineDash([5,5])
  line(perpx1,perpy1,perpx2,perpy2)

  sinA = v1.nx * v2.ny - v1.ny * v2.nx;
  sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
  angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
  angle90 = Math.asin(sinA90 < -1 ? -1 : sinA90 > 1 ? 1 : sinA90);

  print(sinA,sinA90,angle,angle90)

  noFill();

  if (sinA90 < 0) {
    if (angle < 0) {
      angle = Math.PI + angle;
    } else {
      angle = Math.PI - angle;
    }
  }
  angle=angle
  xx = 50*cos(v1.ang+angle/2)
  yy = 50*sin(v1.ang+angle/2)

  strokeWeight(10)
  point(xx,yy)

  noLoop()
}

// p1 -> first point
// p2 -> second point
// v -> container that we will fill
function toVec(p1, p2, v) {
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
}
</script>
<p></p>

However this is still not entirely correct.

<h2>Solving a triangle given two angles and one Side</h2>

Now we can find the distance of the center of our circle from the corner:

<pre><code>lenOut = abs(cos(halfAngle) * radius / sin(halfAngle));
</code></pre>

One problem here is that the radius that we specified as input, might not fit into the corner. This can be remedied by the following check:

<pre><code>
if (lenOut > Math.min(v1.len / 2, v2.len / 2)) {
  lenOut = Math.min(v1.len / 2, v2.len / 2);
  cRadius = Math.abs(lenOut * Math.sin(halfAngle) / Math.cos(halfAngle));
} else {
  cRadius = radius;
}
</code></pre>

<h2></h2>

<pre><code>
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);

  // make origin at center of canvas
  translate(w/2,w/2)

  randAng1 = random(TAU)
  randAng2 = random(TAU)
  p1 = {x: 100*cos(randAng1), y: 100*sin(randAng1)}
  p2 = {x: 0, y: 0}
  p3 = {x: 100*cos(randAng2), y: 100*sin(randAng2)}

  strokeWeight(10)
  point(p1.x,p1.y)
  point(p2.x,p2.y)
  point(p3.x,p3.y)

  text('A',p1.x+5,p1.y)
  text('B',p2.x+5,p2.y)
  text('C',p3.x+5,p3.y)

  strokeWeight(1)
  line(p1.x,p1.y,p2.x,p2.y)
  line(p3.x,p3.y,p2.x,p2.y)

  v1 = {}
  v2 = {}

  toVec(p2,p1,v1)
  toVec(p2,p3,v2)

  //coordinates of perpendicular vector
  perpx1 = v1.len*cos(v1.ang-PI/2)
  perpy1 = v1.len*sin(v1.ang-PI/2)

  perpx2 = v1.len*cos(v1.ang+PI/2)
  perpy2 = v1.len*sin(v1.ang+PI/2)

  stroke(255,0,0)
  strokeWeight(10)
  point(perpx1,perpy1)
  point(perpx2,perpy2)
  strokeWeight(1)
  drawingContext.setLineDash([5,5])
  line(perpx1,perpy1,perpx2,perpy2)

  sinA = v1.nx * v2.ny - v1.ny * v2.nx;
  sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
  angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
  angle90 = Math.asin(sinA90 < -1 ? -1 : sinA90 > 1 ? 1 : sinA90);

  print(sinA,sinA90,angle,angle90)

  noFill();
  radius = 30
  radiusAll = 10
  radDirection = 1;
    drawDirection = 0;
    if (sinA90 < 0) {
      if (angle < 0) {
        angle = Math.PI + angle;
      } else {
        angle = Math.PI - angle;
        radDirection = -1;
        drawDirection = 1;
      }
    } else {
      if (angle > 0) {
        radDirection = -1;
        drawDirection = 1;
      }
    }
  halfAngle = angle/2

  lenOut = Math.abs(Math.cos(halfAngle) * radius / Math.sin(halfAngle));

  print(lenOut)
  if (lenOut > Math.min(v1.len / 2, v2.len / 2)) {
    lenOut = Math.min(v1.len / 2, v2.len / 2);
    cRadius = Math.abs(lenOut * Math.sin(halfAngle) / Math.cos(halfAngle));
  } else {
    cRadius = radius;
  }

  xx = cRadius*cos(v1.ang+halfAngle)
  yy = cRadius*sin(v1.ang+halfAngle)

  strokeWeight(10)
  //point(xx,yy)

  // Part 4
  x = p2.x + v2.nx * lenOut;
  y = p2.y + v2.ny * lenOut;
  //-----------------------------------------
  // Part 5
  x += -v2.ny * cRadius * radDirection;
  y += v2.nx * cRadius * radDirection;

  strokeWeight(1)
  fill(255,0,0)
  arc(x, y, cRadius*2,cRadius*2,
      v1.ang + Math.PI / 2 * radDirection,
      v2.ang - Math.PI / 2 * radDirection);

  noLoop()
}

// p1 -> first point
// p2 -> second point
// v -> container that we will fill
function toVec(p1, p2, v) {
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
}
</code></pre>

<h2>Drawing the shape with the rendering context</h2>
