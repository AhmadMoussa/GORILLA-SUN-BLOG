---
title: How to make sliders in P5
author: Ahmad Moussa
categories:
  - p5js
description: Sliders are a wonderful tool to add some interactivity to your sketches. Here's everything you need to know.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

<h2>Adding sliders to your sketches</h2>
Creating a slider in p5js is straightforward:

<pre><code>let slider;
function setup(){
  slider = createSlider(0, 255, 100);
  slider.position(10, 10);
  slider.style('width', '80px');
}
</code></pre>

The createSlider function requires 4 input values: min, max, value and step.

<ul>
<li><strong>Min:</strong> the smallest value that the slider can represent, inasmuch when you drag the slider thumb all the way to the left.</li>
<li><strong>Max:</strong> the largest value that the slider can represent, inasmuch when you drag the slider thumb all the way to the right.</li>
<li><strong>Value:</strong> the default value of the slider, meaning the value that the slider will start with when you create it. Note, it should be in between the min and max value.</li>
<li><strong>Step:</strong> the value by which the slider is incremented or decremented when you drag it right or left. Basically it determines the spacing between ticks that the slider will snap to. Make sure that this value is smaller than the difference between the Min and Max value, otherwise the slider isn't usable. If set to 0 the slider will move continuously from left to right.</li>
</ul>

The two other lines underneath it specify it's position and it's style.

<h2>Getting slider values</h2>
Getting the slider value can be done as follows:

<pre><code>let val = slider.value();
</code></pre>

If done in the draw loop, the variable 'val' will always contain the value to which the slider is set. Here is an example sketch where we set the background color with the three sliders:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>

<script type="text/p5" data-p5-version="1.2.0" data-autoplay>
function setup() {
  createCanvas(windowWidth, windowHeight)
  slider1 = createSlider(0, 255, 127,1);
  slider1.position(10, windowHeight/4);
  slider1.style('width', '80px');
	
  slider2 = createSlider(0, 255, 127,1);
  slider2.position(10, windowHeight/2);
  slider2.style('width', '80px');
	
  slider3 = createSlider(0, 255, 127,2);
  slider3.position(10, windowHeight/4*3);
  slider3.style('width', '80px');
}

function draw() {
  let val1 = slider1.value();
  let val2 = slider2.value();
  let val3 = slider3.value();
  background(val1,val2,val3);
}
</script>
<p></p>
<h2>Styling your slider</h2>
The default slider style doesn't look very nice but we can easily remedy that by using Daniel Stern's awesome <a href='http://danielstern.ca/range.css/?ref=css-tricks#/'>website</a>. It lets you customize your sliders simply by dragging other sliders that specify different attributes like color and size of the various parts of your slider.

Then you simply copy paste the generated css code into the style.css file of your sketch that is referenced in the index.html of your sketch. Make sure that you add a class of your own for the generated css code:
<p></p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-08-06-How-to-make-Sliders-in-P5/sliderStyle1.png" alt="" /></span>
<p></p>
And to add the class to a specific slider you need to call this function:
<pre><code>slider.addClass("mySliderStyle");
</code></pre>

<h2>Styling your slider programmatically</h2>
However you can also style your slider entirely by using the style() function. Here's a great example by <a href='https://twitter.com/aparrish?lang=en'>Allison Parrish</a>:
<p></p>
<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="400" data-height="600">
let sliderN = 50;
function setup() {
  noCanvas();
  for (let i = 0; i < sliderN; i++) {
    let d = createDiv();
    d.style('padding-left: 50px;');
    d.style('transform-origin: 0 50% 0');
    d.style('transform: rotate(' + (((360 / sliderN) * i)) + 'deg);');
    d.position(200, 200);
    let s = createSlider(-1, 1, 0, 0);
    d.child(s);  
  }
}

function draw() {
  background(220);
}
</script>
	
The style function is super useful in this case, where it let's us set any style keyword directly.


<h2>Updating slider position on window Resize</h2>
When we want our sketches to be responsive we usually also include the windowResized() function, that triggers when the window containing our sketch is resized. The windowResized will usually look something like this:

<pre><code>function windowResized(){
	resizeCanvas(windowWidth, windowHeight);
}
</code></pre>

Inside the windowResized() function we're calling the resizeCanvas() function and giving it as inputs the new dimensions of the screen. If you were to do this, while there is a slider on your canvas that you created earlier, you'll notice that it's going to remain in it's previous position and not update along with the new screen dimensions (happens when you click the minimize button in your browser for example). You'll have to update the slider position manually, this involves deleting the slider and then creating it again:
<pre><code>let slider;

function makeSldr(){
	slider = createSlider(0, 255, 127,1);
  	slider.position(width-90, 10);
  	slider.style('width', '80px');
}

function setup(){
	createCanvas(windowWidth, windowHeight)
	makeSldr()
}

function windowResized(){
	resizeCanvas(windowWidth, windowHeight);
	slider.remove()
	makeSldr()
}

function draw(){
  background(0)
}
</code></pre>
<p></p>
Here we're removing the slider element from the DOM via the remove() function, and then re-adding it to the canvas with the helper function that we created. Try it out for yourself <a href='https://editor.p5js.org/AhmadMoussa/sketches/vInrssviE'>here</a> (try in present mode). Try commenting out the slider.remove() line and notice the difference.

<h2>Multiple Sliders</h2>
Sometimes you'll want to manage multiple parameters at the same time, with multiple sliders and you'd like them to stay in the right place when resizing the canvas. That can get very hairy very quickly. Another problem that we run into when removing the slider from the DOM and re-creating it is that it's default value is reset. This is not good, ideally we want to have the current position of the slider persist while resizing the canvas. One solution would be to create slider handler class in which we wrap the slider and can store the updated slider value:
<p></p>
<pre><code>let numSliders = 5;
let sliders = [];

function sliderHandler(minV, maxV, dflt, step, posX, posY, s){
    this.minV = minV;
    this.maxV = maxV;
    this.dflt = dflt;
    this.step = step;
  
    this.posX = posX;
    this.posY = posY;
    this.s = s;
  
  this.slider;
  this.updatePosition = function(x,y){
    this.posX = x;
    this.posY = y;
  }
  
  this.makeSlider = function(){
    this.slider = createSlider(this.minV, this.maxV, this.dflt, this.step);
    this.slider.position(this.posX, this.posY);
    this.slider.style('width', s+'px');
  }
  
  this.removeSlider = function(){
    this.slider.remove()
  }
}

function setup(){
	createCanvas(windowWidth, windowHeight)
	
    numSliders = 5;
    for(n = 0; n<numSliders; n++){
      sliders.push(new sliderHandler(0, 255, 127, 1, windowWidth-90, 20*n, 80))
      sliders[n].makeSlider();
    }
}

function windowResized(){
	resizeCanvas(windowWidth, windowHeight);
	for(n = 0; n<numSliders; n++){
      sliders[n].removeSlider();
      sliders[n].updatePosition(windowWidth-90,20*n);
      sliders[n].makeSlider();
    }
}

function draw(){
  background(0)
  for(n = 0; n<numSliders; n++){
    sliders[n].dflt = sliders[n].slider.value()
  }
}
</code></pre>

I'm not certain this is the best way to do it, but it does the job. If you have any other suggestions to improve this post, let me know in the comments! Otherwise cheers and happy sketching!
