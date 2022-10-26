---
layout: default
title: "The p5 Graphics Buffer"
author: Ahmad Moussa
categories:
  - blog
description: In this post we'll have a look at the p5js graphics buffer and how to effectively use it in your p5 sketching practice. We'll also create a variation of the 10 PRINT pattern that repeats seamlessly.  
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/Graphics Buffer Guide/banner.png
published: true
exclude_rss: false
legacy: false
listed: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Graphics Buffer Guide/banner.png" alt="">
</span>
<p></p>


The graphics buffer is a powerful feature of the p5 library. When I started with p5 I didn't really understand what it was used for, but now it's become an indispensable tool for me in my sketching practice. Besides creative use cases, the graphics buffer can also help with displaying your sketches nicely on different canvas resolutions. Hence, in this tutorial I want to show you everything that there is to the p5 graphics buffer.

We'll go over how to create one, how to draw to it as well as how to display it correctly and how it can be used as a method to display your sketches in a window-dimension-independent manner. Towards the end I'll show you how to create the cool repeat print sketch you see above, which is done by tiling the canvas with a bunch of small graphics buffers. And by the end of this tutorial I’ll also provide you with a useful template for your own sketching purposes.

<h3>Quick Index:</h3>

1. <a href='#1'>Creating the Buffer</a>
2. <a href='#2'>Displaying the buffer</a>
3. <a href='#3'>Drawing to the buffer</a>
4. <a href='#4'>Buffer as a texture</a>
5. <a href='#5'>Mouse coordinates on the buffer</a>
6. <a href='#6'>Seamless 10 PRINT pattern</a>
7. <a href='#7'>A graphics buffer sketch template</a>


<h2><a name='1'></a>Creating the Buffer</h2>

Creating the buffer is done in a similar manner to creating the main canvas. Generally this is also done in p5's setup function that is run at the beginning of the sketch's execution:

<pre><code>// buffer is declared globally so that we can have access to it everywhere
let buffer;
function setup(){
  w = 400
  createCanvas(w, w)

  // creating a buffer with identical dimensions to the canvas
  buffer = createGraphics(w, w)
}
</code></pre>

<b>createGraphics()</b> essentially creates another canvas element (we can also display it, more about that in a second), with its own rendering instance. This buffer is created offscreen, meaning, if we inspect the browser window of our sketch we can notice that there's another canvas element inside the body tag of our html:

<pre><code>&lt;canvas style="display: none; width: 400px; height: 400px;" width="800" height="800"&gt;&lt;/canvas&gt;</code></pre>

The display:none; css property is in charge of hiding the buffer. You can also notice that the actual width and height of the buffer is 800 pixels instead of the 400 pixels we specified, this is due to the screen's pixeldensity it's rendered in, and might be different on other screens. For this we can also specify the <b>pixeldensity</b> of the graphics buffer:

<pre><code>buffer.pixelDensity(1)</code></pre>

Additionally we can also use the WEBGL rendering mode if we want to create a 3D scene within the buffer rather than the regular 2D one:

<pre><code>buffer = createGraphics(w, w, WEBGL)</code></pre>





<h2><a name='2'></a>Displaying the Buffer</h2>

Displaying the graphics buffer can be done by using p5's image() function. This is possible because the graphics buffer is implicitly also a p5 element that can be displayed with the image function.

<pre><code>// Each frame, the buffer will be drawn as an image to the canvas
function draw(){
  background(220);
  image(buffer, 0, 0);
}
</code></pre>

The image function can be used in a number of different ways, but usually we want the graphics buffer to cover the entire canvas, hence for our purposes we need only pass the buffer as the first parameter alongside the x and y coordinates that specify where our buffer should be drawn. In this case the buffer has the same dimensions as the canvas, hence by simply placing it at (0, 0) we can fill the entire canvas with it.

The p5 image function also allows us to stretch and squeeze the buffer by specifying a width or height that is different from the original buffer dimensions. Here's an example, try moving your mouse over the canvas:

<iframe src="https://openprocessing.org/sketch/1706047/embed/?plusEmbedHash=ZmE3YTRlNmY0ODBlMDczYmMwYjEyY2U1NTNlMTcxMjRmYjg1ZGU4ZmI1MjFiM2M3YmQ2NmVmODgxNjVhOTVmMGM2MTFhNzM5OWYxOGFmYmZiNmVkMzQwZWQwMDIxZjM4ZTFiOWNlNTRiYjY3MWFkZDMwZjkzYjVlZmRjMDcyNTA5RU5NOXUwemZ2ZmJjcXZRSmFaRHkvbFRIOGJFa0VnMEZNTk45Mnk4VEJTQ0FCY0R4Z3JDRE9TV2hZOEd1czRoRWV5UzlrL3JwWjV1ZGRoMG05eUFOUT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

I haven't used it a lot for graphics buffers but we can also use the image function for cropping purposes. This is done by passing in 8 parameters instead of 4, the first four specifying where we want to draw the cropped portion of the graphics buffer on the canvas, whereas the latter 4 specify the portion of the buffer that we want to crop out:

<iframe src="https://openprocessing.org/sketch/1706746/embed/?plusEmbedHash=OGMzYTU4ODI0YjZmZjkxZDc1NjJkYTNlZjA0MjAxM2FkNGYxYjBlNWJkMGQ2MTcyZmRmYTAyY2FjYjM4NmM3ZTgxZTQ0Njc1ZWM2MTk5MzEzODYwNjYzZTU1ZjY5NzdmMzJkNDY5ZmE0NGFlNjAyMWY4ZDQ3OTYzNzJiN2ZjODBObnFmaUdjWVlpcURXcStnenpoVmpkRkMxMU1vOEJtTDNXdEc0ejhiaWd3SXl0K2VXS1dGSWZLV1Y3dWgrN09BUUlGZ2VxR3JUdHN4OVBsVDY3djQ5UT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

Now, what if we wanted to cover the entire canvas with our graphics buffer, but the canvas dimensions are variable? It's often the case that the canvas dimensions aren't fixed and change when the containing browser window is resized, for example in the live view of an fxhash project, where we want the canvas to be centered and take up as much space within the browser window. There isn't one solution that fits all sketches, but when your art is dimensionless we'd want the graphics buffer to be of a fixed size all while resizing along with the canvas. For a static sketch that has a 1:1 aspect ratio we could do something like this:

<pre><code>let buffer;
let buffer_dim = 1080;

// size of the canvas
let w;

function setup(){
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  buffer = createGraphics(buffer_dim, buffer_dim)

  // function where all the stuff in the buffer should be drawn
  drawBufferStuff()

  image(buffer, 0, 0, w, w)
}

// p5's windowResized() function is called when the window dimensions change
function windowResized(){
  w = min(windowWidth, windowHeight)
  resizeCanvas(w, w)
  image(buffer, 0, 0, w, w)
}
</code></pre>

A helpful function in this case is p5's <b>windowResized()</b> that is triggered every time the containing browser window changes in dimensions. In this manner the canvas is of variable size, while the buffer is of a fixed size, but still resizes such that it takes up the entire canvas.

When the sketch doesn't have a 1:1 aspect ratio we'll need to keep track of both width and height, my solution for this:

<pre><code>const BUFF_DIM = 1080;

const BUFF_WID_MOD = 1
const BUFF_HEI_MOD = 1.4

const BUFF_WID = BUFF_DIM * 1
const BUFF_HEI = BUFF_DIM * BUFF_HEI_MOD

let buffer;

let CANV_DIM;

function setup(){
  CANV_DIM = min(windowWidth, windowHeight)
  createCanvas(CANV_DIM*BUFF_WID_MOD, CANV_DIM*BUFF_HEI_MOD);

  buffer = createGraphics(BUFF_WID, BUFF_HEI)

  // function where all the stuff in the buffer should be drawn
  drawBufferStuff()

  image(buffer, 0, 0, CANV_DIM*BUFF_WID_MOD, CANV_DIM*BUFF_HEI_MOD)
}

// p5's windowResized() function is called when the window dimensions change
function windowResized(){
  CANV_DIM = min(windowWidth, windowHeight)
  resizeCanvas(CANV_DIM*BUFF_WID_MOD, CANV_DIM*BUFF_HEI_MOD)
  image(buffer, 0, 0, CANV_DIM*BUFF_WID_MOD, CANV_DIM*BUFF_HEI_MOD)
}
</code></pre>

This is neat, because while working on a sketch I can try out different aspect ratios on the fly. Naturally the rest of the code is also written in manner to take these variables into consideration.

And finally we still need a little bit of css to center the actual canvas on the webpage, for which we'll simply use a flexbox and treat the canvas as a flex item within it:

<pre><code>html,
body {
  margin: 0;
  padding: 0;
  background: #222;
  overflow: hidden;
}

main {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background-color: #222;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

canvas {
  object-fit: contain;
  max-height: 100%;
  max-width: 100%;
}
</code></pre>

Now this is a bit redundant, because if we were to make a sketch simply drawn to a fixed sized canvas this would also take care of centering and resizing the canvas element along with the window.

<h2><a name='3'></a>Drawing to the buffer</h2>

You've probably noticed this by now, but drawing to the buffer is done by prefixing the regular p5 drawing functions by the variable name in which we stored the buffer. For example:

<pre><code>function drawBufferStuff(){
  buffer.background(0)
  buffer.ellipse(PG_WID/2, PG_HEI/2, 200)
}
</code></pre>

This is also true for methods like <b>push()</b> and <b>pop()</b>. A graphics buffer also has it's own rendering context attached, which can be obtained like this:

<pre><code>buffer_ctx = buffer.canvas.getContext('2d')</code></pre>

Another cool thing we can do here is wrapping our drawing code in buffer agnostic functions. This is useful for when we want to draw to a different buffer on the fly simply by passing in the secondary buffer as an input parameter. Here's an example:

<pre><code>function drawBufferStuff(buff){
  buff.background(0)
  buffer.ellipse(PG_WID/2, PG_HEI/2, 200)
}
</code></pre>

When is this useful? For instance, you want to give the person that's viewing your sketch the ability to export your sketch at different sizes, and for this you could create several buffers of different dimensions or pixel densities, then momentarily draw to them, save them and go back to displaying the original buffer:

<pre><code>function setup(){
  createCanvas(400, 400)
  pixelDensity(1)

  buff = createGraphics(400, 400)
  buff.pixelDensity(1)

  buff_highrez = createGraphics(400, 400)
  buff_highrez.pixelDensity(4)
}

function drawBufferStuff(b){
  b.background(0)
  b.ellipse(200,200,100)
}

function keyPressed(){
  if(keyCode === 49){ // 1 key
    drawBufferStuff(buff)
    save(buff, 'buff.png')
  }else if(keyCode === 50){ // 2 key
    drawBufferStuff(buff_highrez)
    save(buff_highrez, 'buff_highrez.png')
  }
}
</code></pre>

This way we don't need two functions with redundant code that effectively do the same thing, just for different buffers. Treating the buffer as an input parameter solves this.

<h2><a name='4'></a>Mouse coordinates on the buffer</h2>

A problem that you might run into while working with a buffer, is that mouse coordinates are relative to the original canvas. Stretching a buffer to fit the canvas will cause mouse coordinates to be off. Here's an example, move your mouse over the canvas, the circle will be drawn in the wrong place:

<iframe src="https://openprocessing.org/sketch/1711525/embed/?plusEmbedHash=NDA3ODk2NTc4Zjc5MjdiMzc3ZjQxZDFkNmI3ZTdjNmI5NmZjYmRiOWYyYzNlODBmZWU3YjIyMzJlODVlZDFiNGZiN2FjNmQ1YTI0Njg2NWM2NjdmYzlmYmIzMTY3MDk3MzA3MDliZmMwOTYzMzc3ODk0OTlkNWU0MGQ5OWVhNmFQMC92R0wxNnNPZk9qRXlmV0lMSzJ0Ym9OVjBPS3piZGNQZ3JOUm9NMzQrNXZrMERLK3dzTkIxU3h2NUNNand4SHh2cDlyM21uQ25RelVUK2dITC9Jdz09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

To remedy this we need to keep track of the scaling factor between the original canvas and the buffer, and then scale the mouse coordinates by that number:

<pre><code>// needs to be recalculated whenever the canvas is resized
scaleFactor = buff_size/actual_size
</code></pre>

And in action this looks as follows:

<iframe src="https://openprocessing.org/sketch/1711528/embed/?plusEmbedHash=ZGU0YTcwNmVjOGFkM2ZiZGRlNDFiOGNmNWNjYzQ5YzE2YzEyMTg4MzM3ZmJhY2JjYzRkYWNiOGQyZWJjZjg1OTYzYTEwNjk1MWU4ZmRhOTE1Y2RhMTc1ZWNmMDkwNjRhZjJjZTkxZWUwMmEyNmUwZWY2NTRjMGQyZTE0NDMzNjBvTGU5b3kyckZ4NFk2MVlxVDJrNXJIakJRYkZ2Z1dENGZHN0pmYmJ6YTJBZDFtR3YwWkpzb1JRWUV3aktoekFxWURxc2QyYU1aSEdZVlB4YTdPQkhvQT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>



<h2><a name='5'></a>Buffer as a texture</h2>

We can also use a graphics buffer as a texture and apply it to 3D shapes in the webgl mode:

<iframe src="https://openprocessing.org/sketch/1707609/embed/?plusEmbedHash=ZjIxODZmZTRlNzE3OGU4MWQyZjA2ZTVlMDg1N2EyNzc1YzVmZWNiNDYwMjRhNmE0NjlmNWY5Yzc0ZmQ1MGFhMGQ2NmQzYTZjNzgzZTVjZmY5NjA3MjNiMWQ4Yzc4ZDU2NjFmMjdkNGQzZmJjZGMxNWM4ZTQxMGM3MTg4NWI1OGUwWmI0bUJ1UU1IYnJkU2U5OG1tR0NZaDc2cCsyK1l1clpxbWJmU2VzSGhtcmFSZlJmOU4zRTRrVERybU1KbU9IeGZNUVB2RmtGUmtsZStrSTV2d0tndz09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

This will work for any shape, and is simply done by passing the graphics buffer (after you've drawn some stuff to it) to the <b>texture()</b> function, which should precede the 3d object that you're drawing. Using graphics buffers as textures I created one of my favourite sketches:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/portfolio_jpg/Hatched Blocks 1.jpg" alt="">
</span>



<h2><a name='6'></a>Seamless 10 PRINT pattern</h2>

And now let's make some art! We're going to make a variation of the famous <a href='https://10print.org/'>10 PRINT pattern</a>, and make it repeat seamlessly in fixed intervals. We'll do this by manipulating a number of graphics buffers and tiling the canvas with them in a specific way. First things first, let's create the pattern:

<pre><code>// A small buffer
let buff;
let buff_dim = 100
function makeBuffer(){
  buff = createGraphics(buff_dim, buff_dim)

  buff.background(255,0,50)
  buff.stroke(0,0,50)
  buff.strokeWeight(7)
  buff.strokeJoin(ROUND)

  for(let x = 0; x < buff_dim; x+=20){
    for(let y = 0; y < buff_dim; y+=20){
      if(random()<.5){
        buff.line(x, y, x+20, y+20)
      }else{
        buff.line(x+20, y, x, y+20)
      }
    }
  }
}
</code></pre>

Here we create a small buffer of 100x100 pixels and create the 10 PRINT pattern with a nested loop. The numbers I chose here are arbitrary, you can try experimenting with the size of the buffer and the length of the lines. Now let's see what it would look like if we tiled the canvas with this pattern:

<iframe src="https://openprocessing.org/sketch/1709350/embed/?plusEmbedHash=NmJkZTU0NzhjZjIxNjkzMjExNzNkYTE2YWZlNjg2MTc2NDM1NThiM2Q3OTAwMTBjNDJmNmNiYzJkYTRhMTM3NzYyN2QyNWIxMGI3MjY4MTFiNzI3OTA3YTgwODY1ZDU1OTYyNTA2N2RjNjFjOTQ0MTUwMjhiOWZjYTUxM2JmZWFEb1NnYmIwa1ZnYzZiT1BVSlhyeVR5eXdQZVZpYzl3czlTOXFUSkxvcklSWUpQaDZDK3JhMFlMUmowdG50U1NmeUNDNFNIanNvajhuNlU1cFREWTErQT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

This already looks really interesting, but as you can see the tiles don't always line up exactly causing some of the lines to end with pointy tips. One way to line them up seamlessly would be by making adjacent tiles mirrored versions of themselves. This would work in a horizontal and vertical manner, and can be done by creating another buffer into which we draw the original tile as well as three mirrored versions of it:

<pre><code>
let compositeBuff;
function makeCompositeBuffer(){
  // buffer needs to be twice the size of original buffer
  compositeBuff = createGraphics(buff_dim*2, buff_dim*2)

  compositeBuff.image(buff, 0, 0, buff_dim, buff_dim)

  compositeBuff.push()
  compositeBuff.translate(buff_dim,0)
  compositeBuff.scale(-1, 1) // a negative number in the scale function mirrors what follows
  compositeBuff.image(buff, 0, 0, -buff_dim, buff_dim)
  compositeBuff.pop()

  compositeBuff.push()
  compositeBuff.translate(0,buff_dim)
  compositeBuff.scale(1, -1)
  compositeBuff.image(buff, 0, 0, buff_dim, -buff_dim)
  compositeBuff.pop()

  compositeBuff.push()
  compositeBuff.translate(buff_dim,buff_dim)
  compositeBuff.scale(-1, -1)
  compositeBuff.image(buff, 0, 0, -buff_dim, -buff_dim)
  compositeBuff.pop()
}
</code></pre>

For mirroring the graphics buffers we can conveniently use p5's <b>scale()</b> function, which actually also accepts negative numbers as input. Passing in -1 to either one or to both parameters will mirror whatever follows along those axes respectively.

Now if we tile the canvas with our composite buffer we obtain a seamlessly repeating pattern:

<iframe src="https://openprocessing.org/sketch/1707717/embed/?plusEmbedHash=MmZlYjdkYmM0MmM1YWFhNTlkZmExYmNmY2RlYzBmODY3OGEyM2MzNTJiYmNjZTI3YjY3MTU0NzdmMjY5N2Y0ZWYwNDMzYzRlMWNjMjE1MDM2MGZhYTU1YjQxYzExMzU5ZWIzYWY1NzA3MmQyMDczMmFhZTNjMzI0ZjBlOGQ4NzBpcUJ0UzdGV2NnRHFpWUxCVVpoU2ZuYnRMN2VsdEtOYkNZV1h3OTMzMnV6eTlvMXoyaXlBN0FidThWT21UR0VleDc3c01NOERjWjYydTJTQittblZGZz09&plusEmbedTitle=true" width="100%" height="500"></iframe>

<p></p>

In this manner the graphics buffer can also be used for creative purposes and not solely for displaying a sketch on the canvas.


<h2><a name='7'></a>A complete Template</h2>

And to conclude, I've put together a little template sketch to get you started incorporating the graphics buffer in your sketching practice:

<pre><code>let buff;
let buff_high_rez;

/* Effective size of the buffer */
const BUFF_SIZE = 1080;

/* Change the ASPECT RATIO of your sketch with these */
const BUFF_WID_MOD = 1;
const BUFF_HEI_MOD = 1;

const BUFF_WID = BUFF_SIZE * BUFF_WID_MOD
const BUFF_HEI = BUFF_SIZE * BUFF_HEI_MOD

const CANVAS_PIXEL_DENSITY = 1
const BUFF_PIXEL_DENSITY = 1
const BUFF_HR_PIXEL_DENSITY = 4

let scaleFactor;

function setup(){
  w = min(windowWidth, windowHeight)

  wx = w * BUFF_WID_MOD
  wy = w * BUFF_HEI_MOD

  createCanvas(wx, wy)
  pixelDensity(CANVAS_PIXEL_DENSITY)

  buff = createGraphics(BUFF_WID, BUFF_HEI)
  buff.pixelDensity(BUFF_PIXEL_DENSITY)

  buff_high_rez = createGraphics(BUFF_WID, BUFF_HEI)
  buff_high_rez.pixelDensity(BUFF_HR_PIXEL_DENSITY)

  scaleFactor = BUFF_SIZE/w

  setupBufferStuff()
}

function draw(){
  drawToBuffer(buff)
  image(buff, 0, 0, wx, wy)
}

function windowResized(){
  w = min(windowWidth, windowHeight)

  wx = w * BUFF_WID_MOD
  wy = w * BUFF_HEI_MOD

  scaleFactor = BUFF_SIZE/w

  resizeCanvas(wx, wy)
  image(buff, 0, 0, wx, wy)
}

function exportBuffer(b){
  save(b, 'img.png')
}

// 1 key exports main buffer | 2 key exports high rez buffer
function keyPressed(){
  if(keyCode === 49){
    drawToBuffer(buff)
    exportBuffer(buff)
  }else if(keyCode === 50){
    drawToBuffer(buff_high_rez)
    exportBuffer(buff_high_rez)
  }
}



/* Things you need to run once, like initialising classes for example */
let backgroundColor;
function setupBufferStuff(){
  backgroundColor = random(255)
}

/* Everything you want to draw to the graphics buffer goes here */
function drawToBuffer(b){
  b.background(backgroundColor)
  b.stroke(255)
  b.ellipse(BUFF_WID/2, BUFF_HEI/2, BUFF_SIZE/4)

  b.ellipse(mouseX*scaleFactor, mouseY*scaleFactor, 10)
}
</code></pre>

You can also fork <a href='https://openprocessing.org/sketch/1711488'>this template</a> on openprocessing.

And we've reached the end of the post again! Thanks for reading! If you've enjoyed this post consider sharing it with a friend, otherwise come say hi over on Twitter! Cheers, and happy sketching!
