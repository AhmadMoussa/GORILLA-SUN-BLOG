---
title: Continuous oscillating motion in P5JS with Sine functions
author: Ahmad Moussa
description: Creating continuous oscillating motion in P5JS can be done via sine functions, in this blog post I explain how it's done and how it can be intuitively understood.
thumbnail_path: 2021-02-06-Continuous-oscillating-motion-in-P5JS-with-Sine-functions.png
---
<span class="image left"><img src="https://gorillasun.de/thumbnails/2021-02-06-Continuous-oscillating-motion-in-P5JS-with-Sine-functions.png" alt="" /></span>

<p>This time around we're going to look at how to create continuous motion in our animation. In this case the sine function is our best friend. I remember studying trigonometric functions in school, and having the worst time of my life. I didn't get them, I hated them and they had no practical purpose for me in real life. But it's kind of funny that I've come around to loving them, almost eight years after having been introduced to them for the first time.</p>

<p>In mathematics the Sine function of an angle is defined as the ratio of the opposite side of an angle to it's hypotenuse. And it's exactly this definition that I hated so much in school. It doesn't really show you all the wonderful things that you can do with the Sine function, it's just a bland non-descriptive book definition. In the next part I'll explain why the Sine function is so great and how I like to think about it.</p>


<hr class="major" />

<h3>The Sine function is a conversion of linear motion into oscillating motion</h3>
<p>The Sine function has two awesome properties. Firstly it is bounded by the interval [-1,1], and secondly, we can use it to convert a linear movement into an oscillating movement. You'll get what I mean with this in a second. Remember the definition of the Sine function 'The Sine function of an angle is defined as the ratio of the opposite side of an angle to it's hypotenuse' yada yada, for creative coding, the interesting property of this trigonometric function comes when you add a linearly increasing parameter to the mix. The simplest form of such a parameter that you can find in probably every programming language is time. In P5JS you can get the current time since the moment of starting the rogramming by invoking the millis() function. This is a constantly increasing value. Plug this into the Sine function and you'll obtain a radius that is sweeping around the circle.</p>

<!--
<iframe style="width: 100%; height: 50vh;"  scrolling="no" frameborder="0" src="https://editor.p5js.org/AhmadMoussa/embed/RwgHRtHtA"></iframe>
-->
<div class="row gtr-200">
			<div class="col-6 col-12-medium"><span class="image fit"><img src="https://gorillasun.de/out.gif" alt="" /></span></div>
			<div class="col-6 col-12-medium"><p> The exact code for this is a little bit more complicated </p>
	<code>function setup() {
  createCanvas(400, 400);
  speed = 100
}

function draw() {
  background(220);
  strokeWeight(2)
  stroke(0)
  ellipse(200, 200, 200)
  stroke(255, 0, 0)
  line(200, 200, sin(radians(millis()) / speed) * 100 + 200, 200)
  stroke(0)
  line(200, 200, sin(radians(millis()) / speed) * 100 + 200, cos(radians(millis()) / speed) * 100 + 200)
  line(sin(radians(millis()) / speed) * 100 + 200, cos(radians(millis()) / speed) * 100 + 200, sin(radians(millis()) / speed) * 100 + 200, 200)
  strokeWeight(10)
}</code></div>
		</div>



<hr class="major" />



<span class="image fit"><img src="https://gorillasun.de/out2.gif" alt="" /></span>

<h2></h2>

<h2></h2>
<p> </p>
