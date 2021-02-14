---
title: Continuous oscillating motion in P5JS with Sine functions
author: Ahmad Moussa
categories:
  - ableton
description: Creating continuous oscillating motion in P5JS can be done via sine functions, in this blog post I explain how it's done and how it can be intuitively understood.
thumbnail_path: 2021-02-06-Continuous-oscillating-motion-in-P5JS-with-Sine-functions.png
---
<span class="image left"><img src="https://gorillasun.de/out.gif" alt="" /></span>

<p> Everybody hates trigonometry (at least at some point throughout their life), but if you want to add motion to your creative coding sketches, then there's no way around it. And trigonometry is not just useful for that, understanding some of the concept intuiteively could go a long way in improving your problem solving skills. I remember studying trigonometric functions in school, and having the worst time of my life. I didn't get them, I hated them and they had no practical purpose for me in real life. But it's kind of funny that I've come around to loving them, almost eight years after having been introduced to them for the first time.</p>

<p>In mathematics the Sine function of an angle is defined as the ratio of the opposite side of an angle to it's hypotenuse. And it's exactly this definition that I hated so much in school. It doesn't really show you all the wonderful things that you can do with the Sine function, it's just a bland non-descriptive book definition. In the next part I'll explain why the Sine function is so great and how I like to think about it.</p>


<hr class="major" />

<h3>The Sine function is a conversion of linear motion into oscillating motion</h3>
<p>The Sine function has two awesome properties. Firstly it is bounded by the interval [-1,1], and secondly, we can use it to convert a linear movement into an oscillating movement. You'll get what I mean with this in a second. Remember the definition of the Sine function 'The Sine function of an angle is defined as the ratio of the opposite side of an angle to it's hypotenuse' yada yada, for creative coding, the interesting property of this trigonometric function comes when you add a linearly increasing parameter to the mix. The simplest form of such a parameter that you can find in probably every programming language is time. In P5JS you can get the current time since the moment of starting the rogramming by invoking the millis() function. This is a constantly increasing value. Plug this into the Sine function and you'll obtain a radius that is sweeping around the circle. The animated gif below exemplifies this, and the snippet of code is what creates the animation. It looks a little messy but I'll explain in detail in what follows.</p>

<!--
<iframe style="width: 100%; height: 50vh;"  scrolling="no" frameborder="0" src="https://editor.p5js.org/AhmadMoussa/embed/RwgHRtHtA"></iframe>
-->
<div class="row gtr-200">
			<div class="col-6 col-12-medium"><span class="image fit"><img src="https://gorillasun.de/out.gif" alt="" /></span></div>
			<div class="col-6 col-12-medium"><p> The exact code for this is a little bit more complicated </p>
	<pre><code>function setup() {
  createCanvas(400, 400);
  background(220);
}

speed = 4000
function draw() {
  ellipse(200, 200, 200)
  stroke(255, 0, 0)
  line(200, 200, sin(millis() / speed) * 100 + 200, 200)
  stroke(0)
  line(200, 200, sin(millis() / speed) * 100 + 200, cos(millis() / speed) * 100 + 200)
  line(sin(millis() / speed) * 100 + 200, cos(millis() / speed) * 100 + 200, sin(millis()/ speed) * 100 + 200, 200)
}</code></pre></div>
		</div>
		
<p> The red line that is contracting and expanding over time is drawn by the following command <code>line(200, 200, sin(radians(millis()) / speed) * 100 + 200, 200)</code>. The P5JS line function takes 4 arguments, the x and y coordinates of the start point and the x and y coordinates of the end point of the line. By replacing the x coordinate of the end point by a sine function we can obtain this oscillating motion. Obviously, as stated earlier we need to give some input to the sine function, which will essentially be a function of time. We call the <code>millis()</code> function to get the current time and divide it by a speed parameter. The speed parameter is used to slow down the movement so that the sweeping radius doesn't go haywire (try running the sketch without dividing by speed). Then, the last thing you still need to do, is to just wrap the result in the already inbuilt <code>sin()</code> function and scale it appropriately such that it appears in the correct location on the canvas (which means multiply by the radius of the circle and translate it to the center of the circle).</p>

<p>You can also see two other lines, the sweeping radius and the line opposite to the angle formed by the radius and the red line, which is essentially the Sine. The rest of the code snippet show how to draw them.</p>
<hr class="major" />

<h2>How to use this in context</h2>
You see this is awesome, because it allows us to add a lot of interesting motion to our P5JS sketches (of course it's not limited to only P5JS). For example we can draw some points and have them move in a wavy pattern as you can see in the next animated gif. 

<span class="image fit"><img src="https://gorillasun.de/out2.gif" alt="" /></span>

<p>We create a number of points in a for loop and modulate their y coordinate with a sine function which will be parametrized by their position in addition to time. This yields an oscillating wave shaped string of dots.</p>
<pre><code>
function setup() {
  createCanvas(800, 400);
  speed = 2000
}

function draw() {
  background(220);
  strokeWeight(2)
  ellipse(200, 200, 200)
  stroke(255, 0, 0)
  line(200, 200, sin(millis() / speed) * 100 + 200, 200)
  stroke(0)
  line(200, 200, sin(millis() / speed) * 100 + 200, cos(millis() / speed) * 100 + 200)
  line(sin(millis() / speed) * 100 + 200, cos(millis() / speed) * 100 + 200, sin(millis() / speed) * 100 + 200, 200)
  strokeWeight(4)

  for (i = 0; i < 100; i++) {
    point((i * 4 + 400) % 400 + 350, sin(i/10+millis() / speed)*80 + 200)
  }
}
</code></pre>

<h2></h2>
<p> </p>
