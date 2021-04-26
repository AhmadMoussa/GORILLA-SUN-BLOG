---
title: Rotation and positioning points on the circumference of a circle in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: This blog post explains how to rotate objects around a circle. It also discusses how to position items equidistantly around a circle.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

If you haven't alraedy read my previous post on sine functions, go check it out after reading this one.

How to create circular motion should be another weapon in your creative coding arsenal, this blog post will explains how to achieve it in P5JS in a number of different ways. Starting with the simplest form: rotating a point around a circle.

To create a rotating point, we usually need 3 parameters: the center of the circle around which we are rotating, the radius, and the angle. The angle in our case will simply be a linearly increasing parameter, which is as always: Time.


<pre><code>
function setup() {
  createCanvas(400, 400);
  centerX = width/2
  centerY = height/2
  radius = 100
}

function draw() {
  background(220);
  stroke(0)
  point(centerX, centerY)
  strokeWeight(10)
  stroke(255,0,0)
  point(
        centerX + radius * sin(millis()/1000),
    centerY + radius * cos(millis()/1000)
  )   
}
</pre></code>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/Simple.png" alt="" /></span>

And this is kind of already the basis for a lot of other things that you can do. For example, what would you have to do to have a 3rd point rotate around the already rotating point? The snippet I provided already has the answer to it!

<pre><code>
function setup() {
  createCanvas(400, 400);
  centerX = width/2
  centerY = height/2
  radius = 100
}

function draw() {
  background(220);
  stroke(0)
  point(centerX, centerY)
  strokeWeight(10)
  stroke(255,0,0)
  point(
        centerX + radius * sin(millis()/1000),
    centerY + radius * cos(millis()/1000)
  )
  stroke(255,0,255)
  point(
        centerX + radius * sin(millis()/1000) + radius/2 * sin(millis()/500),
    centerY + radius * cos(millis()/1000) + radius/2 * cos(millis()/500)
  )
}
</pre></code>

Very simple, yet very powerful!

<h2>Positioning items around a circle</h2>

Let's go back a little and see how to position an arbitrary number of points around a circle in an equidistant manner. The circumference of a full circle can be expressed in either 2xPI RADIANS or 360 Degrees. Positioning an arbitrary number of points around a circle requires splitting 2xPI evenly among them.
<pre><code>function setup() {
  createCanvas(400, 400);
  centerX = width / 2
  centerY = height / 2
  radius = 100
}

function draw() {
  background(220);
  stroke(0)
  point(centerX, centerY)
  strokeWeight(10)
  stroke(255, 0, 0)
  numPoints = 5
  for (i = 0; i < numPoints; i++) {
    point(
      centerX + radius * sin(2 * PI / numPoints * i + millis() / 1000),
      centerY + radius * cos(2 * PI / numPoints * i + millis() / 1000)
    )
  }
  stroke(255, 0, 255)
}
</pre></code>
In this manner we simply divide 2xPI by the number of points and multiply by the number of the current point. If you prefer doing this in degrees, P5JS has a function that allows you to set this.

<h2>Rotation in the WEBGL mode</h2>

Alternatively, you could simply achieve rotation by using the WEBGL mode that provides a conveninent rotate function. This is quite fun and very easy, but allows you to create incredible cool sketches.



