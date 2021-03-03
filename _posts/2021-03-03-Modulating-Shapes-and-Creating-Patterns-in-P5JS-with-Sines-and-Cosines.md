---
title: Modulating Shapes and Creating Patterns in P5JS with Sines and Cosines
author: Ahmad Moussa
categories:
  - p5js
description: Modulating the height and width of rectangle objects, arranged in a grid-like manner, with respect to their x and y coordinates allows you to create interesting patterns, this blog post explains how to achieve this effect.
thumbnail_path: 2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines.png
published: true
---
<div class="box alt">
    <div class="row gtr-50 gtr-uniform">
        <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionX.gif" alt="" /></span></div>
        <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionY.gif" alt="" /></span></div>
        <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionXnY.gif" alt="" /></span></div>
    </div>
</div>
<h2>Continuously evolving your sketches</h2>
<p>My very first post on this website explains how to create a arbitrarily sized grid of shapes, which I think teaches a very important concept, which is 'instancing'. Thinking in an object oriented manner can go a long way when creating sketches, and can lead to having code that follows a modular structure.</p> 

<p>Once you code up a working sketch with such a structure, cimply changing one parameter can lead to an entirely different behaviour. At that point you're not really 'coding' your sketch anymore, but you're 'playing' with it.</p>

<h2>Sketch from Scratch</h2>
<p>In this blog post specifically I'd like to explain what's going on in this Sketch:</p>

<iframe width="100%" height="315" src="https://www.youtube.com/embed/f0RLlSmxfK8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p>It might not be very obvious, but the secret ingredient behind the motion in this sketch are trigonometric functions. I talk a little about trigonometric functions in <a href='https://gorillasun.de/blog/Continuous-oscillating-motion-in-P5JS-with-Sine-functions' target="_blank" rel="noopener noreferrer">this previous blog post</a>.</p>

<h2>Intuition behind the Sketch</h2>
<p>Essentially, we're creating a grid of objects, each of which holds a number of parameters and <code>display()</code> function to draw itself to the canvas. The parameters include it's position and size, and optionally also it's color. The heavy lifting happens in the <code>display()</code> function. Since every rectangle is continuously redrawn, what if we were to change their size and color over time? We could simply do so by modulating the size attribute with a sine function. But first things first, let's create the grid.</p>

<h3>The grid</h3>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/grid.png" alt="" /></span>

<p>For the sake of clarity I'll brush over the code for creating the grid, however a more detailed explanation can be found <a href='https://gorillasun.de/blog/Sketch-from-Scratch-1-Colorful-checkerboard-in-P5JS' target="_blank" rel="noopener noreferrer">here</a>. This snippet, that constitutes the largest part of the code, creates a 'Grid' object which holds an array of 'Square' objects. Invoking the display function of the Grid object will in turn call all the display functions of each Square object and draw them to the canvas, depending on it's position. The grid object takes care of positioning the Squares such that you can create grids with an arbitrary number of rows and columns:</p>
<pre><code>class Square {
  constructor(px, py, s) {
    this.positionX = px;
    this.positionY = py;
    this.curvature = 1;
    this.size = s;
    this.c = 0;
  }
  
  display() {
    strokeWeight(3)
    rect(this.positionX - this.size / 2, this.positionY - this.size / 2,
          this.size, this.size, this.curvature)
  }
}

class SquareGrid {
  constructor() {
    this.squares = []
    this.gridWidth = 20;
    this.gridHeight = 5;
    this.squareSize = 20;
    this.spacing = 24;
    this.positionX = width / 2 - ((this.gridWidth - 1) * (this.spacing)) / 2;
    this.positionY = height / 2 - ((this.gridHeight - 1) * (this.spacing)) / 2;
    for (let i = 0; i < this.gridWidth; i++) {
      let row = []
      for (let j = 0; j < this.gridHeight; j++) {
        row.push(
          new Square((this.positionX + this.spacing * i), (this.positionY + this.spacing * j), this.squareSize)
        )
      }
      this.squares.push(row)
    }
  }

  display() {
    for (let i = 0; i < this.gridWidth; i++) {
      for (let j = 0; j < this.gridHeight; j++) {
        this.squares[i][j].display()
      }
    }
  }
}

function setup() {
  createCanvas(500, 200);
  grid = new SquareGrid()
}


function draw() {
  background(255);
  grid.display();  
}
</code></pre>
<p></p>
<h2>Modulating the Rectangle's size</h2>
<p>As I already mentioned, since the rectangles are continuously redrawn, it would be interesting to plug something more interesting into it's 'this.size' attribute, rather than just a fixed number. For example, a sine function! We also need the <code>millis()</code> function that p5js provides to get motion out of the <code>sin()</code> function. The sine function now start oscillating between it's two bounds, that are -1 and 1, and we need to scale it accordingly to obtain any visual change, in this case I just multiply it by 14, which is adequate for the canvas and rectangle size we are using. You might have to change this number if you change the canvas size and the overall size of you drawn objects. Additionally I divide <code>millis()</code> by a value, which essentially just slows down the osciallting movement of the sine function. Otherwise it would be nauseatingly fast.</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/modulatingSize.gif" alt="" /></span>
<pre><code>
class Square {
  constructor(px, py, s) {
    this.positionX = px;
    this.positionY = py;
    this.curvature = 1;
    this.size = s;
    this.c = 0;
  }
  
  display() {
    strokeWeight(3);
    
    // This Line is all that changed
    this.size = 20 + 14 * sin(millis() / 500);
    rect(this.positionX - this.size / 2, this.positionY - this.size / 2, this.size, this.size, this.curvature, this.curvature, this.curvature, this.curvature);
  }
}
</code></pre>
<p></p>
<h3>Sine function with respect to x coordinate</h3>
<p>Here's where things become interesting, what if we were to plug in another attribute into the sine function? For instance, the x coordinate of the drawn object? This would offset the sine function by some amount, and the size of each one of the rectangles wouldn't uniformly increasing and decreasing. Here you can see the effect:</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionX.gif" alt="" /></span>
<pre><code>display() {
    strokeWeight(3);
    
    // This Line is all that changed
    this.size = 20 + 14 * sin(this.positionX + millis() / 500);
    rect(this.positionX - this.size / 2, this.positionY - this.size / 2, this.size, this.size, this.curvature, this.curvature, this.curvature, this.curvature);
}
</code></pre>
<p></p>
<h3>Sine function with respect to y coordinate</h3>
<p>What about the Y coordinate?</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionY.gif" alt="" /></span>
<pre><code>this.size = 20 + 14 * sin(this.positionY + millis() / 500);</code></pre>
<p></p>
<h4>Sine function with respect to x and y coordinate</h4>
<p>What if we were to use both X and Y coordinates?</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/positionXnY.gif" alt="" /></span>
<pre><code>this.size = 20 + 14 * sin(this.positionY +this.positionX + millis() / 500);</code></pre>
<p></p>
<h3>Adding Color</h3>
<p>Now we can apply this same concept to the color of each object. And we're not just limited to the sine function, we could alternatively use a cosine function or a combination of the two. The possibilities really are endless!</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines/color.gif" alt="" /></span>
<pre><code>fill(127.5 + 127.5 * cos(this.positionX + tan(millis() / 5000) + millis() / 500), 127.5 + 127.5 * sin(this.positionY + cos(millis() / 500) + millis() / 500), 120.5)</code></pre>

<!--
<div style="width:500px; height:200px; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;">
<iframe style="width:100%; height:100%; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;" src="https://editor.p5js.org/AhmadMoussa/embed/HaWoY9swq"></iframe>
</div>
-->

<p>If you enjoyed this article, consider subscribing to my mailing list, or if you'd like to support this website, consider sharing this article.</p>
