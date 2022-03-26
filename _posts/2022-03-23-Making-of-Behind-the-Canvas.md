---
title: Making of Behind the Canvas
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: true
exclude_rss: true
---
<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/banner2.png" alt="">
</span>

If you haven't seen it on my Twitter already, I've had the chance to make a sketch for Crayon Codes! I'm very excited about how it turned out and how everyone responded to it! But before going into any details, I'd like to thank <a href='https://twitter.com/msawired'>Sinan</a> for reaching out to me about this opportunity, and for his continuous feedback throughout the different iterations my sketch went through!

Secondly I'd like to thank all those who've collected an edition or two of 'Behind the Canvas'! It was a joy to make, and as always I learned a lot along the way. Making a sketch where the collector could specify parameters prior to collecting, was yet another beast to conquer. Here are a few of the 70 editions that have been collected:

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

Early February Sinan reached out to me, and sent me some of my sketches that he had seen and thought could be a good fit for the Crayon Code raster. I took a couple of days to dig up their code from my hard drive and assess which ones had enough variety to be turned into generative tokens. The ones that he especially liked were the following:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/init1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/init2.png" alt="">
		</span>
	</div>
</div>

However I wasn't certain if it was possible for them to have enough variety to create an entire collection. And I already had made my <a href='https://www.fxhash.xyz/generative/1193'>'scribble' sketch</a> on fxhash which explored a similar avenue. I did come up with some variations however to see if I could push the idea in a different directon:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/idea1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/idea2.png" alt="">
		</span>
	</div>
</div>

How I ultimately arrived at what 'Behind the Canvas' ended up being, might sound like a far stretch, but my brain works in mysterious ways. What Sinan liked about the two previous sketches, was the 'rug' like texture of them, and how the edges of the lines overlapped with each other.

I pondered a bit on that, and wanted to see if I could somehow make a sketch that didn't convey the texture of fabric, but rather a different property. I explored some other ideas that came to mind, one of them being an attempt at emulating a tear or split in some sort of fabric. I ended up with something that looked like the following:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/proto1.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/behind_the_canvas/proto2.png" alt="">
		</span>
	</div>
</div>

And from here on it's probably obvious how the sketch developed further.

<h2><a name='opc'></a>Setting up your sketch with the OPC Configurator 3000</h2>

A good starting point for figuring out how to weave the Configurator into my code were previous Crayon Code sketches, which conveniently have their code publicly visible. There's a couple of ways to do this, depending on how you structure your sketch, if it's animated or not, and how you want to incorporate the initial random seed throughout your sketch. The most fitting way to do this for my purposes, was how Okazz handled it in his 'Crack' sketch, which only draws and redraws the graphics to the canvas on three occasions:

1. An initial rendering pass
2. Whenever one of the OPC parameters changes
3. Whenever the sketch/window is resized

This was important because running the sketch that I had in mind in real-time would have been very laggy, and since it doesn't have any animated components it doesn't need to do so. Hence it was sufficient to update the sketch only when the parameters changed, or when the window is resized. Let's have a quick look how their method handles this! We'll first have to make some sliders to work with:

<pre><code>/** OPC START **/
OPC.slider('seed', Math.floor(Math.random() * 1000), 0, 1000, 1);
OPC.slider('parameter1', Math.floor(Math.random() * 5), 0, 5, 2);
OPC.slider('parameter2', Math.floor(Math.random() * 5), 0, 5, 2);
/** OPC END**/
</code></pre>

Invoking the OPC.slider() function will create a slider in the small UI widget (that you'll see in the top right corner when you run your sketch), but will also create a variable in your code which will contain the selected slider value. The created variable will then have the same name as the string that you pass to the first parameter. You also need to specify a default starting value, a minimum value, a maximum value and the step amount, in that order. For example:

<pre><code>OPC.slider('seed', Math.floor(Math.random() * 1000), 0, 1000, 1);</code></pre>

This slider will create a variable called seed, with a random starting value in the range of 0 and 1000, and gets incremented by 1 when you drag the slider. You can now use the seed variable throughout your code. Now to resume with Okazz's method we need to declare a couple more things:

<pre><code>let pSeed = seed;
let pParameter1 = parameter1;
let pParameter2 = parameter2;
</code></pre>

We need a placeholder value for each slider. This seems redundant but will make sense when we look at the draw function call:

<pre><code>function draw() {
	if (seed != pSeed || pParameter1 != parameter1 || pParameter2 != parameter2) {
		generate();
	}
}</code></pre>

Essentially, we will only call the generate() function if any of the slider values have changed. Here the generate() function is where we put all of the things that render graphics to the canvas. This generate function will also be called at the end of setup and when the window is resized:

<pre><code>function setup() {
	createCanvas(windowWidth, windowHeight);
	generate();
}

function windowResized() {
	resizeCanvas(windowWidth, windowHeight);
	generate();
}
</code></pre>

And then we just need to update the temporary values at the top of the generate function:

<pre><code>function generate() {
	pSeed = seed;
	pParameter1 = parameter1;
	pParameter2 = parameter2;

	randomSeed(seed);
	noiseSeed(seed); // if you make use of perlin noise also seed noiseSeed()

  // other stuff
}
</code></pre>

It's also important that you set your random seed here at the very top, this is to ensure that the same seed produces the same sketch every time the generate() function is run. I go into this a bit more in detail in a later section, but if we wouldn't set the random seed here, on a subsequent generate() calls we would get completely different sketches. Having discussed this necessary boilerplate component, we can move on to main pat of code that actually generates the visuals. This code will be contained within the generate function, and optionally also inside other helper functions that will be called from the generate function.



<h2><a name='smooth'></a>Creating smooth polygons</h2>

The main portion of the sketch consists of patterned layers, each of which has an opening in the shape of a deformed circle that gives way to the next layer. I've already written about deforming a circular shape with perlin noise <a href='https://gorillasun.de/blog/Radial-Perlin-Noise-and-Generative-Tree-Rings'>here</a>, however this time I did it a little differently reusing my <a href='https://gorillasun.de/blog/An-algorithm-for-polygons-with-rounded-corners'>smooth polygon code</a> for a slightly different aesthetic that does not require perlin noise. If you're interested you can read up on it, but it is not necessary to understand the remainder of this post.

Let's begin by positioning a couple of points in a circular fashion and offsetting them slightly by a random amount:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);
  strokeWeight(5);

  drawShape(w/2, w/2, w/4);
  noLoop();
}

function drawShape(posX, posY, R) {
  let vertices = [];

  beginShape();
  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x = posX + rad * cos(a);
    y = posY + rad * sin(a);

    vertices.push({ x: x, y: y });

    point(x, y);

    vertex(x, y);
  }
  strokeWeight(1);
  endShape(CLOSE);
}
</script>
<p></p>

We obtain a relatively pointy shape when we connect the vertices that we positioned. Here's where we'll reuse the smooth polygon algorithm, to shave off those pointy corners:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);

  ctx = canvas.getContext('2d')
}

function draw() {
  background(220);
  strokeWeight(3);

  drawShape(w/2, w/2, w/4);
  noLoop();
}

function drawShape(posX, posY, R) {
  let vertices = [];

  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x = posX + rad * cos(a);
    y = posY + rad * sin(a);

    vertices.push({ x: x, y: y });
  }

  roundedPoly(ctx, vertices, 9999)
  ctx.stroke()
}

function roundedPoly(ctx, points, radiusAll) {
  ctx.beginPath()
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0 ? (angle += PI) : ((angle += PI), (radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }

    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

And that's the beauty of reusing and building on top of code you've previously written! Here I knew exactly what shape I wanted and how I could achieve it, and it felt really good achieving it with a simple function call.

<h2>Clipping out regions</h2>

Next we will have a look at the rendering context's <a href='https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/clip'>clip()</a> function. Clipping here means cutting out a certain region/portion of the canvas, such that only things inside this specific region are visible, whereas everything that falls outside of it is hidden. Let's have a look at a quick example:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
  ctx = canvas.getContext('2d')
}

function draw() {
  background(220);

  noFill()
  circle(w/2,w/2,w/3)
  ctx.clip()

  stroke(0)
  strokeWeight(2)
  drawPattern(w/2,w/2,w/3)

  noLoop()
}

function drawPattern(posX, posY, size){
  push()

  translate(posX, posY)
  rotate(random(TAU))
  for(let l = 0; l<1; l+=0.05){
    y = map(l,0,1,0,size)

    line(-size/2, y-size/2, size/2, y-size/2)
  }
  pop()
}
</script>
<p></p>

Basically, using the clip() function consists of first drawing a shape that will constitute the clipping region, then calling the clip() function, and thirdly drawing whatever we want to have contained within the clipping region. Note that the shape you're clipping out needs to have a stroke for this to work. If aesthetically you don't want it to have stroke you can still set strokeWeight(0) and the region won't have a stroke. In this example, we're simply clipping out a few parallel lines to obtain something that looks like a hachure.

Now we can chain these clip calls one after another to have several clip regions within each other, which is essentially how the circular openings in 'Behind the Canvas' were made. Here's an example of this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  ctx = canvas.getContext("2d");
}

function draw() {
  background(220);

  noFill();
  strokeWeight(2);
  drawClipLayer(w / 1.5);
  drawClipLayer(w / 2.25);
  drawClipLayer(w / 4);

  noLoop();
}

function drawClipLayer(size) {
  strokeWeight(0);
  fill(220);
  circle(w / 2, w / 2, size);

  circle(w / 2, w / 2, size - 10);
  ctx.clip();

  stroke(0);
  strokeWeight(2);
  drawPattern(w / 2, w / 2, size);
}

function drawPattern(posX, posY, size) {
  push();

  translate(posX, posY);
  rotate(random(TAU));

  for (let l = 0; l < 1; l += 0.05) {
    y = map(l, 0, 1, 0, size);

    line(-size / 2, y - size / 2, size / 2, y - size / 2);
  }
  pop();
}
</script>
<p></p>

The thing of note here is that on subsequent clip calls we need to draw a clip region with a fill, such that it hides the pattern of previous layers which it overlaps. So far we've only done it with simple circles, let's combine it with the irregular shape that we've made earlier:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  ctx = canvas.getContext("2d");
}

function draw() {
  background(220);

  noFill();
  strokeWeight(2);
  stroke(255, 0, 0);
  drawClipLayer(w / 2);

  stroke(0, 0, 255);
  drawClipLayer(w / 3);

  stroke(0, 255, 0);
  drawClipLayer(w / 4);

  noLoop();
}

function drawClipLayer(size) {
  strokeWeight(2);
  fill(220);

  let verts = 0;
  verts = makeShape(w / 2, w / 2, size);

  roundedPoly(ctx, verts[0], 9999);
  ctx.fill();
  ctx.stroke();

  roundedPoly(ctx, verts[1], 9999);
  ctx.stroke();
  ctx.clip();

  strokeWeight(2);
  drawPattern(w / 2, w / 2, size * 2);
}

function makeShape(posX, posY, R) {
  let vertices1 = [];
  let vertices2 = [];

  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x1 = posX + rad * cos(a);
    y1 = posY + rad * sin(a);

    vertices1.push({ x: x1, y: y1 });

    x2 = posX + (rad - 20) * cos(a);
    y2 = posY + (rad - 20) * sin(a);

    vertices2.push({ x: x2, y: y2 });
  }

  return [vertices1, vertices2];
}

function drawPattern(posX, posY, size) {
  push();

  translate(posX, posY);
  rotate(random(TAU));

  for (let l = 0; l < 1; l += 0.05) {
    y = map(l, 0, 1, 0, size);

    line(-size / 2, y - size / 2, size / 2, y - size / 2);
  }
  pop();
}

function roundedPoly(ctx, points, radiusAll) {
  ctx.beginPath();
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0
        ? (angle += PI)
        : ((angle += PI), (radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }

    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

IMPORTANT! Here, to improve visual separation, prior to making the clip call, we precede the clip region by the exact same shape, that is slightly larger and has a fill. This makes sure that the previous pattern doesn't overlap with the new one and has some padding in between itself and the previous layer. If we would use shapes that have no fill, we end up with something that looks like this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  ctx = canvas.getContext("2d");
}

function draw() {
  background(220);

  noFill();
  strokeWeight(2);
  stroke(255, 0, 0);
  drawClipLayer(w / 2);

  stroke(0, 0, 255);
  drawClipLayer(w / 3);

  stroke(0, 255, 0);
  drawClipLayer(w / 4);

  noLoop();
}

function drawClipLayer(size) {
  strokeWeight(2);
  fill(220);

  let verts = 0;
  verts = makeShape(w / 2, w / 2, size);



  roundedPoly(ctx, verts[1], 9999);
  ctx.stroke();
  ctx.clip();

  strokeWeight(2);
  drawPattern(w / 2, w / 2, size * 2);
}

function makeShape(posX, posY, R) {
  let vertices1 = [];
  let vertices2 = [];

  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x1 = posX + rad * cos(a);
    y1 = posY + rad * sin(a);

    vertices1.push({ x: x1, y: y1 });

    x2 = posX + (rad - 20) * cos(a);
    y2 = posY + (rad - 20) * sin(a);

    vertices2.push({ x: x2, y: y2 });
  }

  return [vertices1, vertices2];
}

function drawPattern(posX, posY, size) {
  push();

  translate(posX, posY);
  rotate(random(TAU));

  for (let l = 0; l < 1; l += 0.05) {
    y = map(l, 0, 1, 0, size);

    line(-size / 2, y - size / 2, size / 2, y - size / 2);
  }
  pop();
}

function roundedPoly(ctx, points, radiusAll) {
  ctx.beginPath();
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0
        ? (angle += PI)
        : ((angle += PI), (radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }

    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

Which also makes for an interesting effect, but that's for another sketch. Ultimately, chaining clip calls might not be very intuitive. but it makes for a very interesting visual effect! And maybe you already noticed, but we could substitute the drawPattern() function for any other kind of pattern and it would still work, which is neat.

Another visual improvement we can make is adding a slight shadow between layers, to make it seem as if previous layers are somewhat elevated with respect to subsequent ones. We can also make use of the rendering context for this:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  ctx = canvas.getContext("2d");
}

function draw() {
  background(220);

  noFill();
  strokeWeight(2);
  stroke(255, 0, 0);
  drawClipLayer(w / 2);

  stroke(0, 0, 255);
  drawClipLayer(w / 3);

  stroke(0, 255, 0);
  drawClipLayer(w / 4);

  noLoop();
}

function drawClipLayer(size) {
  strokeWeight(2);
  fill(220);

  let verts = 0;
  verts = makeShape(w / 2, w / 2, size);

  roundedPoly(ctx, verts[0], 9999);
  ctx.fill();
  ctx.stroke();

  ctx.clip();

  ctx.shadowColor = 'black';
  ctx.shadowBlur = 20;

  roundedPoly(ctx, verts[0], 9999);
  ctx.stroke()

  ctx.shadowBlur = 0;

  roundedPoly(ctx, verts[1], 9999);
  ctx.stroke();
  ctx.clip();

  strokeWeight(2);
  drawPattern(w / 2, w / 2, size * 2);
}

function makeShape(posX, posY, R) {
  let vertices1 = [];
  let vertices2 = [];

  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x1 = posX + rad * cos(a);
    y1 = posY + rad * sin(a);

    vertices1.push({ x: x1, y: y1 });

    x2 = posX + (rad - 20) * cos(a);
    y2 = posY + (rad - 20) * sin(a);

    vertices2.push({ x: x2, y: y2 });
  }

  return [vertices1, vertices2];
}

function drawPattern(posX, posY, size) {
  push();

  translate(posX, posY);
  rotate(random(TAU));

  for (let l = 0; l < 1; l += 0.05) {
    y = map(l, 0, 1, 0, size);

    line(-size / 2, y - size / 2, size / 2, y - size / 2);
  }
  pop();
}

function roundedPoly(ctx, points, radiusAll) {
  ctx.beginPath();
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0
        ? (angle += PI)
        : ((angle += PI), (radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }

    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

Here it gets a bit tricky, because we need an additional clip call. I wanted to have the slightly larger shape cast a shadow, for this we can hide an identical shape underneath itself and momentarily toggle on the shadow (using the shadowBlur parameter) and then toggle it off when we don't need it anymore. Finally let us add some color to the mix:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
palette = ['#e74645', '#fb7756', '#facd60', '#fdfa66', '#1ac0c6']

function setup() {
  w = min(windowWidth, windowHeight);
  createCanvas(w, w);
  ctx = canvas.getContext("2d");
}

function draw() {
  background(220);

  noFill();
  strokeWeight(2);

  drawClipLayer(w / 2);

  drawClipLayer(w / 3);

  drawClipLayer(w / 4);

  noLoop();
}

function drawClipLayer(size) {
  strokeWeight(2);
  fill(220);

  shuffle(palette, true)
  fill(palette[0])
  stroke(palette[1])

  let verts = 0;
  verts = makeShape(w / 2, w / 2, size);

  roundedPoly(ctx, verts[0], 9999);
  ctx.fill();
  ctx.stroke();

  ctx.clip();

  ctx.shadowColor = 'black';
  ctx.shadowBlur = 20;

  roundedPoly(ctx, verts[0], 9999);
  ctx.stroke()

  ctx.shadowBlur = 0;

  roundedPoly(ctx, verts[1], 9999);
  ctx.stroke();
  ctx.clip();

  strokeWeight(2);
  drawPattern(w / 2, w / 2, size * 2);
}

function makeShape(posX, posY, R) {
  let vertices1 = [];
  let vertices2 = [];

  for (a = 0; a < TAU; a += TAU / 9) {
    rad = random(1 / 2, 1) * R;

    x1 = posX + rad * cos(a);
    y1 = posY + rad * sin(a);

    vertices1.push({ x: x1, y: y1 });

    x2 = posX + (rad - 20) * cos(a);
    y2 = posY + (rad - 20) * sin(a);

    vertices2.push({ x: x2, y: y2 });
  }

  return [vertices1, vertices2];
}

function drawPattern(posX, posY, size) {
  push();

  translate(posX, posY);
  rotate(random(TAU));

  for (let l = 0; l < 1; l += 0.05) {
    y = map(l, 0, 1, 0, size);

    line(-size / 2, y - size / 2, size / 2, y - size / 2);
  }
  pop();
}

function roundedPoly(ctx, points, radiusAll) {
  ctx.beginPath();
  radius = radiusAll;
  len = points.length;
  p1 = points[len - 1];

  for (i = 0; i < len; i++) {
    p2 = points[i % len];
    p3 = points[(i + 1) % len];

    A = createVector(p1.x, p1.y);
    B = createVector(p2.x, p2.y);
    C = createVector(p3.x, p3.y);

    (BA = A.sub(B)), (BC = C.sub(B));

    (BAnorm = BA.copy().normalize()), (BCnorm = BC.copy().normalize());

    sinA = -BAnorm.dot(BCnorm.copy().rotate(PI / 2));
    sinA90 = BAnorm.dot(BCnorm);
    angle = asin(sinA);

    (radDirection = 1), (drawDirection = false);
    if (sinA90 < 0) {
      angle < 0
        ? (angle += PI)
        : ((angle += PI), (radDirection = -1), (drawDirection = true));
    } else {
      angle > 0 ? ((radDirection = -1), (drawDirection = true)) : 0;
    }

    // accelDir = BAnorm.rotate(PI/2).copy().add(BCnorm)
    // radDirection = Math.sign(accelDir.dot(BCnorm.rotate(PI / 2)))
    // drawDirection = radDirection === -1

    p2.radius ? (radius = p2.radius) : (radius = radiusAll);

    halfAngle = angle / 2;
    lenOut = abs((cos(halfAngle) * radius) / sin(halfAngle));

    // Special part A
    if (lenOut > min(BA.mag() / 2, BC.mag() / 2)) {
      lenOut = min(BA.mag() / 2, BC.mag() / 2);
      cRadius = abs((lenOut * sin(halfAngle)) / cos(halfAngle));
    } else {
      cRadius = radius;
    }

    x =
      B.x +
      BC.normalize().x * lenOut -
      BC.normalize().y * cRadius * radDirection;
    y =
      B.y +
      BC.normalize().y * lenOut +
      BC.normalize().x * cRadius * radDirection;

    ctx.arc(
      x,
      y,
      cRadius,
      BA.heading() + (PI / 2) * radDirection,
      BC.heading() - (PI / 2) * radDirection,
      drawDirection
    );

    p1 = p2;
    p2 = p3;
  }
  ctx.closePath();
}
</script>
<p></p>

And that should cover all bases for the main portion of the sketch! I won't go into too much detail on the patterns, but for hexagonal grids you can find my tutorial on them <a href='https://gorillasun.de/blog/A-guide-to-Hexagonal-Grids-in-P5JS'>here</a>, as well an OpenProcessing sketch on the main pattern <a href='https://openprocessing.org/sketch/1483294'>here</a>.


<h2><a name='determinism'></a>Notes on preserving sketch determinism</h2>

This is an aspect of the sketch where you really have to make sure that everything is done correctly. Having a few fxhash sketches under my belt, I've had a good amount of practice with this, but I still do get some situations where I have to scratch my head for a bit before I can figure out what's wrong.

For example, I oftentimes make a random call within a for loop that depends on a variable that, well, varies over the course of the sketch, increasing or decreasing the number of random calls that are being made. This naturally breaks the determinism of the sketch. I intend to discuss this specific issue at length in an upcoming blog post, but I will give a few examples of things that should be avoided:

<pre><code>rand = random(W/5, W/2)
</code></pre>

For example we can not have random calls that depend on another variable, it needs to be agnostic and should rather be written as follows:

<pre><code>rand = random(1/5, 1/2)*w
</code></pre>

And another example that is not admissible, is having a varying number of random calls:

<pre><code>for(let n = 0; n < windowWidth; n+=step){
    someFunction(random())
}
</code></pre>

Here we would have to make sure that this for loop makes the same number of random() calls each and every time it is executed. This is tricky because what happens when you resize your sketch horizontally? You would either have to make the increment value 'step' scale with the window width, or make this loop independent from the window width altogether. There are a couple of strategies to approach this, but it will have to wait.


<h2><a name='resize'></a>Resizing and Rescaling</h2>

<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/behind_the_canvas/cutscale.mp4" type="video/mp4">
  </video>
</div>

This previous issue leads directly into how we handle scaling and resizing of the sketch, which was also one of the more tricky hurdles to overcome. I've come to the opinion that there isn't really a universal solution to this specific problem, and it really depends on how you want your sketch to behave when you resize it.

An important factor to consider here, is how we tie the window dimensions into the random elements of the sketch as we discussed before. To exemplify this, let's make the sketch, that we have coded up to this point, window scale and size agnostic.

Ideally, we would like to have our sketch span across the entire canvas, but keep the main portion centred and scale regardless of the aspect ratio. Let's setup our sketch in that manner:

<pre><code>function setup() {  
  w = min(windowWidth, windowHeight)
  createCanvas(windowWidth, windowHeight);

  // other setup stuff

  generate()
}

function windowResized(){
  w = min(windowWidth, windowHeight)
  resizeCanvas(windowWidth, windowHeight);
}

function draw() {
  generate()
  noLoop()
}

function generate(){
  randomSeed(seed)

  // other stuff
}
</code></pre>

Here we create a canvas that takes up the entire window, and we make use of p5's windowResized function that triggers when the containing window is resized to adjust the canvas dimensions accordingly. We also keep track of the minimum dimension, which we will use in a second. Additionally, we reset the random seed at the top of the generate function such that the same random calls are made whenever it is rerun. And if you've had a keen eye you will also notice that there is noLoop() statement at the end of the draw function, in which case it shouldn't be redrawn when we resize the window. The resizeCanvas() function however calls the draw() function when it is done resizing the canvas dimensions.

As for the the main shape, if we base our dimensions off of a variable that is tied to the screen dimensions, we can make all the drawn shapes adjust their size when redrawn, and here's an example to show how this works:

<pre><code>function generate(){
  randomSeed(seed)
  translate(windowWidth/2, windowHeight/2)

  clipLayer(w/2)
}

function clipLayer(size){
  verts = makeShape(size);

  roundedPoly(ctx, vertices, 9999);
}

function makeShape(R) {
  let vertices = [];
  for (a = 0; a < TAU; a += TAU / 9) {
     rad = random(1 / 2, 1) * R;

     x = rad * cos(a);
     y = rad * sin(a);

     vertices.push({x: x, y: y})
  }

  return vertices
}   
</code></pre>

We base the radius of the blobby shape off of the minimum dimension of the canvas, and this way ensuring that it will scale along with it, when the canvas is resized. <a href='https://openprocessing.org/sketch/1525688'>Here is an example</a> in the OpenProcessing editor where you can see this in action, just drag the middle separator and observe how it behaves!

<h2><a name='end'></a>Closing Notes</h2>

If you've reached here, thank you for reading! I believe I touched upon all the facets of the sketch that were worth mentioning. Ultimately, what I've realised from projects like this one, is how important it is to complete them, even if you ultimately aren't 100% happy with them. In other words, you can only get better at the entire process, from ideation, to coding up initial versions, to releasing your final sketch, by going through the entire process several times. I think I will be much more confident with my next projects, having more experience as well as more reusable code to back me up in the future.

And again, before my writing becomes unfocused, I am incredibly grateful to everyone who has collected an edition, as well as everyone who has shown me support on Twitter in any form or shape! Thank you, and happy sketching!

<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/behind_the_canvas/cut.mp4" type="video/mp4">
  </video>
</div>
