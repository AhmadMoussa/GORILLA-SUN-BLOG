---
title: Rotation and positioning points on the circumference of a circle in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: This blog post explains how to rotate objects around a circle. It also discusses how to position items equidistantly around a circle.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

<div style="width:100%;height:0;padding-bottom:100%;position:relative;"><iframe src="https://giphy.com/embed/xUOwGnC7Jqlgj7FvSE" width="100%" height="100%" style="position:absolute; pointer-events:none;" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div>

<p>If you haven't already read my previous post on sine functions, go check it out after reading this one.</p>

<p>How to create circular motion should be another weapon in your creative coding arsenal, this blog post will explains how to achieve it in P5JS in a number of different ways. Starting with the simplest form: rotating a point around a circle.</p>

<p>To create a rotating point, we usually need 3 parameters: the center of the circle around which we are rotating, the radius, and the angle. The angle in our case will simply be a linearly increasing parameter, which is as always: Time.</p>


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
  // for what follows simply replace this with the provided snippet
  point(
        centerX + radius * sin(millis()/1000),
    centerY + radius * cos(millis()/1000)
  )   
}
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/Simple.gif" alt="" /></span>
<p></p>
<p>And this is kind of already the basis for a lot of other things that you can do. For example, what would you have to do to have a 3rd point rotate around the already rotating point? The snippet I provided already has the answer to it!</p>

<pre><code>//--------
  point(
        centerX + radius * sin(millis()/1000),
    centerY + radius * cos(millis()/1000)
  )
  stroke(255,0,255)
  point(
        centerX + radius * sin(millis()/1000) + radius/2 * sin(millis()/500),
    centerY + radius * cos(millis()/1000) + radius/2 * cos(millis()/500)
  )

</code></pre>

<p>You need to be mindful about the speed of the new point, here I'm dividing the new point's time by 500. If both were in sync we wouldn't observe an additional orbiting motion. Very simple, yet very powerful!<p>

<h2>Positioning items around a circle</h2>

<p>Let's go back a little and see how to position an arbitrary number of points around a circle in an equidistant manner. The circumference of a full circle can be expressed in either 2xPI RADIANS or 360 Degrees. Positioning an arbitrary number of points around a circle requires splitting 2xPI evenly among them.</p>

<pre><code>numPoints = 5
  for (i = 0; i < numPoints; i++) {
    point(
      centerX + radius * sin(2 * PI / numPoints * i + millis() / 1000),
      centerY + radius * cos(2 * PI / numPoints * i + millis() / 1000)
    )
  }

</code></pre>

<p>In this manner we simply divide 2xPI by the number of points and multiply by the number of the current point. If you prefer doing this in degrees, P5JS has a function that allows you to set this. This concept also applies if you want to position certain elements along the arc of a circle, for example fanning out a number of lines:</p>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/fan.png" alt="" /></span>

<pre><code>// ------
strokeWeight(2)
for(i = 0; i < 20; i++){
    line(centerX,centerY,
        centerX+100*sin(HALF_PI+HALF_PI/20*i),
         centerY+100*cos(HALF_PI+HALF_PI/20*i))
  }
</code></pre>

<p>As you can see P5JS also conveniently provides pre-defined constant that define different portions of PI (QUARTER_PI, HALF_PI, PI and TWO_PI).</p>

<h2>Rotation in the WEBGL mode</h2>

<p>Alternatively, you could simply achieve rotation by using the WEBGL mode that provides a conveninent rotate function. This is quite fun and very easy, but allows you to create incredible cool sketches.</p>



