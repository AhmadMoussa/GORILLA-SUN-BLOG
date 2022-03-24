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

I am exhilarated to have had the opportunity to work on a sketch for Crayon Codes! First off, I'd like to thank Sinan for reaching out to me about this opportunity, and for his continuous feedback throughout the different iterations my sketch went through!

Secondly I'd like to thank all those who've collected an edition or two of 'Behind the Canvas'! It was a joy to make, and as always I learned a lot along the way. Making a sketch where the collector can specify parameters prior to collecting, was yet another beast to conquer.

There's a couple of cool things going on in the sketch, that are worth inspecting a little bit more closely. Such as:

1. Ideation and initial ideas
2. Setting up our sketch with the OPC Configurator 3000
3. Using the ctx.clip() call
4. A closer look at each one of the patterns
5. Handling scaling and resizing
6. Making sure that we get the same output for every seed

<h2>Ideation and initial ideas</h2>


The ideation process began with Sinan shooting me some of my sketches that he had seen and thought could be a good fit for the Crayon Code raster. I took a couple of days to dig up their code from my harddrive

The one that he especially liked was the following:





<h2>Setting up your sketch with the OPC Configurator 3000</h2>

A good starting point for this were previous Crayon Code sketches. There's a couple of ways to do this, depending on how you structure your sketch and how you want to incorporate the initial random seed throughout your sketch. The most fitting way to do this for my purposes, was how Okazz handled it in his 'Crack' sketch, which only draws and redraws the graphics to the canvas on three occasions:

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

Essentially, we will only call the generate() function if any of the slider values have changed. Here the generate() function is where we put all of things that render graphics to the canvas. This generate function will also be called at the end of setup and when the window is resized:

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

It's also important that you set your random seed here. Now we can begin writing the code for our sketch within the generate function, and optionally other functions that should be called from the generate function.





<h2>Creating smooth polygons</h2>

The main portion of the sketch consists of patterned layers, each of which has an opening in the shape of a deformed circle that gives way to the next layer. I've already written about deforming a circular shape with perlin noise <a href='https://gorillasun.de/blog/Radial-Perlin-Noise-and-Generative-Tree-Rings'>here</a>, however this time I did it a little differently reusing my <a href='https://gorillasun.de/blog/An-algorithm-for-polygons-with-rounded-corners'>smooth polygon code</a>, for slightly different shapes.

Let's start by positioning a couple of points in a circular fashion and offsetting them slightly by a random amount:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function setup() {
  w = min(windowWidth, windowHeight)
  createCanvas(w, w);
}

function draw() {
  background(220);
  strokeWeight(5);

  drawShape(w/2, w/2, w/5);
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

We obtain quite a pointy shape when we connect the vertices. Here's where we'll reuse the smooth polygon algorithm, to shave off those pointy corners:

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

  drawShape(w/2, w/2, w/5);
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

And we've got ourselves a nice smooth shape! The next step will be layering these shapes on top of each other.

<h2>Clipping out regions</h2>

Now we'll make heavy use of the rendering context's <a href='https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/clip'>clip() function</a>, where clipping in this sense means cutting out a certain region/portion of the canvas such that we only show this specific region and hide the rest. Let's have a look at a quick example:

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
  strokeWeight(0)
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

Basically, using the clip() function consists of first drawing a shape that will constitute the clipping region, then calling the clip() function, and lastly drawing whatever we want to have contained within the clipping region. Note that the shape you're clipping out needs to have a stroke for this to work. If aesthetically you don't want it to have stroke you can still set strokeWeight(0) and the region won't have a stroke. Now we can chain these clip calls one after another to have several clip regions within each other, which is essentially how the circular openings in 'Behind the Canvas' were made. Here's an example of successive clip calls:

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

The thing of note here is that on subsequent clip calls we need to draw a shape that has a fill, such that it hides the pattern of previous layers which it overlaps. Here we're only doing it with simple circles, let's combine it with the irregular shape that we made earlier:

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

Here it gets a bit tricky, because we need an additional clip call. I wanted to have the slightly larger shape cast a shadow, for this we can hide a the same shape underneath itself and momentarily toggle on the shadow (using the shadowBlur parameter) and then toggle it off when we don't need it anymore. Finally let us add some color to the mix:

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
  fill(palette[1])
  //background(palette[1])
  stroke(palette[0])


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

And that's it, I think that covers all bases for the main portion of the sketch! Next let's have a look at how scaling and resizing is handled.



<h2>Handling scaling and resizing</h2>

Scaling and resizing the sketch was one of the more difficult hurdles to overcome. An important factor to consider here, is how we tie the window dimensions into the random elements of the sketch, oftentimes I make a random call within a for loop that depends on the width or height of the window, which is not something that should be done if you intend to preserve the determinism of the sketch. I intend to discuss this at length in an upcoming blog post, but I will give a few examples of things that can't be done:

<pre><code>rand = random(W/5, W/2)
</code></pre>

For example we can not have random calls that depend on another variable, it needs to be agnostic and should rather be written as follows:

<pre><code>rand = random(1/5, 1/2)*w
</code></pre>

And another example that is not admissible, is having a varying number of random calls:

<pre><code>for(let x = 0; x < windowWidth; x+=step){
    someFunction(random())
}
</code></pre>

Here we would to make sure that this for loop makes the same number of random() calls each and every time it is executed, either by making the increment value scale with the window width, or make the loop independent from the window width. While testing my sketch I encountered this problem, and it took me quite a while to figure out why the sketch was changing when I resized the window.

To exemplify this, let's make the sketch we have coded up to this point window scale and size agnostic.

<iframe src="https://openprocessing.org/sketch/1525688/embed/?plusEmbedHash=MmY5NTE1YWE0ZmJiYzkyODViNDA4NzY4ZmJjOTkwNmU4ZGFiN2MyMmI5ODkzNWM4NDYxNDIwNDEwMzAxYzhjODQ3ZWY5OTczNjUyNzU0ZDM2NTQ2ZWI0ZjFiYTdhMmFjODA1NThmMWQxOTA1NzNkNDMwNTU3YWYxMzgxOGNjZDY2eW9XM2U0NklVRHhyaWc2VldYSUIzVVlGeTgxdW5iSXhkT0JyZVhGSTZtQnRRR1c5RVlaN0QxWk81aEVhcU9QdCtaNDN6TU9Sa3Jxb1lvOXZHUUFIUT09&plusEmbedTitle=true" width="100%" height="400"></iframe>


















1. <a href='#hex'>Drawing a Single Hexagon in p5</a>
2. <a href='#grid'>Constructing a Hexagonal Grid</a>
3. <a href='#spiralo'>Hexagon Spiral</a>
4. <a href='#spiral2'>Spiral Method Compact</a>
5. <a href='#recursive'>Recursive Method</a>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jewelsbanner.png" alt="">
</span>

<h2>A little bit of Terminology</h2>


<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">

</script>
<p></p>

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/roses.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/hexagons/jestercap.png" alt="">
		</span>
	</div>
</div>
