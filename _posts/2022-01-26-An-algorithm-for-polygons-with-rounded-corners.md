---
title: An algorithm for polygons with rounded corners
author: Ahmad Moussa
categories:
  - p5js
description: An approach for creating all sorts of different smooth shapes in p5, using the canvas rendering context.
thumbnail_path: https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/sand.mp4
published: true
exclude_rss: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/Sand.gif" alt="">
</span>
<p></p>

<h2>Quick Index</h2>

1. <a href='#start'>Introduction: An algorithm for rounded corners</a>
2. <a href='#credit'>Credit where credit's due</a>
3. <a href='#intuitive'>An intuitive explanation of the algorithm</a>
4. <a href='#breakdown'>Breakdown of the algorithm</a>
5. <a href='#vecstopoints'>Two Vectors from three Points</a>
6. <a href='#angle'>Calculating the angle using the cross product</a>
7. <a href='#ambiguity'>Ambiguity of the cross product</a>
8. <a href='#position'>Positioning the circle</a>
9. <a href='#ctx'>The rendering context</a>
10. <a href='#drawing'>Drawing the final shape</a>

<h2><a href='#further'>Further Improvements</a></h2>
1. <a href='#p5vec'>Blindman67's method using p5 vectors</a>
2. <a href='#dave'>Dave's acceleration method</a>
3. <a href='#bezier'>Dave's Bezier curve method</a>
		
<h2><a name='start'></a>Introduction: An algorithm for rounded corners</h2>

If you spent some time doing creative coding, you'll very quickly come to the realization that anything, which has a shape that is little more complicated than your average rectangle or circle, quickly starts requiring a fair amount of code to be summoned onto your canvas.

This post/tutorial was initially sparked by a snippet of code that I've found on stackoverflow. In the past months, this algorithm has unlocked a number shapes and sketches for me, that I otherwise wouldn't have been able to make. In essence, the algorithm allows you to create arbitrarily shaped polygons, with any number of vertices while at the same time being able to control the roundness (or curvature) of each vertex.

I came across this code when I attempted a sketch in which I wanted to round off the corners of some triangles:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/2.png" alt="">
		</span>
	</div>
</div>
<p></p>

In p5js there is no out-of-the-box method for doing so, except when it comes to rectangular shapes, where the 5th to 8th parameters can be used for that purpose. You could technically do it with a series of curveVertex() calls, however that strategy doesn't offer much control. Here's another sketch that I made during #GENUARY2022, which also makes use of the same algorithm, even if it might not be as obvious:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/9dunes.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/4dunes.png" alt="">
		</span>
	</div>
</div>
<p></p>

You can notice that you can even mix between pointy corners and round corners, which is another thing that isn't possible with pure p5js (without writing a lot of additional code towards that end). This definitely came in clutch this Genuary. The remainder of this post will walk you through this really cool and useful algorithm, step by step.

<h2><a name='credit'></a>Credit where credit's due</h2>
The code discussed in this post stems from this <a href="https://stackoverflow.com/a/44856925">stackoverflow answer</a> by stackoverflow user <a href="https://stackoverflow.com/users/3877726/blindman67">Blindman67</a>. (Go give him some upvotes on his answer if you find this useful!) Blindman67 explains his strategy succinctly, and is more than sufficient for you to start using his code. 

But for the sake of REALLY understanding the algorithm, we'll recreate it from scratch based off of his explanation and analyzing his code! There are a number of noteworthy things going on, that are worth inspecting in more detail, and can be generalized to a number of other scenarios.

<h2><a name='intuitive'></a>An intuitive explanation of the algorithm</h2>
We'll begin with an intuitive depiction of what the algorithm is accomplishing. The idea behind what we're trying to do is generally simple: We're going to take an imaginary circle of a certain radius, and push it as far as possible into a corner (any kind of corner, not necessarily 90 degrees) that we're trying to round. Next, we erase the lines that form the pointy corner, starting from the place where they are tangential to the imaginary circle, and close the shape again by tracing the outward facing portion of our circle.

And voila, we have obtained a rounded corner with a specific radius. Seems straightforward, right? However, to accomplish this there are a number of steps that we need to follow.

<h2><a name='breakdown'></a>Breakdown of the algorithm</h2>
Given a specific radius, the difficulty lies within finding where to exactly position the circle that rounds the corner to that radius. If we can exactly pinpoint where this circle is positioned, we can also determine where it is tangential to the lines that form the pointy corner. Those two points will determine the start and end of the arc that will form the rounded corner. Overall, given three points A, B and C that form a corner, as well as a given radius, the steps are:

<!--
1. Calculate the angle that is formed by A, B and C by computing the cross product of the vectors BA and BC
2. Calculating the cross product of the vectors BA and the line at 90 degrees from BA, to find the which side of the vector to place the circle
3. Finding the distance from B to F by solving it with R and the angle FBD or FBE
4. Drawing an arc from E to F (or the other way around) by tracing a portion of the circle
-->

1. Converting 3 points into 2 vectors
2. Calculating the angle between those two vectors
3. Calculating and locating the half-angle between the two vectors
4. Finding the distance from the circle center to the corner
5. Drawing the arc

<h2><a name='vecstopoints'></a>Two Vectors from three Points</h2>
First we'll need to find the value of the angle that is formed by three points. To do so we first need to turn these 3 points into two vectors. For three points A, B and C we can find the vectors BA and BC by using the following function:

<pre><code>// p1 -> first point
// p2 -> second point
// v -> container that we will fill
// we could also not pass v and simply return it
function toVec(p1, p2, v) {

    // center the vector based on the coordinates of p2 (the point B in this case)
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;

    // compute magnitude (length) of the vector
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);
    
    // alternatively and more concisely v.len = Math.hypot(v.x, v.y); 

    // normalized coordinates
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;

    // calculate the angle
    v.ang = Math.atan2(v.ny, v.nx);
}
</code></pre>

The comments should be sufficient to understand what is happening, we also use the atan2() function to calculate the angle of the vectors with respect to the zero angle. For simplicity's sake we'll make the origin at the center of the canvas. To fill our vector containers, we can do as such:

<pre><code>function setup() {
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

  noLoop()
}

// p1 -> first point
// p2 -> second point
// v -> container that we will fill
function toVec(p1, p2, v) {
    v.x = p2.x - p1.x;
    v.y = p2.y - p1.y;
    v.len = Math.hypot(v.x, v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
}
</code></pre>

You might ask, why we're not doing this with p5js's inbuilt vector class? And fret not, at the end of this post well go over an alternative version where we use the class with all it's functionalities. Now that we have converted our 3 points into vector form, we can tackle computing and locating the intermediary angle between the two vectors!

<h2><a name='angle'></a>Calculating the angle using the cross product</h2>

<h3>Explanation</h3>
Getting the halfway angle is probably the trickiest part of the procedure, and requires a number of steps. There probably are multiple ways to do so (more on that later), but for starters we'll begin with the method deduced from Blindman67's code. It will require us to freshen up on our linear algebra a little bit, specifically on the cross product of two vectors. We'll be heavily exploiting the formula of the cross product to compute the value of the formed angle, as well as where it is located with respect to the two vectors.

What the cross product actually represents algebraically, is a bit outside of the scope of this post. A perfect resource for understanding it can be found in the form of <a href="https://www.youtube.com/watch?v=eu6i7WJeinw&ab_channel=3Blue1Brown">this video</a> by <a href="https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw">3Blue1Brown</a> (I don't think there's a need for me to introduce his channel).

<!--
The important part here is how the cross product can help us find the angle between two vectors, and what problems arise from this method. It is important to mention here that the order of the points is crucial! When dealing with a closed polygonal shape the order of the points is such that the formed angle is pointing inwards. Using the cross product to determine the angle between two vectors gives an ambiguous result, the angle can form an acute wedge but could equivalently be interpreted as an obtuse fan. Keep this in mind for now, it will be relevant in a bit!
-->

The important part here is how the cross product can help us find the angle between two vectors, and what problems arise from this method. Given two vectors, we observe two angles, one that is less than 180 degrees, and another 'reflex' angle larger than 180 degrees. Our task is thus finding the angle that allows us to draw an arc, such that we can trace it and round off the pointy corner. We can only round off a corner if we select the angle that has a value less than 180 degrees. 

In this manner, we also need to determine the correct drawing order. We're going around the polygonal shape in a clockwise manner, but depending if the angle is concave or convex (curved inwards vs being curved outwards), we'll have to sometimes draw our arcs in a counterclockwise manner. To this end, calculating the value of the angle is not sufficient, we need an additional indicator that tells us how the second vector BC is situated with respect to the first one BA. For this we can use the cross product of BC and the perpendicular line to BA. More on that in a bit.


<h3>The Math</h3>
Generally the formula for finding the cross product, is the product of the magnitudes of our two vectors, with the sine of the angle that they form. More concretely:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Vert BA \Vert * \Vert BC \Vert * sin( \Theta ) \) </p>
</div>

<p>
This means that, finding the cross product requires us to already have the angle... who's value we're trying to find. Now, if we were to somehow already have the numerical value of the cross product, we could solve for \( sin( \Theta ) \), since we also already have the magnitudes of the two vectors concerned \( \Vert BA \Vert \) and \( \Vert BC \Vert \).
</p>

Luckily, there is another way to find the numerical value of the cross product! It is actually equal to the determinant of the 2x2 matrix formed by these vectors. This means computing this determinant will allow us to find the angle by solving the previous formula! Computing the determinant of a matrix is done as follows:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( v_{1}x * v_{2}y - v_{2}x * v_{1}y \) </p>
</div>

Then the value of the angle can be calculated as follows:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Theta = sin^{-1}(\frac{v_{1}x * v_{2}y - v_{2}x * v_{1}y}{\Vert BA \Vert * \Vert BC \Vert}) \) </p>
</div>

If you're not familiar with the inverse sine function, it simply does the reverse operation that a regular sine function does. So for example, a sine function will accept a value between -PI/2 and PI/2, and return a value that ranges between -1 and 1. The inverse function will accept values between -1 and 1 and return an angle that ranges between -PI/2 and PI/2. 

<p>
This formula can be further simplified! Since our vectors are already normalized \( \Vert BA \Vert * \Vert BC \Vert \), they will simply evaluate to 1, leaving us with:
</p>

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \Theta = sin^{-1}(det) \)</p>
</div>

Which is thus simply the inverse sine of the determinant. Now let's implement this.

<h3>The Code</h3>

The code for all of what we have discussed in the previous section is relatively... tame, and can essentially be summarised in a single line! Let's have a look at Blindman67's code:

<pre><code>// compute and store our 2 vectors
toVec(p2, p1, v1);
toVec(p2, p3, v2);

// Cross product of the two vectors - what we discussed in the previous section
sinA = v1.nx * v2.ny - v1.ny * v2.nx;

// Cross product of v2 and the line at 90 degrees to v1
// This is necessary and will help determine where to exactly position the center of the circle
sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;

// Clamping the value of sinA to avoid NaNs
angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
</code></pre>

We already discussed at length the first calculation that computes the variable sinA. The second line computes the cross product of the perpendicular line to BA and the vector BC. In actuality, we don't need to create a separate vector for this perpendicular, we can simply exploit the fact that the dot product of two vectors is zero when they're perpendicular. A vector perpendicular to BA would be simply one that has it's x and y values flipped, in addition to having one of their signs flipped. For example:

<pre><code>zeroDotProduct = v1.nx*(-v1.ny) + v1.ny*v1.nx
</code></pre>

Of course it also matters which coordinate's sign we flip, because it determines which perpendicular we're selecting (the one at 90 degrees or the other one at -90 degrees). If we flip the other sign, we'll would have to proceed differently in the calculations that follow.

Another tricky part here, I found to be the nested ternary check inside the inverse sine function. By simply expanding it, we visually get some clarity:

<pre><code>if(sinA<-1){
  angle = Math.asin(-1)
}else if(sinA>1){
  angle = Math.asin(1)
}else{
  angle = Math.asin(sinA)
}
</code></pre>

Go ahead a try putting values lesser than -1 or greater than 1 into the inverse sine function. It'll yield a NaN value. To avoid such thing, we simply clamp the value and can be done compactly as a one liner. And if you noticed, in actuality, we have already obtained the value of the angle!

<h2><a name='ambiguity'></a>Ambiguity of the cross product</h2>

There's a couple more steps that we have to do to go through to get the correct half angle between our two vectors. We'll start with this if/else flurry to determine the orientation of our angle:

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
</code></pre>

In essence, the information that we're trying to find here, is where exactly the center of the circle is located (the angle or it's reflex) as well as the correct drawing order of the arc (clockwise or counter clockwise). This is what the above if/else block takes care of. Let's for a second assume that the angle that we have calculated is sufficient, and simply halve it to find the bisector:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
}

function draw() {
  background(220);

  // make origin at center of canvas
  translate(w / 2, w / 2);

  randAng = random(TAU);
  randAng2 = random(TAU);
  p1 = { x: 100 * cos(randAng), y: 100 * sin(randAng) };
  p2 = { x: 0, y: 0 };
  p3 = { x: 100 * cos(randAng2), y: 100 * sin(randAng2) };

  strokeWeight(10);
  point(p1.x, p1.y);
  point(p2.x, p2.y);
  point(p3.x, p3.y);

  text("A", p1.x + 5, p1.y);
  text("B", p2.x + 5, p2.y);
  text("C", p3.x + 5, p3.y);

  strokeWeight(1);
  line(p1.x, p1.y, p2.x, p2.y);
  line(p3.x, p3.y, p2.x, p2.y);

  v1 = {};
  v2 = {};

  toVec(p2, p1, v1);
  toVec(p2, p3, v2);

  //coordinates of perpendicular vector
  perpx = v1.len * cos(v1.ang + PI / 2);
  perpy = v1.len * sin(v1.ang + PI / 2);

  stroke(0, 0, 255);
  strokeWeight(10);
  point(perpx, perpy);
  strokeWeight(1);
  drawingContext.setLineDash([5, 5]);
  line(perpx, perpy, 0, 0);
  drawingContext.setLineDash([0, 0]);

  sinA = v1.nx * v2.ny - v1.ny * v2.nx;
  sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
  angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);

  xx = 50 * cos(v1.ang + angle / 2);
  yy = 50 * sin(v1.ang + angle / 2);

  stroke(255, 0, 0);
  strokeWeight(10);
  point(xx, yy);
  strokeWeight(1);
  line(xx, yy, 0, 0);

  strokeWeight(1);
  stroke(0);
  fill(0);
  text(
    "sinA: " + (Math.round(sinA * 100) / 100).toFixed(2),
    -w / 2 + 20,
    -w / 2 + 20
  );
  text(
    "sinA90: " + (Math.round(sinA90 * 100) / 100).toFixed(2),
    -w / 2 + 20,
    -w / 2 + 40
  );
  
  stroke(0,0,255)
  fill(0,0,255)
  text('BLUE: The Perpendicular',-w/2+20,w/2-40)
  
  stroke(255,0,0)
  fill(255,0,0)
  text('RED: The Bisector',-w/2+20,w/2-20)

  noLoop();
}

function toVec(p1, p2, v) {
  v.x = p2.x - p1.x;
  v.y = p2.y - p1.y;
  v.len = Math.hypot(v.x, v.y);
  v.nx = v.x / v.len;
  v.ny = v.y / v.len;
  v.ang = Math.atan2(v.ny, v.nx);
}
</script>
<p></p>

Run the snippet a couple of times and take note of where the red line falls, it only gets it right when the angle is acute. We do not get the correct bisector when the angle is obtuse.

Hence, we need another indicator to be able to determine where to position the center of the circle. For this purpose we can use the cross product of the angle formed by the perpendicular of BA and the vector BC that we previously calculated. Simultaneously observing where that perpendicular falls with respect to BC, by inspecting the sign of these two cross products we can pin point where the bisector is located. Let's examine the different scenarios that arise:

<h4>When sinA90 &#60; 0 and sinA &#62; 0 </h4>
When sinA90 is larger than 0, and sinA is less than 0, then we can conclude that the perpendicular lies in between BA and BC, and the vectors BA and BC form an obtuse fan. In this case we need to add PI to the angle before halving it to obtain the correct half angle. 

<h4>When sinA90 &#60; 0 and sinA &#62; 0 </h4>
In this case the angle formed is also an obtuse fan, but the perpendicular does not lie in between BA and BC. In this case we need to subtract the angle from PI and also invert the drawing order (this is what radDirection and drawDirection are used for).

<h4>When sinA90 &#62; 0 </h4>
In this case the angle formed is an acute wedge, and we need to distinguish the two cases where the vector BC lies in between BA and it's perpendicular aka sinA &#60; 0 and when it's not aka sinA &#62; 0, and in the latter case we need to invert the drawing order.
	
If that explanation wasn't enough mental gymnastics, then feel welcome to sketch out a couple of angles and work them out by yourself. Important to keep in mind that if we were to select the other perpendicular, we then would have to flip some of the checks.
	
<!--
There's quite a lot going on here. We'll go through it step by step. The important thing to grasp here is that the sign of the cross product also tells us if the angle is oriented clockwise or counter clockwise, which we'll use to find the half angle that splits our angle in two. And remember, order is important! Keeping in mind that sinA90 is the angle formed by the perpendicular (to v1) and v2 (in that order), and sinA is the angle formed by v1 and v2 (in that order).

If the value of sinA90 is less than 0 then this means that the angle sinA90 has a counter-clockwise orientation. the opposite is also true. In essence there's 4 different possibilities depending on how the vectors v1 and v2 are situated with respect to each other. Have a look at the badly drawn examples:

<h4> When sinA90 > 0 </h4>
If sinA90 larger than 0, then the angle formed by the perpendicular (to v1) and v2 is oriented clockwise. In this case there is no need to do anything, and we can simply halve the value of angle to get the correct angle.

<h4> When sinA90 &#60 0 </h4>
If sinA90 is less than 0, then this angle is oriented counterclockwise. In this case we must distinguish between v1 being before or past v2. If the angle is less than 0 then v1 is past v2, and we have to add PI to the angle formed, else we need to subtract the angle from PI. Halving this value then yields the correct mid-way angle.
-->

<h4>Example </h4>
Here's an example of this in action. Observe the position of BC, BA and it's perpendicular as well as the values of sinA and sinA90. The assumed drawing order is clockwise starting from BA, going to BC:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
}

function draw() {
  background(220);

  // make origin at center of canvas
  translate(w / 2, w / 2);

  randAng = random(TAU);
  randAng2 = random(TAU);
  p1 = { x: 100 * cos(randAng), y: 100 * sin(randAng) };
  p2 = { x: 0, y: 0 };
  p3 = { x: 100 * cos(randAng2), y: 100 * sin(randAng2) };

  strokeWeight(10);
  point(p1.x, p1.y);
  point(p2.x, p2.y);
  point(p3.x, p3.y);

  text("A", p1.x + 5, p1.y);
  text("B", p2.x + 5, p2.y);
  text("C", p3.x + 5, p3.y);

  strokeWeight(1);
  line(p1.x, p1.y, p2.x, p2.y);
  line(p3.x, p3.y, p2.x, p2.y);

  v1 = {};
  v2 = {};

  toVec(p2, p1, v1);
  toVec(p2, p3, v2);

  //coordinates of perpendicular vector
  perpx = v1.len * cos(v1.ang + PI / 2);
  perpy = v1.len * sin(v1.ang + PI / 2);

  stroke(0, 0, 255);
  strokeWeight(10);
  point(perpx, perpy);
  strokeWeight(1);
  drawingContext.setLineDash([5, 5]);
  line(perpx, perpy, 0, 0);
  drawingContext.setLineDash([0, 0]);

  sinA = v1.nx * v2.ny - v1.ny * v2.nx;
  sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
  angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);

  radDirection = 1;
  drawDirection = false;
  if (sinA90 < 0) {
    if (angle < 0) {
      angle = Math.PI - angle;
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

  xx = 50 * cos(v1.ang + angle / 2);
  yy = 50 * sin(v1.ang + angle / 2);

  stroke(255, 0, 0);
  strokeWeight(10);
  point(xx, yy);
  strokeWeight(1);
  line(xx, yy, 0, 0);

  strokeWeight(1);
  stroke(0);
  fill(0);
  text(
    "sinA: " + (Math.round(sinA * 100) / 100).toFixed(2),
    -w / 2 + 20,
    -w / 2 + 20
  );
  text(
    "sinA90: " + (Math.round(sinA90 * 100) / 100).toFixed(2),
    -w / 2 + 20,
    -w / 2 + 40
  );
  
  stroke(0,0,255)
  fill(0,0,255)
  text('BLUE: The Perpendicular',-w/2+20,w/2-40)
  
  stroke(255,0,0)
  fill(255,0,0)
  text('RED: The Bisector',-w/2+20,w/2-20)

  noLoop();
}

function toVec(p1, p2, v) {
  v.x = p2.x - p1.x;
  v.y = p2.y - p1.y;
  v.len = Math.hypot(v.x, v.y);
  v.nx = v.x / v.len;
  v.ny = v.y / v.len;
  v.ang = Math.atan2(v.ny, v.nx);
}
</script>
<p></p>

If you've followed until here, then congrats, the hardest part is past us!

<h4>Simpler way to calculating the angle?</h4>

Now these are a lot of hoops to go through for simply calculating. If you want to have a simpler way of finding that angle you can use:

<pre><code>// vectors should be normalized
angle = atan2(v2.y, v2.x) - atan2(v1.y, v1.x)
if (angle < 0) { angle += 2 * PI;}
</code></pre>

However, this isn't sufficient to figuring out the correct drawing order of our arc. We'll go over a simpler way to our strategy later in this post.

<h4>A visual example</h4>
To conclude this section, here's a visual example of why this is necessary. If we were to ignore the correct orientation and drawing order of the arcs we would end up with weird behaviour:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  createCanvas(400, 400);
  ctx = canvas.getContext('2d');
}

function draw() {
  background(220);
  
  vertices = []
  vertices.push({x: 100, y: 100})
  vertices.push({x: 300, y: 100})
  vertices.push({x: 200, y: 200})
  vertices.push({x: 400, y: 400})
  vertices.push({x: 100, y: 300})
  vertices.push({x: 200, y: 200})
  vertices.push({x: 150, y: 200})
  
  ctx.beginPath();
  roundedPoly(ctx, vertices, 50+40*sin(frameCount/50));
  ctx.stroke();

}

// To draw you must call between 
//    ctx.beginPath();
//    roundedPoly(ctx, points, radius);
//    ctx.stroke();
//    ctx.fill();
// as it only adds a path and does not render. 
function roundedPoly(ctx, points, radiusAll) {
  var i, x, y, len, p1, p2, p3, v1, v2, sinA, sinA90, radDirection, drawDirection, angle, halfAngle, cRadius, lenOut,radius;
  // convert 2 points into vector form, polar form, and normalised 
  var asVec = function(p, pp, v) {
    v.x = pp.x - p.x;
    v.y = pp.y - p.y;
    v.len = Math.hypot(v.x, v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
  }
  radius = radiusAll;
  v1 = {};
  v2 = {};
  len = points.length;
  p1 = points[len - 1];
  // for each point
  for (i = 0; i < len; i++) {
    p2 = points[(i) % len];
    p3 = points[(i + 1) % len];
    //-----------------------------------------
    // Part 1
    asVec(p2, p1, v1);
    asVec(p2, p3, v2);
    sinA = v1.nx * v2.ny - v1.ny * v2.nx;
    sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
    angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
    //-----------------------------------------
    radDirection = 1;
    drawDirection = false;
    // if (sinA90 < 0) {
    //   if (angle < 0) {
    //     angle = Math.PI - angle;
    //   } else {
    //     angle = Math.PI - angle;
    //     radDirection = -1;
    //     drawDirection = true;
    //   }
    // } else {
    //   if (angle > 0) {
    //     radDirection = -1;
    //     drawDirection = true;
    //   }else{
    //     angle = TAU + angle
    //   }
    // }
    
    
    if(p2.radius !== undefined){
        radius = p2.radius;
    }else{
        radius = radiusAll;
    }
    //-----------------------------------------
    // Part 2
    halfAngle = angle / 2;
    //-----------------------------------------

    //-----------------------------------------
    // Part 3
    lenOut = Math.abs(Math.cos(halfAngle) * radius / Math.sin(halfAngle));
    //-----------------------------------------

    //-----------------------------------------
    // Special part A
    if (lenOut > Math.min(v1.len / 2, v2.len / 2)) {
      lenOut = Math.min(v1.len / 2, v2.len / 2);
      cRadius = Math.abs(lenOut * Math.sin(halfAngle) / Math.cos(halfAngle));
    } else {
      cRadius = radius;
    }
    //-----------------------------------------
    // Part 4
    x = p2.x + v2.nx * lenOut;
    y = p2.y + v2.ny * lenOut;
    //-----------------------------------------
    // Part 5
    x += -v2.ny * cRadius * radDirection;
    y += v2.nx * cRadius * radDirection;
    //-----------------------------------------
    // Part 6
    ctx.arc(x, y, cRadius, v1.ang + Math.PI / 2 * radDirection, v2.ang - Math.PI / 2 * radDirection, drawDirection);
    //-----------------------------------------
    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

<h2><a name='start'></a>Positioning the circle</h2>

The next step is finding the distance from the circlecenter to the coordinate of the corner:

<pre><code>lenOut = abs(cos(halfAngle) * radius / sin(halfAngle));
</code></pre>

<p> Here, instead of finding the distance of the circle center F to corner point B, we're directly finding the distance from the corner point B to the tangential points E and F. For this we're making use of the formulas that can be applied for finding the length of the hypotenuse, which is designated by BF. We then have \( BF * cos(\Theta) = BE \) and \( BF * sin( \Theta) = FE \). 
</p>

Using these two formulas we find the formula that Blindman67 used:

<div style="width:100%; display: flex; justify-content: center;">
<p> \( \frac{BE}{cos(\Theta)} = \frac{r}{sin(\theta)} \)</p>
</div>

And here we also have the value of the radius (that we specified):

<div style="width:100%; display: flex; justify-content: center;">
<p> \( BE = abs( \frac{cos(\Theta) * r}{sin(\theta)}) \)</p>
</div>

One problem here is that the radius that we specified as input, might not fit into the corner. Since it could techincally be longer than the edge itself. This can be remedied with the following check:

<pre><code>
if (lenOut > min(v1.len / 2, v2.len / 2)) {
  lenOut = min(v1.len / 2, v2.len / 2);
  cRadius = abs(lenOut * sin(halfAngle) / cos(halfAngle));
} else {
  cRadius = radius;
}
</code></pre>

Once this is dealt with, we can calculate the coordinates of one of the tangetial points to the circle:

<pre><code>x = p2.x + v2.nx * lenOut;
y = p2.y + v2.ny * lenOut;
</code></pre>

Then move along the perpendicular to find the circle center:

<pre><code>x += -v2.ny * cRadius * radDirection;
y += v2.nx * cRadius * radDirection;
</code></pre>

Note that this also depends on the direction that we determined earlier.

<h2><a name='ctx'></a>The rendering context</h2>

For drawing purposes we will make use of the javascript canvas rendering context interface, which allows us to draw all sorts of shapes. You can find an extensive documentation <a href='https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D'>here<a>.

To do so we need to invoke the context, usually I do this in the setup function, as such:
<pre><code>function setup(){
    ctx = canvas.getContext('2d');
}
</code></pre>

And then we can make use of it to draw stuff:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup(){
	w = min(windowWidth, windowHeight)
	createCanvas(w,w)
	ctx = canvas.getContext('2d');
}

function draw(){
	background(220)
	// Set line width
	ctx.lineWidth = 10;

	// Wall
	ctx.strokeRect(75, 140, 150, 110);

	// Door
	ctx.fillRect(130, 190, 40, 60);

	// Roof
	ctx.beginPath();
	ctx.moveTo(50, 140);
	ctx.lineTo(150, 60);
	ctx.lineTo(250, 140);
	ctx.closePath();
	ctx.stroke();
	
	noLoop();
}
</script>
<p></p>

For our purposes we'll only need to use one function, namely the ctx.arc() call:
<pre><code>ctx.arc(x, y, cRadius, v1.ang + Math.PI / 2 * radDirection, v2.ang - Math.PI / 2 * radDirection, drawDirection);
</code></pre>

Where the first two parameters are the coordinates of the arc center, followed by the radius in third place, and the starting and end angle in fourth and fifth position. the last input specifies if this arc is drawn in clockwise or counter clockwise manner.

<h2><a name='drawing'></a>Drawing the final shape</h2>

The final shape will be drawn by looping through the set of points that make up the vertices of our polygon. Here's a condensed version of this loop with the calculations omitted: 

<pre><code>ctx.beginPath();
len = points.length;

// initial point is the last point of the shape
p1 = points[len - 1];
for (i = 0; i < len; i++) {
    p2 = points[(i) % len];
    p3 = points[(i + 1) % len];
    
    // Calculations go here
    
    // get the next set of points
    p1 = p2;
    p2 = p3;
}
ctx.closePath();
ctx.stroke() // add an outline to the shape
ctx.fill()   // add color to the shape
</code></pre>

In this manner, the first point is actually the last vertex of the polygon. The final shape would then be drawn by first positioning all of your vertices, and storing them in an array, in the following manner:

<pre><code>vertices = []
vertices.push({x: coordX, y:coordY})
// add more vertices etc...
</code></pre>

And then passing the array to the drawing function:

<pre><code>drawRoundPoly(ctx, vertices, radius)
</code></pre>

The final snippet of code that you would then use, is the one provided by Blindman67. Here's a minimal example:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  createCanvas(400, 400);

  ctx = canvas.getContext("2d");
  rSlider = createSlider(0, 50, 25)
  rSlider.position(10, 10);
}

function draw() {
  background(220);

  vertices = [];
  vertices.push({ x: 100, y: 100 });
  vertices.push({ x: 300, y: 100, radius: 20 });
  vertices.push({ x: 200, y: 200 });
  vertices.push({ x: 400, y: 400 });
  vertices.push({ x: 100, y: 300 });
  vertices.push({ x: 200, y: 200, radius: 0 });
  vertices.push({ x: 150, y: 200 });

  ctx.beginPath();
  roundedPoly(ctx, vertices, rSlider.value());
  ctx.stroke();
  ctx.fill();
}

// To draw you must call between
//    ctx.beginPath();
//    roundedPoly(ctx, points, radius);
//    ctx.stroke();
//    ctx.fill();
// as it only adds a path and does not render.
function roundedPoly(ctx, points, radiusAll) {
  var i,
    x,
    y,
    len,
    p1,
    p2,
    p3,
    v1,
    v2,
    sinA,
    sinA90,
    radDirection,
    drawDirection,
    angle,
    halfAngle,
    cRadius,
    lenOut,
    radius;
  // convert 2 points into vector form, polar form, and normalised
  var asVec = function (p, pp, v) {
    v.x = pp.x - p.x;
    v.y = pp.y - p.y;
    v.len = Math.sqrt(v.x * v.x + v.y * v.y);
    v.nx = v.x / v.len;
    v.ny = v.y / v.len;
    v.ang = Math.atan2(v.ny, v.nx);
  };
  radius = radiusAll;
  v1 = {};
  v2 = {};
  len = points.length;
  p1 = points[len - 1];
  // for each point
  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];
    //-----------------------------------------
    // Part 1
    asVec(p2, p1, v1);
    asVec(p2, p3, v2);
    sinA = v1.nx * v2.ny - v1.ny * v2.nx;
    sinA90 = v1.nx * v2.nx - v1.ny * -v2.ny;
    angle = Math.asin(sinA < -1 ? -1 : sinA > 1 ? 1 : sinA);
    //-----------------------------------------
    radDirection = 1;
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
      } else {
        angle = TAU + angle;
      }
    }

    if (p2.radius !== undefined) {
      radius = p2.radius;
    } else {
      radius = radiusAll;
    }
    //-----------------------------------------
    // Part 2
    halfAngle = angle / 2;
    //-----------------------------------------

    //-----------------------------------------
    // Part 3
    lenOut = Math.abs((Math.cos(halfAngle) * radius) / Math.sin(halfAngle));
    //-----------------------------------------

    //-----------------------------------------
    // Special part A
    if (lenOut > Math.min(v1.len / 2, v2.len / 2)) {
      lenOut = Math.min(v1.len / 2, v2.len / 2);
      cRadius = Math.abs((lenOut * Math.sin(halfAngle)) / Math.cos(halfAngle));
    } else {
      cRadius = radius;
    }
    //-----------------------------------------
    // Part 4
    x = p2.x + v2.nx * lenOut;
    y = p2.y + v2.ny * lenOut;
    //-----------------------------------------
    // Part 5
    x += -v2.ny * cRadius * radDirection;
    y += v2.nx * cRadius * radDirection;
    //-----------------------------------------
    // Part 6
    ctx.arc(
      x,
      y,
      cRadius,
      v1.ang + (Math.PI / 2) * radDirection,
      v2.ang - (Math.PI / 2) * radDirection,
      drawDirection
    );
    //-----------------------------------------
    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

Also notice that you can additionally pass a 'radius' to each individual vertex to override the main radius passed to the function call.



<h1><a name='p5vec'></a>Further Improvements</h1>
First off, huge thanks to <a href='https://twitter.com/davepvm'>Dave</a> and <a href='https://twitter.com/clayheaton'>Clay</a> from the birbsnest for the feedback! Thanks to Dave (who's a freaking coding wizard), you're getting three additional sections here, firstly we'll update the method that we've discussed so far using the inbuilt p5 vector class and it's functionalities, and secondly we'll have a look at the solution Dave came up with for rounding off corners. He also shared a version using Bezier Curves!

<h2><a name='p5vec'></a>Blindman67's method using p5 vectors</h2>
Rather than defining our own asVec() function, we can make use of the p5 vector class and the functions that come along with it. In that manner we declare our vector BA and BC like this:

<pre><code>p2 = points[i % len];
p3 = points[(i + 1) % len];

A = createVector(p1.x, p1.y);
B = createVector(p2.x, p2.y);
C = createVector(p3.x, p3.y);

BA = A.sub(B);
BC = C.sub(B);
</code></pre>

Next we can compute the angle and the angle of the perpendicular in the following manner:
<pre><code>// need to call copy() because most vector functions are in-place
BAnorm = BA.copy().normalize();
BCnorm = BC.copy().normalize();

sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
sinA90 = BAnorm.dot(BCnorm);
angle = asin(sinA);
</code></pre>

Note that we have to call the copy() function prior to functions such as normalize() and rotate(), as they will mutate and alter the vector itself. Hence we create two normalized copies of BA and BC, since we'll need to use their magnitudes later on again.

Also note how we're now calculating the dot product rather than the cross product! It's still the same calculation, but for convenience it's easier to do it in that manner. This is derived from the fact that the dot product of two vectors is equal to the cross product of one of these vectors and the perpendicular to the other vector. So we simply rotate one of them by 90 degress and compute the dot product. Same for sinA90.

The rest of the code is identical to what we had before, with the exception that we're solely using the p5 vector properties and functions:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  createCanvas(400, 400);

  ctx = canvas.getContext("2d");
  rSlider = createSlider(0, 50, 25)
  rSlider.position(10, 10);
}

function draw() {
  background(220);

  vertices = [];
  vertices.push({ x: 300, y: 100 });
  vertices.push({ x: 200, y: 200});
  vertices.push({ x: 350, y: 350 });
  vertices.push({ x: 100, y: 300 });
  vertices.push({ x: 200, y: 300 });
  //vertices.push({ x: 200, y: 200, radius: 0 });
  vertices.push({ x: 150, y: 200 });

  ctx.beginPath();
  roundedPoly(ctx, vertices, rSlider.value());
  ctx.stroke();
  ctx.fill()
}

function roundedPoly(ctx, points, radiusAll) {
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0 ? (angle += PI) : ((radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }
    
    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

This makes it a little more compact, but also less readable.

<h2><a name='dave'></a>Dave's acceleration method</h2>

After asking for feedback on <a href='https://twitter.com/sableRaph'>Raph's</a> discord and discussing the previous method a little, Dave pointed out that an alternative way to knowing the drawing order would be using the curvature vector's direction. Rather than using the positions of BA and it's perpepndicular with respect to BC, and doing this check:

<pre><code>(radDirection = 1), (drawDirection = false);
if (sinA90 < 0) {
  angle < 0 ? (angle += PI) : ((radDirection = -1), (drawDirection = true));
} else {
  angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
}
</code></pre>

We can do this:

<pre><code>accelDir = BAnorm.copy().add(BCnorm)
radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
drawDirection = radDirection === -1
</code></pre>

We obtain the curvature vector by adding BA and BC, and this vector will generally always point into the direction of the circlecenter. Now we only need to determine if we need to draw the arc in a clockwise or counterclockwise direction. For this we recycle our previous check and observe where the curvature vector falls with respect to BC. The drawing order can then be deduced from the arc direction!

Here's a sketch where we visualize these curvature vectors:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  createCanvas(400, 400);

  ctx = canvas.getContext("2d");
  rSlider = createSlider(0, 50, 25)
  rSlider.position(10, 10);
}

function draw() {
  background(220);

  vertices = [];
  vertices.push({ x: 300, y: 100 });
  vertices.push({ x: 200, y: 200});
  vertices.push({ x: 350, y: 350 });
  vertices.push({ x: 100, y: 300 });
  vertices.push({ x: 200, y: 200 });
  //vertices.push({ x: 200, y: 200, radius: 0 });
  vertices.push({ x: 150, y: 200 });

  ctx.beginPath();
  roundedPoly(ctx, vertices, rSlider.value());
  ctx.stroke();
  ctx.fill()
  drawAccelerations(ctx, vertices, rSlider.value());
}

function drawAccelerations(ctx, points, radiusAll) {
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    angle = asin(sinA);

    accelDir = BAnorm.copy().add(BCnorm)
    accelDirunorm = BA.copy().add(BC).limit(30)
    
    strokeWeight(5)
    point(B.x,B.y)
    strokeWeight(1)
    push()
    translate(B.x,B.y)
    line(0,0, accelDirunorm.x,accelDirunorm.y)
    vbase = createVector(0,0)
    vhead = createVector(accelDirunorm.x,accelDirunorm.y)
    drawArrow(vbase, vhead, 'black');
    pop()
    radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    drawDirection = radDirection === -1


    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    // ctx.arc(
    //   x,
    //   y,
    //   cRadius,
    //   BA.heading() + (PI / 2) * radDirection,
    //   BC.heading() - (PI / 2) * radDirection,
    //   drawDirection
    // );

    p1 = p2;
    p2 = p3;
  }
  // ctx.closePath();
}


function roundedPoly(ctx, points, radiusAll) {
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    angle = asin(sinA);

    accelDir = BAnorm.copy().add(BCnorm)
    radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    drawDirection = radDirection === -1


    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
   ctx.closePath();
}

function drawArrow(base, vec, myColor) {
  push();
  stroke(myColor);
  fill(myColor);
  translate(base.x, base.y);
  line(0, 0, vec.x, vec.y);
  rotate(vec.heading());
  let arrowSize = 7;
  translate(vec.mag() - arrowSize, 0);
  triangle(0, arrowSize / 2, 0, -arrowSize / 2, arrowSize, 0);
  pop();
}
</script>
<p></p>

<h2><a name='bezier'></a>Using Bezier vertices</h2>

And for the final alternative method, Dave wrote <a href='https://editor.p5js.org/davepagurek/sketches/AwgpGbabn'>this method</a> that relies on the bezierVertex() function in p5:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
let verts
let rSlider

function setup() {
  createCanvas(400, 400);
  background(255)
  rSlider = createSlider(0, 50, 25)
  rSlider.position(10, 10);
  verts = [
    createVector(300, 100),
    createVector(200, 200),
    createVector(400, 400),
    createVector(100, 300),
    createVector(200, 200),
    createVector(150, 200),
  ]
}

function draw() {
  background(255)
  noFill()
  stroke(0)
  beginShape()
  for (const { x, y } of verts) {
    vertex(x, y)
  }
  endShape(CLOSE)

  noStroke()
  fill(255, 0, 0, 100)
  drawRounded(verts, rSlider.value())
}

function drawRounded(points, r) {
  beginShape()
  for (let i = 0; i < points.length; i++) {
    const a = points[i]
    const b = points[(i+1) % points.length]
    const c = points[(i+2) % points.length]
    const ba = a.copy().sub(b).normalize()
    const bc = c.copy().sub(b).normalize()
    
    // Points in the direction the corner is accelerating towards
    const normal = ba.copy().add(bc).normalize()
    
    // Shortest angle between the two edges
    const theta = ba.angleBetween(bc)
    
    // Find the circle radius that would cause us to round off half
    // of the shortest edge. We leave the other half for neighbouring
    // corners to potentially cut.
    const maxR = min(a.dist(b), c.dist(b))/2 * abs(sin(theta / 2))
    const cornerR = min(r, maxR)
    // Find the distance away from the corner that has a distance of
    // 2*cornerR between the edges
    const distance = abs(cornerR / sin(theta / 2))
    
    // Approximate an arc using a cubic bezier
    const c1 = b.copy().add(ba.copy().mult(distance))
    const c2 = b.copy().add(bc.copy().mult(distance))
    const bezierDist = 0.5523 // https://stackoverflow.com/a/27863181
    const p1 = c1.copy().sub(ba.copy().mult(2*cornerR*bezierDist))
    const p2 = c2.copy().sub(bc.copy().mult(2*cornerR*bezierDist))
    vertex(c1.x, c1.y)
    bezierVertex(
      p1.x, p1.y,
      p2.x, p2.y,
      c2.x, c2.y
    )
  }
  endShape(CLOSE)
}
</script>
<p></p>


And that's a wrap! If there are any unclear notions, mistakes or questions, feel free to leave me a comment or reach out to me on social media! If you find this post useful, consider supporting me by sharing it or following me on my social medias. Otherwise, cheers and happy sketching!