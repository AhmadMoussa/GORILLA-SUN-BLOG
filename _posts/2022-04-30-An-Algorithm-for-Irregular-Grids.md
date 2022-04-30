---
title: An Algorithm for Irregular Grids
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/irregular_grids/thumb.mp4
published: true
exclude_rss: true
legacy: false
listed: true
---

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/1mod.png" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/2mod.png" alt="">
		</span>
	</div>
  <div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/3mod.png" alt="">
		</span>
	</div>
</div>

<p></p>

Grids, in their various shapes and forms, have been an important backbone for my sketches since I started creative coding. I'd go as far as saying that grids are a prominent generative art archetype that sits at the foundation of many works. Two influential examples from an early generative art period would be Georg Nees' 1968 work Schotter (german word for Gravel) and Vera Molnár's 1974 artwork (Dés)Ordres (french word for (dis)order):

<div class="row gtr-50 gtr-uniform">
	<div class="col-5">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/nees.jpeg" alt="">
		</span>
	</div>
  <div class="col-7">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/vera.jpeg" alt="">
		</span>
	</div>
</div>

<div class="row gtr-50 gtr-uniform">
	<div class="col-5">
		<p style="margin: 0 0 0.1em 0;">
		</p>
		<p><i>Schotter - Georg Nees, 1968</i></p>
		<p></p>
	</div>
  <div class="col-7">
	  	<p style="margin: 0 0 0.1em 0;">
	  	</p>
		<p><i>(Dés)Ordres - Vera Molnár, 1974</i></p>
		<p></p>
	</div>
</div>

Grids go hand in hand with what computers do best: repetition. Grids are also by nature 'orderly'. We usually use grids and tables to neatly display information, align elements in an organized manner, as well as delimit text/numbers in such a way that it is easy for the eye to follow. However, observing these two artworks reveals a characteristic that is inherent to many generative pieces: the interplay between order and chaos. And I believe that that's one of the most fun aspects of gen-art, when you're presented with a new artwork for the first time and you have to figure out what the pattern is and where the twists and irregularities were introduced.

Here I'd like to conclude this little interlude and dedicate the rest of the post towards a grid-construction algorithm that I have used extensively in the past weeks. The algo is relatively straight-forward and unoptimized, but quite versatile. Here's a little index:

<h3>Discussion:</h3>
1. <a href='#interlude'>Interlude</a>
2. <a href='#grids'>Grids and their Variations</a>
3. <a href='#strat'>A strategy for constructing Irregular Grids</a>

<h3>The Algorithm:</h3>
1. <a href='#bool'>A Boolean Grid</a>
2. <a href='#pack'>The Packing Procedure</a>
3. <a href='#viz'>Visualising the Grid</a>
4. <a href='#gap'>Filling out the Leftover Gaps</a>
5. <a href='#styles'>Different Grid Styles</a>


<h2><a name='grids'></a>Grids and their Variations</h2>

Most of my first sketches were based on simple grids:

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/gorilla sun 2021/April/datastream.gif" alt="">	
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/gorilla sun 2021/June/hieroglyphs I.jfif" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
			<video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
				<source src="https://gorillasun.de/assets/images/gifs/sdf/ktork.webm" type="video/mp4">
			</video>	
		</span>
	</div>
</div>
	
<p></p>	
	

They allowed me to explore concepts like <a href='https://gorillasun.de/blog/Making-of-Gzork'>SDFs</a> and <a href='https://gorillasun.de/blog/Introduction-to-Perlin-Noise-in-P5JS-and-Processing'>noise fields</a> (these two posts are also good starting points if you'd like to learn how to construct grids!). I have also experimented a bit with hexagonal grids and written about different strategies to construct them <a href='https://gorillasun.de/blog/A-guide-to-Hexagonal-Grids-in-P5JS'>here</a>. This leads to the next point that I'd like to discuss, that is irregular grids. When you google 'irregular grid' you get a bunch of odd looking grid layouts, some of them look like this:

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
    <a href='https://openprocessing.org/sketch/1555978'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/grid.png" alt="">
  		</span>
    </a>
	</div>

  <div class="col-4">
    <a href='https://openprocessing.org/sketch/1555984'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/recursiveregular.png" alt="">
  		</span>
    </a>
	</div>

	<div class="col-4">
    <a href='https://openprocessing.org/sketch/1555985'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/recursiveirregular.png" alt="">
  		</span>
    </a>
	</div>
</div>

<div class="row gtr-50 gtr-uniform">

  <div class="col-4">
    <a href='https://openprocessing.org/sketch/1555982'>
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/randomspacing.png" alt="">
      </span>
    </a>
  </div>

  <div class="col-4">
    <a href='https://openprocessing.org/sketch/1555988'>
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/quad.png" alt="">
      </span>
    </a>
  </div>

	<div class="col-4">
    <a href='https://openprocessing.org/sketch/1555993'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/distorted.png" alt="">
  		</span>
    </a>
	</div>
</div>

<p></p>

Except for the first, each of these is an irregular grid in it's own right. <b>Click on the thumbnail of each grid and it will take you to it's corresponding code in the openprocessing editor</b>, <b><u>I would however recommend to attempt recreating these by yourself first, since I found it to be an interesting little exercise</u></b>.

There is however one pattern that does not emerge in these grids, which occurred to me after reading a question by <a href='Griff420 | PixelWank'>@griff420</a> on the birbsnest discord server:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/griffquestion.png" alt="">
</span>


This is a bit tricky to explain, but it's not immediately evident how the rectangles could have been placed. In the middle we see three rectangles interlocking in a manner that wouldn't be possible to achieve by recursive subdivision.

<a href='https://maxhalford.github.io/'>Here is a nice article about a 'Recursive Mondrian' approach by Max Halford</a>, who has an excellent blog btw. His approach is similar to the second and third grid examples I showed a little bit above. In such a grid it is clear where the larger rectangles were subdivided into smaller ones. However, you will never see rectangles protrude beyond the border of a larger containing rectangles. In the next section I'll give an intuitive explanation of an algorithm that can construct such an irregular grid.


<h2><a name='strat'></a>A strategy for constructing an Irregular Grid</h2>

I came up with this strategy at the same time that I was working on my <a href='https://gorillasun.de/blog/An-Object-oriented-approach-to-random-walkers'>grid random walker code</a> as well as the <a href='https://gorillasun.de/blog/Making-of-Grand-Canyon'>mini grand canyon sketch</a>. What it has in common with them is that it is also based on an initial boolean grid, which we will use to do some pseudo rectangle packing. In this case, I am calling it 'pseudo' rectangle packing, because the size of the rectangles is chosen at runtime rather than being specified at a prior time.

Similar to the two previously mentioned sketches, we'll begin with a boolean grid, which basically consists of a 2D array of boolean values. Initially we'll set all of these values to true, which signifies that these spots are unoccupied. With the grid ready, we can begin placing some rectangles. We will go through the grid from the top left towards the bottom right, and depending how we arrange our loops we can do this in a row-wise or column-wise order.

First, we pick a reasonable rectangle size that spans a specific area of the grid. Here it's important to note that the sizes are relative, say we are working with a 12 by 12 grid and choose a rectangle size of 3x3, this would mean that the rectangle spans 3 columns and 3 rows. After having chosen a rectangle size, we will check if the grid at the current position, as well as the positions that the rectangle would span are unoccupied. If that is so, we can place the rectangle, and mark those spots in the grid as occupied.

We repeat this process while iterating over the grid. Once we are done and visualise the grid, we will notice that some unoccupied spots remain, this is due to choosing rectangle sizes that either overlapped with occupied spots in the grid or exceeded it's dimensions. Once we tackle the code we'll find that we have an implicit solution already. And this is pretty much all there is to it, we basically place some rectangles on a grid, and with


<h2><a name='bool'></a>A Boolean Grid</h2>

Let's begin by creating the 2d boolean array! For starters we'll only consider the case where the shape of the grid is square (the final code will be able to produce any arbitrarily shaped grid however):

<pre><code>function setup(){
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  // size of the padding between grid and sketch borders
  padding = w/12;

  // number of rows and columns of the grid
  gridDivsX = 15;
  gridDivsY = 15;

  // actual spacing between grid points
  gridSpacingX = (w - padding*2)/gridDivsX;
  gridSpacingY = (w - padding*2)/gridDivsY;

  // here we populate the 2d boolean array
  let bools = [];

  for(let x = 0; x<gridDivsX; x++){
    var column = [];
    for(let y = 0; y<gridDivsY; y++){
      column.push(1);
    }
    bools.push(column);
  }
}
</code></pre>

Here gridDivsX and gridDivsY essentially specify the overall resolution of the grid and how many total empty spots it'll have. gridSpacingX and gridSpacingY are not used here yet, but we will need them when the time comes to draw our grid to the canvas, since they define the actual dimensions of the grid.

We also need to create an array in which we will store the positions of the rectangles that will make up the grid:

<pre><code>let rectInfo = [];
</code></pre>

And that concludes the setup, now we can tackle the actual packing procedure.

<h2><a name='pack'></a>The Packing Procedure</h2>

The packing procedure consists of a number of steps that I'll go over in this section. Our logic will be contained within a function called constructGrid():

<pre><code>function constructGrid(sizesArr){
  for(let x = 0; x<gridDivsX-max(sizesArr)+1; x++){
    for(let y = 0; y<gridDivsY-max(sizesArr)+1; y++){
    }
  }
}
</code></pre>

There's already a bunch of things going on here. The main part of the function consists of a nested loop that goes over the grid. The function also requires an input called sizesArr, which basically is an array from which we will draw different sizes at random. We will use these sizes to create and place randomly sizes rectangles on the grid.

In the loop condition we also don't loop over the entire grid, for now we ignore the spots where rectangles could potentially exceed the dimensions of the grid and throw an error. The next step now is coding the rectangle placing logic.

So, at this point, since we haven't chosen dimensions for the rectangle that is to be placed, and until proven otherwise, we can make the assumption that the rectangle should theoretically fit, whatever size it may have. We designate this fact with a boolean variable:

<pre><code>var fits = true;</code></pre>

Now we can actually choose dimensions for our rectangle. At random, we select different sizes for the rectangle's width and height from our sizes array:

<pre><code>xdim = random(sizesArr);
ydim = random(sizesArr);
</code></pre>

As mentioned earlier there are two conditions during which the rectangle does not fit: it either exceeds the bounds of the grid, or it overlaps with another rectangle that we already placed. We can check if it is within bounds:

<pre><code>if(x + xdim > gridDivsX || y + ydim > gridDivsY){
  fits = false
}</code></pre>

And check if it overlaps with another rectangle:

<pre><code>for(let xc = x; xc < x + xdim; xc++){
  for(let yc = y; yc < y + ydim; yc++){
    if(bools[xc][yc] == 0){
      fits = false
    }
  }
}</code></pre>

Now if both of these checks fail and 'fits' remains true, it is safe to place the rectangle. In this case we need to mark the area that the rectangle will occupy as occupied and save the rectangle's position and dimensions for later use since we are not going to draw it immediately:

<pre><code>if(fits){
  // mark area as occupied
  for(let xc = x; xc < x + xdim; xc++){
    for(let yc = y; yc < y + ydim; yc++){
      bools[xc][yc] = false
    }
  }

  rectInfo.push(new makeRect(x,y,xdim,ydim))
}
</code></pre>

You probably noticed that we are storing the rectangle information as a custom object, which is useful if we want to store additional information with each rectangle, but is not necessary. The class in this case is quite minimal and looks something like the following:

<pre><code>function makeRect(posX, posY, dimX, dimY){
  this.posX = posX;
  this.posY = posY;
  this.dimX = dimX;
  this.dimY = dimY;

  // other stuff here
}
</code></pre>

<h2><a name='viz'></a>Visualising the Grid</h2>

Drawing the grid to the canvas now is relatively straight forward, we simply loop over the array in which we stored our rectangle information:

<pre><code>function drawGrid(){
  for(let n = 0; n<rectInfo.length; n++){
    r = rectInfo[n]
    rect(r.posX * gridSpacingX, r.posY * gridSpacingY,
          r.dimX * gridSpacingX, r.dimY * gridSpacingY)
  }
}
</code></pre>

Here's where the gridSpacingX and gridSpacingY variables we computed at the start come into play. We need to multiply the rectangles' position and dimensions with the actual dimensions of our grid to get accurate sizes. I really like this approach, because in this manner we can stretch the grid horizontally and vertically with fairly little effort. Let's first put together everything we have discussed so far:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
let rectInfo = []

function setup(){
  randomSeed(0);

  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  // size of the padding between grid and sketch borders
  padding = w/12;

  // number of rows and columns of the grid
  gridDivsX = 15;
  gridDivsY = 15;

  // actual spacing between grid points
  gridSpacingX = (w - padding*2)/gridDivsX;
  gridSpacingY = (w - padding*2)/gridDivsY;

  // here we populate the 2d boolean array
  bools = [];

  for(let x = 0; x<gridDivsX; x++){
    var column = [];
    for(let y = 0; y<gridDivsY; y++){
      column.push(1);
    }
    bools.push(column);
  }

  constructIrregularGrid([2,3]);

  background(0);
  stroke(255);
  strokeWeight(4);
  noFill()
  drawGrid()
  markEmptySpots()
}

function makeRect(posX, posY, dimX, dimY){
  this.posX = posX;
  this.posY = posY;
  this.dimX = dimX;
  this.dimY = dimY;
}

function constructIrregularGrid(sizesArr){
  for(let x = 0; x<gridDivsX-max(sizesArr)+1; x++){
    for(let y = 0; y<gridDivsY-max(sizesArr)+1; y++){

      xdim = random(sizesArr)
      ydim = random(sizesArr)

      fits = true

      // check if within bounds
      if(x + xdim > gridDivsX || y + ydim > gridDivsY){
        fits = false
      }

      // check if rectangle overlaps with any other rectangle
      if(fits){
        for(let xc = x; xc < x + xdim; xc++){
          for(let yc = y; yc < y + ydim; yc++){
            if(bools[xc][yc] == 0){
              fits = false
            }
          }
        }
      }

      if(fits){
        // mark area as occupied
        for(let xc = x; xc < x + xdim; xc++){
          for(let yc = y; yc < y + ydim; yc++){
            bools[xc][yc] = false
          }
        }

        rectInfo.push(new makeRect(x,y,xdim,ydim))
      }
    }
  }
}

function drawGrid(){
  for(let n = 0; n<rectInfo.length; n++){
    r = rectInfo[n]
    rect(r.posX * gridSpacingX + padding, r.posY * gridSpacingY + padding,
          r.dimX * gridSpacingX, r.dimY * gridSpacingY)
  }
}

function markEmptySpots(){
  for(let x = 0; x<gridDivsX; x++){
    for(let y = 0; y<gridDivsY; y++){
      if(bools[x][y]){
        point(x * gridSpacingX + gridSpacingX/2 + padding,
              y * gridSpacingY + gridSpacingY/2 + padding)
      }
    }
  }
}
</script>
<p></p>

Notice here that our strategy wasn't able to completely fill out the entire grid, and there were some empty spots left over. These spots are marked with dots. However before we tackle filling out these spots, let make this grid adjustable to different canvas sizes!

This change can be effectuated by introducing a couple more variables in the setup function:

<pre><code>function setup(){
  // for demonstration purposes
  randomSeed(0);

  widMod = 1  // width modifier
  lenMod = 1  // height modifier

  w = min(windowWidth, windowHeight);

  wx = w*widMod
  wy = w*lenMod

  createCanvas(wx, wy);

  // size of the padding between grid and sketch borders
  padding = w/12;

  // number of rows and columns of the grid
  gridDivsX = 15;
  gridDivsY = 15;

  // actual spacing between grid points
  gridSpacingX = (wx - padding*2)/gridDivsX;
  gridSpacingY = (wy - padding*2)/gridDivsY;

  // rest is kept the same as before
}
</code></pre>

Now we can control the aspect ratio of the sketch simply by specifying it with the widMod and lenMod variables. For demonstration purposes I am using a fixed seed number:

<div class="row gtr-50 gtr-uniform">
  <div class="col-4">
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/regular.png" alt="">
      </span>
  </div>

  <div class="col-8">
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/stretch.png" alt="">
      </span>
  </div>
</div>

If you're not a fan of the stretched out look, we can also multiply the grid divisions by the width and length mods to obtain a consistent look:

<pre><code>gridDivsX = 15*widMod;
gridDivsY = 15*lenMod;
</code></pre>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/widcons.png" alt="">
</span>

That should give you a couple of options to obtain a variety of sketches that don't always have a square aspect ratio!

<h2><a name='gap'></a>Filling out the Leftover Gaps</h2>

Now on to completely filling out the grid! For this we simply need to make an additional constructGrid() call:

<pre><code>constructIrregularGrid([2,3]);
constructIrregularGrid([1]);
</code></pre>

And we obtain a grid that looks something like this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
let rectInfo = []

function setup(){
  randomSeed(0);

  w = min(windowWidth, windowHeight);
  createCanvas(w, w);

  // size of the padding between grid and sketch borders
  padding = w/12;

  // number of rows and columns of the grid
  gridDivsX = 15;
  gridDivsY = 15;

  // actual spacing between grid points
  gridSpacingX = (w - padding*2)/gridDivsX;
  gridSpacingY = (w - padding*2)/gridDivsY;

  // here we populate the 2d boolean array
  bools = [];

  for(let x = 0; x<gridDivsX; x++){
    var column = [];
    for(let y = 0; y<gridDivsY; y++){
      column.push(1);
    }
    bools.push(column);
  }

  constructIrregularGrid([2,3]);
  constructIrregularGrid([1]);

  background(0);
  stroke(255);
  strokeWeight(4);
  noFill()
  drawGrid()
  markEmptySpots()
}

function makeRect(posX, posY, dimX, dimY){
  this.posX = posX;
  this.posY = posY;
  this.dimX = dimX;
  this.dimY = dimY;
}

function constructIrregularGrid(sizesArr){
  for(let x = 0; x<gridDivsX-max(sizesArr)+1; x++){
    for(let y = 0; y<gridDivsY-max(sizesArr)+1; y++){

      xdim = random(sizesArr)
      ydim = random(sizesArr)

      fits = true

      // check if within bounds
      if(x + xdim > gridDivsX || y + ydim > gridDivsY){
        fits = false
      }

      // check if rectangle overlaps with any other rectangle
      if(fits){
        for(let xc = x; xc < x + xdim; xc++){
          for(let yc = y; yc < y + ydim; yc++){
            if(bools[xc][yc] == 0){
              fits = false
            }
          }
        }
      }

      if(fits){
        // mark area as occupied
        for(let xc = x; xc < x + xdim; xc++){
          for(let yc = y; yc < y + ydim; yc++){
            bools[xc][yc] = false
          }
        }

        rectInfo.push(new makeRect(x,y,xdim,ydim))
      }
    }
  }
}

function drawGrid(){
  for(let n = 0; n<rectInfo.length; n++){
    r = rectInfo[n]
    rect(r.posX * gridSpacingX + padding, r.posY * gridSpacingY + padding,
          r.dimX * gridSpacingX, r.dimY * gridSpacingY)
  }
}

function markEmptySpots(){
  for(let x = 0; x<gridDivsX; x++){
    for(let y = 0; y<gridDivsY; y++){
      if(bools[x][y]){
        point(x * gridSpacingX + gridSpacingX/2 + padding,
              y * gridSpacingY + gridSpacingY/2 + padding)
      }
    }
  }
}
</script>
<p></p>

This works because on the subsequent constructGrid() call we are going over the same boolean grid, the spots that have already been marked as occupied will, well, still be occupied, but we get a chance to fill out those spots that are still free. And since we pass an array that only has one size the rectangles will inevitably fill a single grid cell. Now you don't necessarily need to fill it out this way and can pass in different grid sizes.

<h2><a name='styles'></a>Different grid styles</h2>

We can exploit this behaviour and obtain completely different grid styles! Let's make a little modification that allows us to control the rectangle widths and heights independently:

<pre><code>function constructIrregularGrid(sizesArrX, sizesArrY){
  for(let x = 0; x<gridDivsX-max(sizesArrX)+1; x++){
    for(let y = 0; y<gridDivsY-max(sizesArrY)+1; y++){

      xdim = random(sizesArrX)
      ydim = random(sizesArrY)

      // rest stays the same
    }
  }
}
</code></pre>

Now we are passing in two independent arrays, one that specifies the range of widths that rectangles can have, and another one for their heights. You can obtain a variety of different grids in this manner:

<div class="row gtr-50 gtr-uniform">
  <div class="col-4">
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/123-12.png" alt="">
      </span>
  </div>
  <div class="col-4">
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/13-24.png" alt="">
      </span>
  </div>

  <div class="col-4">
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/34-12.png" alt="">
      </span>
  </div>
</div>

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<p style="margin: 0 0 0.1em 0;">
		</p>
		<p><i>[1,2,3], [1,2]</i></p>
		<p></p>
	</div>
  	<div class="col-4">
	  	<p style="margin: 0 0 0.1em 0;">
	  	</p>
		<p><i>[1,3], [2,4]</i></p>
		<p></p>
	</div>
	<div class="col-4">
	  	<p style="margin: 0 0 0.1em 0;">
	  	</p>
		<p><i>[3,4], [1,2]</i></p>
		<p></p>
	</div>
</div>

And that's prettymuch everything that goes into this algorithm, if I stumble across any additional modifications I will make sure to include them here. If you do end up using this, and/or make anything with please @ me on Twitter when you share it!
