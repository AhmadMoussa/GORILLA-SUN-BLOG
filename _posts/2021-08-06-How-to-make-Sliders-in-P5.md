---
title: How to make sliders in P5
author: Ahmad Moussa
categories:
  - p5js
description: Sliders are a wonderful tool to add some interactivity to your sketches. Here's everything you need to know.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

<h2>Adding a slider to your sketch</h2>
Making a slider is straightforward:


<pre><code>let slider;
function setup(){
  slider = createSlider(0, 255, 100);
  slider.position(10, 10);
  slider.style('width', '80px');
}
</code></pre>

The createSlider function requires 4 input values: min, max, value and step.

<ul>
<li>Min: the smallest value that the slider can represent, inasmuch when you drag the slider thumb all the way to the left.</li>
<li>Mas: the largest value that the slider can represent, inasmuch when you drag the slider thumb all the way to the right.</li>
<li>Value: the default value of the slider, meaning the value that the slider will start with when you create it. Note, it should be in between the min and max value</li>
<li>Step: the value by which the slider is incremented or decremented when you drag it right or left. Basically it determines the spacing between ticks that the slider will snap to. Make sure that this value is smaller than the difference between the Min and Max value, otherwise the slider isn't usable. If set to 0 the slider will move continuously from left to right.</li>
</ul>



<h2>Getting the slider value</h2>
Getting the slider value can be done as follows:

<pre><code>
let val = slider.value();
</code></pre>

If done in the draw loop, the variable 'val' will always contain the value to which the slider is set. Here are some examples of sliders:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>

<script type="text/p5" data-p5-version="1.2.0" data-autoplay>
let slider;
function setup() {
  slider = createSlider(0, 255, 100,50);
  slider.position(10, 10);
  slider.style('width', '80px');
	
  slider2 = createSlider(0, 1, 0.5,0.1);
  slider2.position(10, 20);
  slider2.style('width', '80px');
	
  slider3 = createSlider(0, 100, 50,10);
  slider3.position(10, 30);
  slider3.style('width', '80px');
}

function draw() {
  let val = slider.value();
  background(val);
}
</script>


<h2>Styling your slider</h2>
The default slider style doesn't look very nice but 


<h2>Updating slider position on window Resize</h2>

<h2>Multiple Sliders</h2>
