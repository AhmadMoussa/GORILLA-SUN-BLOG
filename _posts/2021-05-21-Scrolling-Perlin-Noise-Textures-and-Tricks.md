---
title: Scrolling Perlin Noise Textures and Tricks
author: Ahmad Moussa
categories:
  - p5js
description: In this part of the perlin noise tutorial series we will have a look at creative applications of Perlin Noise and using it to texture.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

This is the second part of the explorative series on Perlin Noise in P5JS and Processing, and focuses on applying Perlin Noise to a 2 dimensional grid of items on the canvas and how we can obtain interesting textures in this manner.

We've already had a look at scrolling perlin noise across the canvas by increasing one or multiple noise parameters over time. However, creativity doesn't stop there!

<h2>Quantizing the noise values</h2>
<pre><code>
function setup() {
  createCanvas(301, 301);
  t = rez = c = n = 0.008;
  strokeWeight(3);
}
function draw() {
  background(0);
  noStroke();
  for (i = 0; i < height; i += 3) {
    for (j = 0; j < width; j += 3) {
      n = noise(i * rez, j * rez + t) * 3;
       n > 3
        ? (c = 3)
        : n > 2
        ? (c = 2)
        : n > 1
        ? (c = 1)
        : (c = 0);
      fill(c*100);
      rect(i, j, 3);
    }
  }
  t += 0.01;
}
</code></pre>

## Landscape
<pre><code>
function setup() {
  createCanvas(301, 301);
  t = rez = c = n = 0.02;
  strokeWeight(3);
}
function draw() {
  background(0);
  noStroke();
  for (i = 0; i < height; i += 3) {
    for (j = 0; j < width; j += 3) {
      n = noise(i * rez, j * rez + t);
       if(n > 0.5){
               fill(0);
       }else{
         fill(127.5*127.5*sin(i*0.02+millis()/1000),
              127.5*127.5*cos(j*0.02+millis()/1000),
              127.5)
       }
      rect(i, j, 3);

    }
  }
  t += 0.08;
}
</code></pre>

## Truchet Tiling
<pre><code>
function setup() {
  createCanvas(301, 301);
  t = rez = c = n = 0.02;
  strokeWeight(2);
}


function draw() {
  background(0);
  //noStroke();
  stroke(255)
  for (i = 0; i < height; i += 10) {
    for (j = 0; j < width; j += 10) {
      n = noise(i * rez, j * rez + t);
      if(n > 0.5){
        
        line(i,j,i+10,j+10)
      }else{
        line(i+10,j,i,j+10)
      }
    }
  }
  t += 0.01;
}
</code></pre>


We can get more creative with this:

<pre><code>
if(n > 0.5){
  line(i+n*10,j+n*10,i+10,j+10)
}else{
  line(i+10,j,i,j+10)
}
</code></pre>


Equivalently we can add more conditional statements to make this texture more interesting, and don't necessarily have to animate it:

<pre><code>
function setup() {
  createCanvas(601, 601);
  t = rez = c = n = 0.022;
  strokeWeight(2);
  frameRate(30)
  //createLoop({duration:5, gif:true})
  xOff = 50
  yOff = 50
}


function draw() {
  
  background(0);
  //noStroke();
  stroke(255)
  for (i = xOff; i < width-xOff; i += 10) {
    for (j = yOff; j < height-yOff; j += 10) {
      
      n = noise(i * rez, j * rez + t);
      drawingContext.setLineDash([n*5,n*10])
    
      if(i - 40 < mouseX && i + 40 > mouseX 
        && j - 40 < mouseY && j + 40 > mouseY){
        drawingContext.setLineDash([n*5,n*10])
      }
      if(n>0.65){
        drawingContext.setLineDash([n*5,1])
        stroke(255,0,0)
      }else{
        stroke(255)
      }
      
      if(n > 0.5){
        line(i,j,i+10,j+10)
      }else{
        line(i+10,j,i,j+10)
      }
      

    }
  }
  t += 0.01;
  noLoop();
}
</code></pre>

Or we can make it interactive with mouse interaction, the possibilities are essentially limitless:
<pre><code>
function setup() {
  createCanvas(401, 401);
  t = rez = c = n = 0.022;
  strokeWeight(2);
  frameRate(30);
  //createLoop({duration:5, gif:true})
  xOff = 50;
  yOff = 50;
}

function draw() {
  background(0);
  //noStroke();
  stroke(255);
  fill(0);
  for (i = xOff; i < width - xOff; i += 10) {
    for (j = yOff; j < height - yOff; j += 10) {
      n = noise(i * rez, j * rez + t);
      drawingContext.setLineDash([n * 5, n * 10]);

      strokeWeight(2);
      if (n > 0.65) {
        drawingContext.setLineDash([n * 5, 1]);
        stroke(255, 0, 0);
      } else {
        stroke(255);
      }

      if (
        i - 40 < mouseX &&
        i + 40 > mouseX &&
        j - 40 < mouseY &&
        j + 40 > mouseY
      ) {
        strokeWeight(1);
        for (isub = i - 20; isub < i + 20; isub += 5) {
          for (jsub = j - 20; jsub < j + 20; jsub += 5) {
            n = noise(isub * rez, jsub * rez + t);
            drawingContext.setLineDash([n * 5, 1]);
            if (n > 0.65) {
              drawingContext.setLineDash([n * 5, 1]);
              stroke(127.5 + 127.5*sin(millis()/1000 + jsub*isub),
                     127.5 + 127.5*cos(millis()/1000 + isub),
                     127.5);
            } else {
              stroke(255);
            }
            if (n > 0.5) {
              line(isub, jsub, isub + 5, jsub + 5);
            } else {
              line(isub + 5, jsub, isub, jsub + 5);
            }
          }
        }
      } else {
        if (n > 0.5) {
          line(i, j, i + 10, j + 10);
        } else {
          line(i + 10, j, i, j + 10);
        }
      }
    }
  }
  t += 0.015;
  //noLoop();
}

</code></pre>
