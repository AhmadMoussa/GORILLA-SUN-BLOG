---
title: Modulating Shapes and Creating Patterns in P5JS with Sines and Cosines
author: Ahmad Moussa
categories:
  - p5js
description: Modulating the height and width of rectangle objects, with respect to their x and y coordinates allows you to create interesting patterns, this blog post explains how to achieve this effect.
thumbnail_path: 2021-03-03-Modulating-Shapes-and-Creating-Patterns-in-P5JS-with-Sines-and-Cosines.png
published: true
---

<h2>Continuously evolving your sketches</h2>
<p>As of now, I've already written a couple of blog posts about creating sketches with P5JS which detail a number of interesting techniques to achieve interesting animated effects, and I don't think I'm going to run out of ideas anytime soon. My very first post talks about creating a grid of shapes, which I think teaches a very important concept, that is 'instancing'. Thinking in an object oriented manner can go a long way when creating sketches sketches, and can lead to a sort of modular structure for your code.</p> 

<p>Once you code up a working sketch with such a structure, changing a simple parameter can lead to an entirely different behaviour. At that point you're not really 'coding' your sketch anymore, but you're 'playing' with it.</p>

<h2>Sketch from Scratch</h2>
<p>In this blog post specifically I'd like to explain what's going on in this Sketch:</p>

<iframe width="100%" height="315" src="https://www.youtube.com/embed/f0RLlSmxfK8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

<p>It might not be very obvious, but the secret ingredient behind the motion in this sketch are trigonometric functions. I talk a little about trigonometric functions in <a href='https://gorillasun.de/blog/Continuous-oscillating-motion-in-P5JS-with-Sine-functions' target="_blank" rel="noopener noreferrer">this previous blog post</a>.</p>

<h2>Intuition behind the Sketch</h2>
<p>Essentially, we're creating a grid of objects, each of which holds a number of parameters and <code>display()</code> function to draw itself to the canvas. The parameters include it's position and size, and optionally also it's color. The heavy lifting happens in the <code>display()</code> function. Since every rectangle is continuously redrawn, what if we were to change their size and color over time? We could simply do so by modulating the size attribute with a sine function. But first things first, let's create the grid.</p>

<h3>The grid</h3>
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
    rect(this.positionX - this.size / 2, this.positionY - this.size / 2, this.size, this.size, this.curvature, this.curvature, this.curvature, this.curvature)
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

<h3>Modulating Rectangle Sizes</h3>
<h4>Sin() function</h4>
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

<h4>Sin() function with respect to X coordinate</h4>
<pre><code>display() {
    strokeWeight(3);
    
    // This Line is all that changed
    this.size = 20 + 14 * sin(this.positionX + millis() / 500);
    rect(this.positionX - this.size / 2, this.positionY - this.size / 2, this.size, this.size, this.curvature, this.curvature, this.curvature, this.curvature);
}
</code></pre>

<h4>Sin() function with respect to Y coordinate</h4>
<pre><code>this.size = 20 + 14 * sin(this.positionY + millis() / 500);</code></pre>

<h4>Sin() function with respect to X and Y coordinate</h4>
<pre><code>this.size = 20 + 14 * sin(this.positionY +this.positionX + millis() / 500);</code></pre>


<div style="width:500px; height:200px; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;">
<iframe style="width:100%; height:100%; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;" src="https://editor.p5js.org/AhmadMoussa/embed/HaWoY9swq"></iframe>
</div>


<pre><code>

</code></pre>
