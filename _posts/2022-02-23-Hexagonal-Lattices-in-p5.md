---
title: Hexagonal Lattices in p5
author: Ahmad Moussa
categories:
  - p5js
description: A tutorial on multiple ways to create a hexagonal grid
thumbnail_path: https://gorillasun.de/assets/images/vestiges/hexagon.mp4
published: true
exclude_rss: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jewels.png" alt="">
</span>


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

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/texture.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/texture2.png" alt="">
</span>


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

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles2.png" alt="">
		</span>
	</div>
</div>

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles3.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles4.png" alt="">
		</span>
	</div>
</div>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/tiles.png" alt="">
</span>

Tiling and tessellations is one of those honeypots that you fall into and helplessly get stuck in.

Hexagonal grids have many names, depending on the context. Maybe the first term that comes to mind is the honeycomb lattice, where the term lattice has implications for physics and chemistry. Joh Conway called it the hextille (https://en.wikipedia.org/wiki/Hexagonal_tiling),  which also has a nice ring to it. In geometry we would call it a 'hexagonal tiling' or 'hexagonal tesselation'. Note that Tesselation is a vast and incredibly interesting field of mathematics (https://en.wikipedia.org/wiki/Tessellation), that one could study for hours and draw endless inspiration from. You can dip your toes into it with this Veritasum video (https://www.youtube.com/watch?v=48sCx-wBs34&ab_channel=Veritasium).



1. Drawing a hexagon in p5
2. The grid method
3. The spiral method
4. The recursive subdivision method
5. The bravais method
6. Two offset triangular lattices
7. other methods
10. Beyond Hexagonal tesselations


<h2>Drawing a hexagon in p5</h2>

The first step we'd have to tackle, is actually drawing a hexagon in p5. I believe that the most sensible approach to doing so is by employing trigonometry:

<pre><code>
function drawHexagon(posX, posY, radius){
  beginShape()
  for(let a = 0; a < TAU; a+=TAU/6){
    var x = posX + radius * cos(a)
    var y = posY + radius * sin(a)

    vertex(x, y)
  }
  endShape(CLOSE)
}
</code></pre>

A regular hexagon can be best described as equiangular and equilateral (on wikipedia you can find more interesting properties https://en.wikipedia.org/wiki/Hexagon).

In radians, the value of a full rotation around a circle has a value of TAU, which is essentially 2 times PI (360 degrees). To construct a hexagon we can divide this full rotation into 6 equal parts (TAU/6), obtaining six 'equiangular' wedges (pizza slices). Given a specific radius, we can then place the 6 vertices at a certain distance from the centre point described by the coordinate pair posX and posY.

Passing the position and the radius information as input parameters to the function, we can then reuse it multiple times to construct a hexagonal grid. And for the remainder of the post we'll be relying on this little function to draw our hexagonal lattice.


<h2>The grid method</h2>

At first I wanted to call this the 'naive' way of constructing a hexagonal lattice, but I don't think it deserves to be labeled as such, since occasionally it has advantages over the following methods, depending on the sketch. I believe that the caveat here is thinking of a hexagonal lattice as a 'grid', when it is so much more than that!

So basically, we'll construct out lattice with a nested for loop, in the same manner that we would construct a rectangular grid:

<pre><code>function setup(){
  w = min(windowWidth, windowHeight)
  createCanvas(w, w)

  gridWidth = w
  gridHeight = w
  hexagonSize = w/10
}

function drawHexagon(posX, posY, radius){
  stroke(255)
  noFill()
  beginShape()
  for(let a = 0; a < TAU; a+=TAU/6){
    var x = posX + radius * cos(a)
    var y = posY + radius * sin(a)

    vertex(x, y)
  }
  endShape(CLOSE)
}

function makeGrid(){
  for(y = 0; y < gridWidth; y+=hexagonSize){
    for(x = 0; x < gridHeight; x+=hexagonSize){

      // divide hexagon size by two
      // since we need to pass in the radius and not the diameter
      drawHexagon(x,y,hexagonSize/2)
    }
  }
}

function draw(){
  background(0);

  makeGrid()

  noLoop()
}
</code></pre>

Ok, so we got a grid... but it doesn't look right at all! The hexagons are arranged, just as if they were sitting on top of a rectangular grid, which is how we programmed it. We'll have to add some stuff!

First let's space out this grid a little more, simply by multiplying the increment by 1.5:

<pre><code>for(x = 0; x < gridWidth; x+=hexagonSize*1.5){}
</code></pre>

Next we're going to offset every other row of hexagons. For this we need a count variable:

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

Here we're using a boolean trick to add a horizontal offset whenever the count variable is even. Looks a bit better but we're not there yet, we need to lessen the vertical distance between the rows:

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

And we end up with a neatly packed hexagonal grid!

<!--
First things first, to be able to perfectly align these hexagons we need to know to figure out a number. This number being the ratio of the distance of the hexagon centre to the midpoint of any edge, and the radius of the enclosing circle.
-->

<h2>The spiral method</h2>

There's a couple of ways to write this method:

<pre><code>// source: https://stackoverflow.com/a/43913544
function makeSpiral(centerX, centerY, size, count) {
    var x = centerX
    var y = centerY
    var angle = TAU / 6
    var i = 1
    var side = 0

    drawHexagon(x,y,size/1.75)
    count--;
    while (count > 0) {
        for (var t = 0; t < floor((side+4)/6)+(side%6==0) && count; t++) {
            y = y - size * cos(side * angle);
            x = x - size * sin(side * angle);
            drawHexagon(x, y, size/1.75);
            count--;
        }
        side++;
    }
}
</code></pre>

This is a bit heavy to digest, so let's go through it step by step!

What this chunk of code essentially does, is draw hexagons in an outward spiraling manner. In a sense, we're stepping around the initial hexagon that is located at the center of the canvas of this spiral. The tricky part is, that we need to draw increasingly more hexagons for each side of this growing spiral.

The main drawing portion consist of a for loop nested within a while loop, where the 'count' variable tells us how many hexagons are left to draw and is decremented within the loop, whenever we draw a hexagon. Try running the function while passing different values for 'count', you'll notice the order in which the hexagons are drawn, which is a spiral forming around the initial hexagon!

The magic happens within the condition of our for loop. We'll









Tiling interesting question: https://math.stackexchange.com/questions/4187829/can-one-always-tile-the-plane-with-a-regular-polygon-a-single-other-shape
