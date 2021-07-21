---
title: Perlin Noise Flow Fields in Processing - Part II
author: Ahmad Moussa
categories:
  - p5js
description: Part 2 of creating perlin noise flow fields in Processing.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

In this part of the tutorial we'll tackle drawing the flow lines that go through the flow field.

1. <a href='#start'>Selecting a starting Point</a>
2. <a href='#algo'>Flow Line Algorithm</a>
3. <a href='#draw'>Drawing the Flow-Field</a>
4. <a href='#hp'>Choosing Hyper-Parameters</a>

<h2><a name='start'>Selecting a starting Point</a></h2>
As Tyler Hobbs discusses in his essay, there a number of choices that we have to consider now. For example, how we decide the starting points of our flow lines. For now we'll just choose them at random. We can do so by selecting a random GridAngle from our array, like so:

<pre><code>int x = (int)random(grid.size());
y = (int)random(grid.size());
GridAngle ga = grid.get(x).get(y);
</code></pre>

Alternatively we could also iterate over each grid angle and draw a respective flow line.

<h2><a name='algo'>Flow Line Algorithm</a></h2>

Then from that starting point the basic strategy will be to draw a line that follows the direction of the angles that fall directly underneath it. Similarly to how a river digs it's way through soil and earth. We do this incrementally, from our starting point we search for the nearest point in the grid and get it's angle, then extend our line in the direction of that angle and repeat.

<pre><code>beginShape();
PVector vec = ga.v.copy()
curveVertex(vec.x, vec.y);
</code></pre>

Again, we'll make use of our good old friend the curveVertex() function to help us draw our flow lines. We take the angle from the starting point we selected, and store it in a new variable called vec, we'll extend our line from here.

The next part, I'll be honest with you, is quite inefficient. And can be improved in multiple ways, but to keep it simple we'll first do it this way and then maybe improve it later. The inefficiency comes from finding the point that is closest to the current end of our line, the brute force way (which we're doing) is going through all points in the grid and finding the one that has the smallest distance to end of the flow line. For this process we'll need a couple of parameters:

<pre><code>// Used to check for the closest point in the grid
float minDist = Float.MAX_VALUE;
float dist = 0.0;

// indices of closest point in the grid
int nearestX = 0;
int nearestY = 0;

// stores closest point in grid
GridAngle tempGA;

// stylistic parameters controlling the line length
int lineLength = 50;
float segmentLength = 2;
</code></pre>

These'll make more sense in a second. We'll start by creating three loops, the first one specifying how many times we would like to extend our flowline (hence determining the overall length of it) and then a nested loop that goes over all the GridAngles in our ArrayList:

<pre><code>for (int n = 0; n&lt;lineLength; n++) {
   for (int x = 0; x&lt;grid.size(); x++) {
     for (int y = 0; y&lt;grid.get(0).size(); y++) {
     }
   }
 }
</code></pre>

Next we want to find the closest point, that's where our previously declared variables come into play:

<pre><code>for (int n = 0; n&lt;lineLength; n++) {
    for (int x = 0; x&lt;grid.size(); x++) {
      for (int y = 0; y&lt;grid.get(0).size(); y++) {
        tempGA = grid.get(x).get(y);
        dist = (float)dist(vec.x, vec.y, tempGA.x, tempGA.y);

        if (dist &lt; minDist) {
          minDist = dist;
          nearestX = x;
          nearestY = y;
        }
      }
    }
  }
</code></pre>

Our nested loop will essentially go over all the points in the grid storing them in tempGA, then calculating the distance between that point and our start point / previous point that extended the flow line. If that distance is smaller than what is previously stored in the minDist variable, we'll overwrite that and also store the indices of that point in the array. This is quite expensive but allows us to find the closest point to the end of the line.

Once we've found that point we need to reset minDist to some large value and extend our flowline. Float.MAX_VALUE essentially sets the variable minDist to the largest possible value that fits into a float, we could equivalently set it to some number like 99999.9, the only thing that matters here is that it's larger than the most distant point in the grid:
<pre><code>minDist = Float.MAX_VALUE;
float angle = grid.get(nearestX).get(nearestY).angle;

vec = new PVector(vec.x + segmentLength*cos(angle),
                 vec.y + segmentLength*sin(angle));

curveVertex(vec.x, vec.y);
</code></pre>

Here it's super important that you overwrite the vec variable, since it'll have to serve as the starting point for the next iteration in the loop. And that's pretty much it, once you're out of the three loops, you just have to call endShape(); and it should have drawn a flowline. 

As you probably can see, there's a lot of room for improvement, but first let's draw the Flow-Field and see what it looks like!

<h2><a name='hp'>Drawing the Flow-Field</a></h2>
Everything discussed above can be neatly combined as a function:

<pre><code>// stylistic parameters controlling the line length
int lineLength = 50;
float segmentLength = 2;

void drawFlowLine() {
  //random starting point
  GridAngle ga = grid.get((int)random(grid.size())).get((int)random(grid.size()));
  
  beginShape();
  PVector vec = ga.v;
  curveVertex(vec.x, vec.y);

  // Used to check for the closest point in the grid
  float minDist = Float.MAX_VALUE;
  float dist = 0.0;

  // indices of closest point in the grid
  int nearestX = 0;
  int nearestY = 0;

  // stores closest point in grid
  GridAngle tempGA;

  for (int n = 0; n<lineLength; n++) {
    for (int x = 0; x&lt;grid.size(); x++) {
      for (int y = 0; y&lt;grid.get(0).size(); y++) {
        tempGA = grid.get(x).get(y);
        dist = (float)dist(vec.x, vec.y, tempGA.x, tempGA.y);

        if (dist&lt;minDist) {
          minDist = dist;
          nearestX = x;
          nearestY = y;
        }
      }
    }

    minDist = Float.MAX_VALUE;
    float angle = grid.get(nearestX).get(nearestY).angle;

    vec = new PVector(vec.x + segmentLength*cos(angle),
                      vec.y + segmentLength*sin(angle));
    curveVertex(vec.x, vec.y);

    if ((float)random(1.0)>1) {
      endShape();
      beginShape();
    }
  }
  endShape();
}
</code></pre>

Now to actually draw the Flow Field we need to call this function in the draw loop, which is going to end up looking something like this:

<pre><code>final int numLines = 200; //number of flow lines we'll draw
void draw() {
  background(220);
  // We don't need to draw the grid anymore
  /*
  for (int x = 0; x<grid.size(); x++) {
    for (int y = 0; y<grid.get(0).size(); y++) {
      grid.get(x).get(y).display();
    }
  }
  */
  
  for (int n = 0; n < numLines; n++) {
    drawFlowLine();
  }
  
  noLoop(); // noneed to loop
}
</code></pre>

And we'll obtain a flow field like this:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/flow.png" alt="" /></span>

This doesn't look very nice, we can do much better by playing a little bit with the parameters and generating some more flow fields!

<h2><a name='hp'>Choosing Hyper-Parameters</a></h2>
One last thing we want to do, to make it look nice, is span the grid of angles such that it begins outside of the canvas and ends outside of the canvas. For this we can simply modify the loops in the createGrid function as such:

<pre><code>void createGrid() {
  for (int x = -xOff; x&lt;width+xOff; x+=spacing) {
    for (int y = -yOff; y&lt;width+yOff; y+=spacing) {
    }
  }
}
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/flow2.png" alt="" /></span>

Next, let's crank up the number of flowlines with the numLine parameter to 500, 1000, 2000 and 5000 respectively:

<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f500.png" alt="" /></span>
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f1000.png" alt="" /></span>
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f2000.png" alt="" /></span>
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000.png" alt="" /></span>
      </div>
</div>

The lines are too squiggly for my taste, we can fix that by reducing the rez parameter that controls overall angles of the grid given by the Perlin Noise. Reducing it to 0.003 and 0.001 makes it look a lot less jagged:

<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez003.png" alt="" /></span>
       
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez001.png" alt="" /></span>
      </div>
</div>

Also reducing the the spacing between the grid angles will make it look a lot more dense. A spacing of 10 and 5 respectively (note that this makes it way slower since there's more grid angles it has to check):


<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez001sp10.png" alt="" /></span>
       
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez001sp5.png" alt="" /></span>
      </div>
</div>


We could also control the length by which the line gets extended each iteration. A length of 5 and 1 respectively:

<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez001sp5sl5.png" alt="" /></span>
       
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-07-13-Perlin-Noise-Flow-Fields-in-Processing-Part-II/f5000rez001sp5sl1.png" alt="" /></span>
      </div>
</div>


And that's just some of the results you can get with the code we wrote. Feel free to mess around, modify, deconstruct the code in any way, shape or form you like, and send your results my way! Hope you enjoyed! And maybe share it with a friend, it helps a lot!





