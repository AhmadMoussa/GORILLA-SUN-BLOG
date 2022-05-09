---
layout: default
title: Making of Behind the Canvas
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: false
exclude_rss: true
---
<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/banner2.png" alt="">
</span>

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/50.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/27.png" alt="">
		</span>
	</div>

  <div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/16.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/31.png" alt="">
		</span>
	</div>
</div>

You can check out the entire collection <a href='https://openprocessing.org/crayon/28'>here</a>. I think that there's a couple of cool things going on in the sketch, that from a technical and ideative point of view are worth inspecting a little bit in more detail:

1. <a href='#idea'>From initial idea to final sketch</a>
2. <a href='#opc'>Setting up our sketch with the OPC Configurator 3000</a>
3. <a href='#smooth'>Creating smooth Polygons</a>
4. <a href='#determinism'>Notes on preserving sketch determinism</a>
5. <a href='#resize'>Resizing and Rescaling</a>
6. <a href='#end'>Closing Notes</a>

<h2><a name='idea'></a>From initial idea to final sketch</h2>

<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/behind_the_canvas/cut.mp4" type="video/mp4">
  </video>
</div>




<h2>Inspiration</h2>


<h2>A little trigonometric refresher</h2>

If you have a good grasp of trigonometry and functions like sin() and cos(), feel free to skip this section. Otherwise, read on, as it will help me make the coming walkthrough a little less wordy.

Essentially, what you need to know about sin() and cos(), is that they're great for two things: positioning things around a circle, as well as making things oscillate, wobble and/or make things move from side to side. BUT, you need to tell them how to do it, by giving them some sort of input.

In the case of positioning things around a circle, we're essentially converting polar coordinates to cartesian coordinates. This means that given a radius and an angle, we can find it's equivalent x and y coordinates by making use of our two trigonometric functions. Generally it is done in this manner:

<pre><code>var x = radius * cos(angle);
var y = radius * sin(angle);
</code></pre>

In the case of oscillation, we can feed a linearly increasing parameter to sin() and obtain a value that goes back and forth between -1 and 1. In this manner, sin() maps the range [-PI/2, PI/2] to the range [-1, 1]. What happens when we input a value outside the range of [-PI/2, PI/2]? This is not a problem, and it will still work. It's simply as if we made an additional lap around the circle in clock-wise or counter-clockwise direction, which will result in an equivalent angle. And here's an example of this in action:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);

  for(let n = w/12; n < w-w/12; n+=10 ){
    strokeWeight(5)
    point(w/2 + sin(frameCount/50 + n/50)*w/8, n)
  }    
}
</script>
<p></p>

If you still want a longer version of this I have a post <a href='https://gorillasun.de/blog/Rotation-along-the-circumference-of-a-circle'>here</a>, which is in dire need of an overhaul at this point.


<h2>Modulating the width of a circle</h2>

Let's get started on the actual sketch! We'll start by making a simple circle, that is made out of other smaller circles:
<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

let radius = 100
function draw() {
  background(220);
  translate(w/2, w/2)

  for(let a = 0; a < TAU; a+=TAU/100){
    var x = radius * cos(a)
    var y = radius * sin(a)

    ellipse(x,y, 10)
  }
}
</script>
<p></p>

This doesn't look too nice, we can improve it a bit by removing the stroke, using a black fill and cranking up the number of circles:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

let radius = 100
function draw() {
  background(220);
  translate(w/2, w/2)

  noStroke()
  fill(0)

  for(let a = 0; a < TAU; a+=TAU/200){
    var x = radius * cos(a)
    var y = radius * sin(a)


    ellipse(x,y, 10)
  }
}
</script>
<p></p>

By increasing the number of circles that make up our main circle, we now have something that looks like a smooth circle with a very chunky stroke-weight/line-width. Since our circle is made up out of many smaller circles, we can now vary the size of each one of them based on their angular position, which is similar to Zach Lieberman's sketch which varied their size based on their vertical position on the canvas:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

let radius = 100
function draw() {
  background(220);
  translate(w/2, w/2)

  noStroke()
  fill(0)

  for(let a = 0; a < TAU; a+=TAU/300){
    var x = radius * cos(a)
    var y = radius * sin(a)

    ellipse(x,y, sin(a)*10)
  }
}
</script>
<p></p>

That doesn't look too great just yet, but hang on for a sec. Here the only change we did, is that we multiplied the circles size with sin(), into which we fed the angle at which the circle is positioned. Remember, the range of sin() is between 1 and -1 (when the angle ranges from half PI to negative half PI), and multiplying that value by 10, the size of the circle will be ranging between -10 and 10. What the ellipse function actually does when you give it a negative size, it will just take the absolute value and make it positive. That's why we get that odd shape in the previous example.

We can use this and obtain a variety of interesting shapes. Let's wrap our circle drawing code in a function and demonstrate this on a grid:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  pad = w/12
  gridDivs = 3
  gridSpacing = (w - pad*2)/gridDivs
}

function drawCirc(posX, posY, maxSize, mod){
  noStroke()
  fill(0)

  for(let a = 0; a < TAU; a+=TAU/300){
    var x = posX + maxSize * cos(a)
    var y = posY + maxSize * sin(a)

    ellipse(x,y, sin(a*mod)*10)
  }
}

let radius = 100
function draw() {
  background(220);

  var count = 1

  for(let x = 0; x < gridDivs; x++){
    for(let y = 0; y < gridDivs; y++){
      drawCirc(x * gridSpacing + pad + gridSpacing/2,
               y * gridSpacing + pad + gridSpacing/2,
               gridSpacing/3,
              count)

      count ++
    }
  }
}
</script>
<p></p>

The different circular shapes in the grid are obtained by multiplying the angular value that we feed into sin() by a multiplier.

<pre><code>sin(a*mod)*10
</code></pre>

Here is an example that uses the horizontal mouse coordinate:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function drawCirc(posX, posY, maxSize, mod){
  noStroke()
  fill(0)

  for(let a = 0; a < TAU; a+=TAU/500){
    var x = posX + maxSize * cos(a)
    var y = posY + maxSize * sin(a)

    ellipse(x,y, sin(a*mod)*w/20)
  }
}

let radius = 100
function draw() {
  background(220);

  drawCirc(w/2,
           w/2,
          w/4.5,
          int(map(mouseX,0,w,1,10)))
}
</script>
<p></p>

The important thing here is that we only multiply the angle by whole numbers, which ensures that it will align perfectly.
