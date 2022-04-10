---
title: Perlin Noise Surface Textures and Scrolling Tricks
author: Ahmad Moussa
categories:
  - p5js
description: In this part of the explorative series on Perlin Noise, we will have a look at scrolling Perlin noise across the canvas, quantizing it and obtaining interesting surface textures with it.
thumbnail_path: https://gorillasun.de/assets/images/thumbnails/corrupt_datastream.webm
published: true
exclude_rss: false
listed: true
legacy: false
---

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Perlin Landscape.gif" alt="" /></span>


This is the second part of the explorative series on Perlin Noise in P5JS and Processing, and focuses on applying Perlin Noise to a 2 dimensional grid of items on the canvas and how we can obtain interesting surfaces and textures in this manner.

We've already had a look at scrolling perlin noise across the canvas by increasing one or multiple noise parameters over time. However, creativity doesn't stop there!

<h2>Quantizing the noise values</h2>
I'm not sure if this specific technique has a name, but it essentially consists of quantizing the value from the nosie() function. When we feed grid coordinates (scaled by some scale parameter), the noise() function returns smooth and continuous values that resemble a landscape. We can use this to create interesting patterns in combination with quantization. Think about it like a real lanscape, where we shave off mountain tops that are higher that a very specific altitude, and flatten everything around it. The formal definition of quantization is:

<blockquote>
  Quantization is the process of constraining an input from a continuous or otherwise large set of values (such as the real numbers) to a discrete set (such as the integers).
</blockquote>

A simple cisual example in code:

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
      n = noise(i * rez, j * rez);
      
      //uncomment the next line to see difference between noise() and random()
      //n = random()
      if(n>0.6){
      fill(255);
      }else{
        fill(0);
      }
      rect(i, j, 3);
    }
  }
  noLoop()
}
</code></pre>

This allows us to obtain these blob like separation between the black and white areas:
<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/perlin 1.png" alt="" /></span>

        </div>
  <div class="col-6 col-12-medium">
    <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/perlin 2.png" alt="" /></span>
        </div>
</div>

If we were to replace the noise() function with random, we would get something that is much more similar to static noise:
  <div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/noise 1.png" alt="" /></span>

        </div>
  <div class="col-6 col-12-medium">
    <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/noise 2.png" alt="" /></span>
        </div>
</div>

Obviously, we are not limited to quantizing to only two distinct values, we could quantize to an arbitrary amount of values. Here I'm using ternary conditional statements:

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

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Simple quantized.gif" alt="" /></span>

## Landscape

In this manner we could quantize the values to different colors and obtain and almost landscape like scrolling surface:
<pre><code>
function setup() {
  createCanvas(400, 200);
  t = rez = c = n = 0.005;
  strokeWeight(3);
  cols = [
    "#f94144",
    "#f3722c",
    "#f8961e",
    "#f9844a",
    "#f9c74f",
    "#90be6d",
    "#43aa8b",
    "#4d908e",
    "#577590",
    "#277da1",
  ];
}
function draw() {
  background(0);
  noStroke();
  for (i = 0; i < width; i += 3) {
    for (j = 0; j < height; j += 3) {
      n = noise(i * rez, j * rez + t) * 10;
      n > 9
        ? (c = 9)
        : n > 8
        ? (c = 8)
        : n > 7
        ? (c = 7)
        : n > 6
        ? (c = 6)
        : n > 5
        ? (c = 5)
        : n > 4
        ? (c = 4)
        : n > 3
        ? (c = 3)
        : n > 2
        ? (c = 2)
        : n > 1
        ? (c = 1)
        : (c = 0);
      fill(cols[c]);
      rect(i, j, 3);
    }
  }
  t += 0.01;
}

</code></pre>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Perlin Landscape.gif" alt="" /></span>

## Landscape + Tiling
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

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/quantized interesting.gif" alt="" /></span>

<pre><code>
if(n > 0.5){
  fill(127.5*127.5*sin(i*0.02+millis()/3000),
        127.5,
        127.5*127.5*cos(j*0.02+millis()/3000),)
 }else{
   fill(127.5*127.5*sin(i*0.02+millis()/3000),
        127.5*127.5*cos(j*0.02+millis()/3000),
        127.5)
 }
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/quantized interesting 2.gif" alt="" /></span>

## Truchet Tiling

We can also mess around with simple truchet tiling, where the angle of the line is determined by the value of the noise at that particular point:
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
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Perlin Truchet Tiling.gif" alt="" /></span>

We can get more creative with this by offsetting the tiles differently:

<pre><code>
if(n > 0.5){
  line(i+n*10,j+n*10,i+10,j+10)
}else{
  line(i+10,j,i,j+10)
}
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Perlin Truchet Tiling 2.gif" alt="" /></span>

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

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/cartridge.png" alt="" /></span>

Alternatively we can also animate this and add more variation:

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-05-21-Perlin-Noise-Surface-Textures-and-Scrolling-Tricks/Corrupt Datastream.gif" alt="" /></span>

And this is it for this article! If you'd like to support my blog while purchasing some of my sketches, you can do so now by purchasing them on hicetnunc.xyz <a href='https://www.hicetnunc.xyz/tz/tz1hXx6tvTV3kzxpBR6E4hDYcxdgcRGHUTFj'> here </a>.

<!--
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
-->
