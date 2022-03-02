---
title: Hexagonal Grids in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: A tutorial on multiple ways to create a hexagonal grid in p5js
thumbnail_path: https://gorillasun.de/assets/images/hexagons/hexagon.mp4
published: true
exclude_rss: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jewelsbanner.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles.png" alt="">
</span>




<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/texture.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/texture2.png" alt="">
</span>









Hexagonal grids have many names, depending on the context. Maybe the first term that comes to mind is the honeycomb lattice, where the term lattice has implications for physics and chemistry. Joh Conway called it the hextille (https://en.wikipedia.org/wiki/Hexagonal_tiling),  which also has a nice ring to it. In geometry we would call it a 'hexagonal tiling' or 'hexagonal tesselation'. Note that Tesselation is a vast and incredibly interesting field of mathematics (https://en.wikipedia.org/wiki/Tessellation), that one could study for hours and draw endless inspiration from. You can dip your toes into it with this Veritasum video (https://www.youtube.com/watch?v=48sCx-wBs34&ab_channel=Veritasium).



1. Drawing a hexagon in p5
2. The grid method
3. The spiral method
4. The recursive subdivision method
5. The bravais method
6. Two offset triangular lattices
7. other methods
10. Beyond Hexagonal tesselations




<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/roses.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jestercap.png" alt="">
		</span>
	</div>
</div>

<h2>Drawing a hexagon in p5</h2>

The first step we need to tackle, is how to draw a singular hexagon in p5. I believe that the most sensible approach to doing so is by utilising trigonometry.

A regular polygon satisfies two properties: it is equiangular and equilateral. Meaning that the value of it's vertex angles are equal, and it's sides are of equal length. In this manner, the vertices of any regular polygon, with any number of sides, all lie on a <a href='https://en.wikipedia.org/wiki/Regular_polygon'>common circle</a>. We can leverage this property to construct our hexagons (or any other regular polygon for that matter).

A full circle has 360 degrees, or equivalently TAU radians (TAU == 2 * PI). Constructing any regular polygon thus boils down to splitting 360 degrees by the number of sides that the desired polygon has. In the case of a hexagon, we divide a full rotation of 360 degrees by 6, ending up with a value of 60 degrees. We can now use this knowledge to position the hexagon vertices in an equiangular manner around a circle:

<pre><code>
// Function that draws a hexagon (or any other regular polygon)
// centerX and centerY determine where the polygon is positioned
// the radius parameter determines the size of the enclosing circle
// numSides specifies the number of the polygon's sides
function drawHexagon(centerX, centerY, radius, numSides){
  // p5 already has some functionality for drawing more complex shapes
  // beginShape tells p5 that we'll be positioning some vertices in a bit
  beginShape()

  // This is where the heavy lifting happens
  // Make equiangular steps around the circle depending on the number of sides
  for(let a = 0; a < TAU; a+=TAU/numSides){

    // calculate the cartesian coordinates for a given angle and radius
    // and centered at the centerX and centerY coordinates
    var x = centerX + radius * cos(a)
    var y = centerY + radius * sin(a)

    // creating the vertex
    vertex(x, y)
  }

  // telling p5 that we are done positioning our vertices
  // and can now draw it to the canvas
  endShape(CLOSE)
}
</code></pre>

Go through the code and have a look at the comments, they should explain the purpose of every statement. And here's a condensed version of the function:

<pre><code>function drawHexagon(cX, cY, r){
  beginShape()
  for(let a = 0; a < TAU; a+=TAU/6){
    vertex(cX + r * cos(a), cY + r * sin(a))
  }
  endShape(CLOSE)
}
</code></pre>

Since we're only going to draw hexagons, we can omit the numSides input parameter.



<h2>The grid method</h2>
<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles3.png" alt="">
		</span>
	</div>
</div>

At first I wanted to call this the 'naive' way of constructing a hexagonal lattice, but I don't think it deserves to be labeled as such, since occasionally it has advantages over the following methods (depending on the sketch you might want to do it this way). I believe that the caveat here is thinking of a hexagonal lattice as a 'grid', when it is a lot more than that!

Essentially, we'll construct our lattice with a nested for loop, in the same manner that we would construct a rectangular grid:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup(){
  w = min(windowWidth, windowHeight)
  createCanvas(w, w)

  gridWidth = w
  gridHeight = w
  hexagonSize = w/10
}

function drawHexagon(cX, cY, r){
  beginShape()
  for(let a = 0; a < TAU; a+=TAU/6){
    vertex(cX + r * cos(a), cY + r * sin(a))
  }
  endShape(CLOSE)
}

function makeGrid(){
  for(y = 0; y < gridWidth; y+=hexagonSize){
    for(x = 0; x < gridHeight; x+=hexagonSize){

      // divide hexagon size by two
      // since we need to pass in the radius and not the diameter
      drawHexagon(x, y, hexagonSize/2)
    }
  }
}

function draw(){
  background(0);
  stroke(255);
  noFill();

  makeGrid()

  noLoop()
}
</script>
<p></p>

Ok, so we got a grid... but it doesn't look right at all! The hexagons are arranged, just as if they were sitting on top of a rectangular grid. After all, we programmed it the same way. We'll have to make a couple of modifications! Also note that we are dividing the the value passed to the drawHexagon() function by two, since we need to pass a radius and not a diameter.

First, let's space out this grid a little more, simply by multiplying the increment of the inner loop by 1.5:

<pre><code>for(x = 0; x < gridWidth; x+=hexagonSize*1.5){}
</code></pre>

Next up, we'll have to offset every other row. To accomplish this we can keep track of the row number with an additional 'count' variable:

<pre><code>function makeGrid(){
  count = 0
  for(y = 0; y < gridHeight; y+=hexagonSize){
    for(x = 0; x < gridWidth; x+=hexagonSize*1.5){
      drawHexagon(
        x+hexagonSize*(count%2==0)*0.75,
        y,
        hexagonSize/2
      )
    }
    count ++
  }
}
</code></pre>

Here we're using a little boolean trick to add a horizontal offset whenever the count variable is even! (so basically every other row). We could do it in the following manner:

<pre><code>if(count%2 == 0){
  drawHexagon(x + hexagonSize*0.75, y, hexagonSize/2)
}else{
  drawHexagon(x + hexagonSize, y, hexagonSize/2)
}
</code></pre>

This is a bit wordy, and can be replaced by the little statement (count%2==0), which will evaluate to 1 when it's true and 0 when it's false. And since we're wrapping it in parentheses and enclosing it with arithmetic operators, it will be parsed as an integer. Hence when it is true, the offset is applied, otherwise the offset gets multiplied by zero and has no effect on the overall position. Neat, right?

The overall grid looks a bit better now, but we're not quite there yet, we need to lessen the vertical distance between the rows, which can be done by dividing the vertical increment by a value of 2.3:

<pre><code>function makeGrid(){
  count = 0
  for(y = 0; y < gridHeight; y+=hexagonSize/2.3){
    for(x = 0; x < gridWidth; x+=hexagonSize*1.5){
      drawHexagon(
        x+hexagonSize*(count%2==0)*0.75,
        y,
        hexagonSize/2
      )
    }
    count ++
  }
}
</code></pre>

And we end up with a neatly packed hexagonal grid! Here is a working example, try to change the hexagonSize parameter and see the grid at different scales:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup(){
  w = min(windowWidth, windowHeight)
  createCanvas(w, w)

  gridWidth = w
  gridHeight = w
  hexagonSize = w/10
}

function drawHexagon(cX, cY, r){
  beginShape()
  for(let a = 0; a < TAU; a+=TAU/6){
    vertex(cX + r * cos(a), cY + r * sin(a))
  }
  endShape(CLOSE)
}

function makeGrid(){
  count = 0
  for(y = 0; y < gridHeight; y+=hexagonSize/2.3){
    for(x = 0; x < gridWidth; x+=hexagonSize*1.5){
      drawHexagon(x+hexagonSize*(count%2==0)*0.75, y, hexagonSize/2)
    }
    count ++
  }
}

function draw(){
  background(0);
  stroke(255);
  noFill();

  makeGrid()

  noLoop()
}
</script>
<p></p>

<h2>Hexagon Spirals</h2>
<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/abstractvegetation.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/abstractvegetation2.png" alt="">
		</span>
	</div>
</div>

Rather than drawing a grid in a top down, left to right manner, this method starts at a given point and paves it's way outwards spiraling around the already drawn hexagons. Here's a visualization of how this works:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/spiralgrid.gif" alt="">
</span>

<h2>Spiral Method 1</h2>

An alternate method to generating a hexagonal grid is by spiraling outwards around an initial hexagon:

<pre><code>// modified from: https://stackoverflow.com/a/2143499
function makeSpiral(centerX, centerY, size, count){
  var x = 0;
  var y = 0;

  s = size/1.75

  push()
  translate(centerX, centerY)
  drawHexagon(centerX, centerY, size/1.75)
  for(let n = 0; n<count; n++ ) {
    for(let i=0; i<n; i++){x++;drawHexagon(x*s, y*s, s/1.75)}  // move right
    for(let i=0; i<n-1; i++){y++;drawHexagon(x*s, y*s, s/1.75)} // move down right. Note N-1
    for(let i=0; i<n; i++){x--;y++;drawHexagon(x*s, y*s, s/1.75)} // move down left
    for(let i=0; i<n; i++){x--;drawHexagon(x*s, y*s, s/1.75)} // move left
    for(let i=0; i<n; i++){y--;drawHexagon(x*s, y*s, s/1.75)} // move up left
    for(let i=0; i<n; i++){x++;y--;drawHexagon(x*s, y*s, s/1.75)} // move up right
  }
  pop()
}
</code></pre>

This is pretty straightforward, we are stepping around an initial hexagon paving our way with other hexagons. We do this by incrementing two counters x and y that specify the position of the drawn hexagons (we need to multiply by the hexagon size to get the right spacing).

The further out we go the more hexagons we need to draw on a line/side, this is determined by the variable 'n' in the outer loop. In this manner the count variable controls the number of hexagons that will be found on the longest outer side of the final hexagon cluster.

However, if you run this snippet as is, you'll notice that the final cluster doesn't quite look right. We still need to apply a transform to the position of the drawn hexagons. In that manner our hexagon function becomes:

<pre><code>function drawHexagon(cX, cY, r){
  hx = cX + cY/2
  hy = sqrt(3)/2 * cY

  beginShape()
  for(let a = TAU/12; a < TAU + TAU/12; a+=TAU/6){
    vertex(hx + r * cos(a), hy + r * sin(a))
  }
  endShape(CLOSE)
}
</code></pre>

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup(){
  w = min(windowWidth, windowHeight)
  createCanvas(w, w)

  gridWidth = w
  gridHeight = w
  hexagonSize = w/10
}

function drawHexagon(cX, cY, r){
  hx = cX + cY/2
  hy = sqrt(3)/2 * cY

  beginShape()
  for(let a = TAU/12; a < TAU + TAU/12; a+=TAU/6){
    vertex(hx + r * cos(a), hy + r * sin(a))
  }
  endShape(CLOSE)
}

function makeSpiral(centerX, centerY, size, count){
  var x = 0;
  var y = 0;

  s = size/1.75

  push()
  translate(centerX, centerY)
  drawHexagon(centerX, centerY, size/1.75)
  for(let n = 0; n<count; n++ ) {
    for(let i=0; i<n; i++){x++;drawHexagon(x*s,y*s,s/1.75)}  // move right
    for(let i=0; i<n-1; i++){y++;drawHexagon(x*s,y*s,s/1.75)} // move down right. Note N-1
    for(let i=0; i<n; i++){x--;y++;drawHexagon(x*s,y*s,s/1.75)} // move down left
    for(let i=0; i<n; i++){x--;drawHexagon(x*s,y*s,s/1.75)} // move left
    for(let i=0; i<n; i++){y--;drawHexagon(x*s,y*s,s/1.75)} // move up left
    for(let i=0; i<n; i++){x++;y--;drawHexagon(x*s,y*s,s/1.75)} // move up right
  }
  pop()
}

function draw(){
  background(0);

  makeSpiral(w/2,w/2,hexagonSize, 5);

  noLoop();
}
</script>
<p></p>


<h2>Spiral Method 2</h2>

If you don't like 6 separate for loops, we can do the same thing with only one and a tricky calculation. Here goes the compacter version:

<pre><code>// source: https://stackoverflow.com/a/43913544
function makeSpiral(centerX, centerY, radius, count) {
    var x = centerX
    var y = centerY
    var angle = TAU / 6
    var side = 0

    drawHexagon(x,y,size/1.75)
    count--;
    while (count > 0) {
        for (var t = 0; t < floor((side+4)/6)+(side%6==0) && count; t++) {
            y = y - radius * cos(side * angle);
            x = x - radius * sin(side * angle);
            drawHexagon(x, y, size/1.75);
            count--;
        }
        side++;
    }
}
</code></pre>



Essentially the function accomplishes the same thing similar to the previous version. Similarly to it's little brother, we're stepping in a spiral around our initial hexagon, gradually going outwards. Here we also keep track of our position with the x and y variables:

<pre><code>y = y - radius * cos(side * angle);
x = x - radius * sin(side * angle);
</code></pre>

The variable 'side' being the indicator that tells us in which direction we need to step next (multiplying the angle). Now the inner for loop just needs to be able to determine how many steps we need to take before we are required to increment the side variable and change direction.

This happens in the condition statement of the for loop and is a bit heavy to digest, so let's go through it step by step! Let's have a look at what this calculation does:

<pre><code>floor((side+4)/6)+(side%6==0)
</code></pre>

The best way to explain this is by going through the first couple of iterations of the for loop. Say the 'side' variable is 0 at the very beginning, then the statement floor((side+4)/6) will evaluate to zero and the statement side%6 == 0 evaluates to 1 because it is true. In this case we will draw exactly one hexagon located straight above our initial hexagon (because 0 * TAU/6 is 0):

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/2.png" alt="">
</span>

Next we decrement the count variable and increment the side variable (note that the side variable gets incremented outside of the for loop). Repeating the same thought process we proceed: floor((side+4)/6) will again evaluate to 1. In fact it will evaluate to 1 for the first 5 iterations. Only on the 6th iteration it will evaluate to 2, since we need to step in the same direction twice:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/6th.png" alt="">
</span>

Essentially the neat little formula in the for loop condition tells us how many steps we need to take in one direction, before we should increment the variable 'side' and make a turn paving new hexagons onto a new side of the hexagonal spiral.

Why do we need:

<pre><code>(side%6==0)
</code></pre>

in the condition? Well, every loop we make around, we're gonna need one extra additional hexagon to begin a new lap around. This happens when the 'side' variable assumes a multiple of 6. So basically, every time we complete six sides.


Okay, this was tough to explain, but I hope it makes sense. If there are any unclear points leave me a comment or send me a DM! Alright, onto the next method!


<h2>Recursive subdivision method</h2>
<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/flowers.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jap.png" alt="">
		</span>
	</div>
</div>

If you're a fan of the honeycomb shape, then you'll love the sweet recursive way to do it:

<pre><code>
function recursiveHexagon(cX, cY, depth, r){
  for(let a = 0; a<TAU; a+=TAU/6){
    var x = cX + r * cos(a)
    var y = cY + r * sin(a)

    strokeWeight(2)
    point(x,y)

    if(depth > 0){
      recursiveHexagon(x,y,depth-1,r/2)
    }
  }
}
</code></pre>

This is the first time that I talk about a recursion on the blog. So for those not familiar, something is called 'recursive' is when you call a method from within itself. Naturally, if done wrong, you'll end up with your machine exploding. But when done correctly you can achieve some pretty neat results, and often in a lot less code!


<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/bubblepop3.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/bubblepop.png" alt="">
		</span>
	</div>
</div>








Hexagonal Grid related methods and resources that go beyond drawing: https://www.redblobgames.com/grids/hexagons/#:~:text=In%20the%20pointy%20orientation%2C%20a,from%20sin(60%C2%B0).&text=The%20horizontal%20distance%20between%20adjacent,is%20h%20*%203%2F4%20.


Tiling interesting question: https://math.stackexchange.com/questions/4187829/can-one-always-tile-the-plane-with-a-regular-polygon-a-single-other-shape
