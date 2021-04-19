---
title: Detecting mouse location within different sectors and parts of a circle
author: Ahmad Moussa
categories:
  - p5js
description: Detecting if a point is in specific region within a circle is tricky. In this blog post I go over the different possibilities and tricks to detect the mouse.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

Detecting if a point is within the circumference of a circle is pretty straightforward, we simply have to check that the distance between the center of the circle and the point is less than or equal to the radius:

<pre><code>
var dist = pow((mouseX - this.p1x), 2) / pow(this.p2x[1] / 2, 2) +
      pow((mouseY - this.p1y), 2) / pow(this.p2y[1] / 2, 2)
</code></pre>

Now what if we would like to check if the point was in a specific slice of the circle, slice as in slice of a pizza or pie. This is a bit trickier, we'll have to check that angle formed by the vector CP is within the angle formed by the slice. This takes a number of steps, where we assume that we have the angle of the slice.

First, get the angle formed by the line between the point and the center:

<pre><code>// andgle between lines formed by the coordinates (a, b) -> (d, p) and (p, q) -> (r, s)
getAngle(a, b, c, d, p, q, r, s){
    //find vector components
    var dAx = c - a;
    var dAy = d - b;
    var dBx = r - p;
    var dBy = s - q;
    var angle = Math.atan2(dAx * dBy - dAy * dBx, dAx * dBx + dAy * dBy);
    //if(angle < 0) {angle = angle * -1;} 
    //In our case we actually want the sign of the angle
    var degree_angle = angle * (180 / Math.PI);
    
    return angle
  }
</code></pre>

Here we exploit the atan2 functionwhich gives you the angle between two vectors. The atan2 function has become a faeture of many programming languages as it usefully also gives you the sign of the angle, this helps determine in which quadrant the angle is. Calculating the atan2 function from scratch however is a little bit complicated.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/atan2
<blockquote>
The Math.atan2() method returns a numeric value between -π and π representing the angle theta of an (x, y) point. This is the counterclockwise angle, measured in radians, between the positive X axis, and the point (x, y). Note that the arguments to this function pass the y-coordinate first and the x-coordinate second.
</blockquote>


