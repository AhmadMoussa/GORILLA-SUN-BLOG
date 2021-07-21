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

As Tyler Hobbs discusses in his essay, there a number of choices that we have to consider now. For example, how we decide the starting points of our flow lines. For now we'll just choose them at random. We can do so by selecting a random GridAngle from our array, like so:

<pre><code>
GridAngle ga = grid.get((int)random(grid.size())).get((int)random(grid.size()));
</code></pre>

Then from that starting point the basic strategy will be to draw a line that follows the direction of the angles that fall directly underneath it. Similarly to how a river digs it's way through soil and earth. We do this incrementally, from our starting point we search for the nearest point in the grid and get it's angle, then extend our line in the direction of that angle and repeat.

<pre><code>
beginShape();
PVector vec = new PVector(ga.x + 1 * cos(ga.angle),
                          ga.y + 1 * sin(ga.angle));

curveVertex(vec.x, vec.y);
</code></pre>

Again, we'll make use of our good old friend the curveVertex() function to help us draw our flow lines. From the starting point that we selected at random from the grid we create a new vector using processing's class for it, and draw our initial point.

The next part, I'll be honest with you, is quite inefficient. And can be improved in multiple ways, but to keep it simple we'll first do it this way and then maybe improve it later. The inefficiency comes from finding the point that is closest to the current end of our line, the brute force way (which we're doing) is going through all points in the grid and finding the one that has the smallest distance to end of the flow line. For this process we'll need a couple of parameters:

<pre><code>
float minDist = 999999.9;
float dist = 0.0;
int nearestX = 0;
int nearestY = 0;
GridAngle tempFA;
</code></pre>

These'll make more sense in a second. We'll start by creating three loops, the first one specifying how many times we would like to extend our flowline (hence determining the overall length of it) and then a nested loop that goes over all the GridAngles in our ArrayList:

<pre><code>
for (int n = 0; n<50; n++) {
   for (int x = 0; x<grid.size(); x++) {
     for (int y = 0; y<grid.get(0).size(); y++) {
     }
   }
 }
</code></pre>

Next we want to find the closest point, that's where our previously declared variables come into play:

<pre><code>
for (int n = 0; n<50; n++) {
    for (int x = 0; x<grid.size(); x++) {
      for (int y = 0; y<grid.get(0).size(); y++) {
        tempFA = grid.get(x).get(y);
        dist = (float)dist(vec.x, vec.y, tempFA.x, tempFA.y);

        if (dist<minDist) {
          minDist = dist;
          nearestX = x;
          nearestY = y;
        }
      }
    }
  }
</code></pre>

Our nested loop will essentially go over all the points in the grid storing them in tempFA, then calculating the distance between that point and our start point / previous point that extended the flow line. If that distance is smaller than what is previously stored in the minDist variable, we'll overwrite that and also store the indices of that point in the array. This is quite expensive but allows us to find the closest point to the end of the line.

Once we've found that point we need to reset minDist to some large value and extend our flowline:
<pre><code>
minDist = 999999.9;
float angle = grid.get(nearestX).get(nearestY).angle;

vec = new PVector(vec.x + 2*cos(angle),
                 vec.y + 2*sin(angle));

curveVertex(vec.x, vec.y);
</code></pre>

Here it's super important that you overwrite the vec variable, since it'll have to serve as the starting point for the next iteration in the loop. And that's pretty much it, once you're out of the three loops, you just have to call endShape(); and it should have drawn a flowline. As you probably can see, there's a lot of room for improvement. But first let's play a little bit with the parameters and generate some flow fields!
