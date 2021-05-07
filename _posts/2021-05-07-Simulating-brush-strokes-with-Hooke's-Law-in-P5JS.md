---
title: Simulating brush strokes with Hooke's Law in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: Hooke's Law is generally found in physics and concerns the behaviour of springs, however it can also come in handy for simulating brush strokes programmatically.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: false
---

One continuous big source of inspiration for me has been Processing's <a href='https://openprocessing.org/browse/#'>Discover Tab</a>, where you can browse sketches from different users and see what they have come up with.
Recently one in particular caught my eye, that allows you to draw brush strokes in a style reminiscent of the far east. The behaviour of these brush strokes felt very fluid and responsive, so I dug little deeper and had a look at the code (you can view the code for every sketch on there!).

The <a href='https://openprocessing.org/sketch/755877'>sketch</a> was made by Twitter user <a href='https://twitter.com/BUN_information'>@BUN_information</a>, and has even made a very nice tutorial for his code <a href='https://openprocessing.org/sketch/793375'>here</a>. This blog post's purpose is to explain and adapt this code for p5js.

BUN divides his tutorial into 6 parts, where each part introduces new behaviour to the brush stroke. The first part is very simple, we simply draw circles at the cursor location:

<pre><code>function setup() {
  createCanvas(400, 400);
}

// brushSize simply is the thikness of the brush stroke
let brushSize = 20;
function draw() {
  if(mouseIsPressed) {
    strokeWeight( 3 );
    circle( mouseX, mouseY, size );
  }
}
</code></pre>

The second step will actually improve the drawing behaviour significantly by implementing Hooke's Law. Essentially, we're not drawing the ellipses at the exact mouse coordinates anymore, but are applying a number of modifiers to them. It will almost feel like the drawn ellipses are dragged behind the mouse cursor, as if they were attached to a spring, and once you stop moving the cursor, it will actually overshoot and then snap back ellastically to a resting position.
I'm not a physics whizz, but I will try my best at explaining the concept. 

Picture an imaginary spring, where one end is attached to the circle that we're drawing, and the other end is connected to your cursor. This spring has an ellastic behaviour, when you begin dragging one end, the other end, to which our circle is attached, will start dragging behind it. The faster you drag one end the more this spring will be stretched out and the harder the snap-back when you come to a rest. The trick here is to somehow simulate this behaviour, and it can be done with Hooke's Law.

Hooke's Law states that the force F by which a spring is displaced can be calculated with the following formula:
<pre><code>F = k*x</code></pre>
where x is the amount of displacement since the beginning of dragging the spring, and k is a parameter characteristic of the spring. We can actually choose this parameter, which simply signifies the elasticity of the spring.

In BUN's code, the first thing is the caculation of the velocity of our cursor when it is moving, where velocity is a vector quantity that defines the distance traveled over some period of time. 
The amount of displacement is simply the distance between the moment we pressed the mouse to where we moved the mouse, and will be the x parameter in Hooke's formula. For now well simply choose k to be 0.5 for a balanced behaviour (k should be between 0 and 1).

One thing to note here, is that we need to calculate the formula for both x and y coordinates, since we're moving on a 2D plane:
<pre><code>vx += ( mouseX - x ) * spring;
vy += ( mouseY - y ) * spring;
</code></pre>

Now, we would end up with a very erratic behaviour, where the spring seems to never come to a stop, and keeps oscillating around the cursor's rest position:

This is because we ignored one variable that occurs in the physical world: friction. Friction, like the spring parameter, is a variable between 0 and 1, where values closer to 1 designated less friction and values closer to zero designate more friction. We simply multiply our velocity by this friction parameter. And we will obtain a nice behaviour. The code should look like this now:
<pre><code>function setup() {
  createCanvas(400, 400);
}

// brushSize simply is the thikness of the brush stroke
let brushSize = 20;
let f = 0.5;
let spring = 0.5;
let friction = 0.5;
function draw() {
  /*
    Smoother movement than using mouse coordinates
  */
  /*
    Parameters used
      size : Brush size
      spring : Spring constant(Larger value means stronger spring)
      friction : Friction(Smaller value means, the more slippery)
  */
  
  if(mouseIsPressed) {
    
    if(!f) {
      // Initialize coordinates
      f = true;
      x = mouseX;
      y = mouseY;
    }
    
    // Calculate velocity
    /* 
      MEMO : Use Hooke's law to make spring motion
        DistanceX = (X1 - X0)
        SpringConstant = (value between 0 and 1)
        AccelerationX = DistanceX * SpringConstant
        VelocityX = ( VelocityX + AccelerationX ) * Friction
    */
    vx += ( mouseX - x ) * spring;
    vy += ( mouseY - y ) * spring;
    vx *= friction;
    vy *= friction;
    
    x += vx;
    y += vy;
    
    // Draw at the calculated coordinates
    strokeWeight( 3 );
    circle( x, y, brushSize );  // AMEND: mouseX, mouseY -> x, y
    
  } else if(f) {
    // Reset state
    vx = vy = 0;
    f = false;
  }
}
</code></pre>

In the third part of the tutorial, we begin making some changes to make the drawn circles look more like strokes, rather than just circles. Generally, in chinese, japanese and korean calligraphy, symbols can be drawn in different styles and different speeds, where the seed at which the calligraphy is made is a characteristic feature. When drawn slow, strokes tend to be thicker and heavier, when drawn faster, less pressure is put on the strokes and they tend to be thinner. This is what BUN tries to achieve in this part of the tutorial, drawn circles should be smaller (thinner), in the middle portion of the stroke, the start and end points of the stroke should be thicker, as more pressure is put on the brush.

This behaviour can be simulated by calculating the stroke thickness as a function of the velocity. Essentially, the faster we are drawing, the higher the velocity, the thinner is our stroke.
<pre><code>    v += sqrt( vx*vx + vy*vy ) - v;  // ADD
v *= 0.6;  // ADD</code></pre>

We also need to ensure a minimum width of 1, so that the stroke doesn't completely vanish.

In part 4, we actually make the stroke look much more like a stroke than just ellipses. I'm actually getting more and more impressed by how this works. Here, instead of drawing searate circles, we'll draw lines between the points where we would have drawn circles instead, this can be done by simply adding an intermediary variable that stores the previous circles coordinates.

However you can see that the different line segments that make up the stroke can look a little discontinuous and jagged when drawn at higher speeds. Part 5 tries to ammend this by splitting up the line segments into multiple smaller chunks to smooth these portions.
<pre><code>for( let i = 0; i < splitNum; ++i ) {  // ADD
      oldX = x;
      oldY = y;
      x += vx/splitNum;  // AMEND: vx -> vx/splitNum
      y += vy/splitNum;  // AMEND: vy -> vy/splitNum
      oldR += (r-oldR)/splitNum;  // ADD
      if(oldR < 1) { oldR = 1; }  // AMEND: r -> oldR
      strokeWeight( oldR );  // AMEND: r -> oldR
      line( x, y, oldX, oldY );
    }  // ADD
</code></pre>
Where you can choose a value for the 'splitNum' parameter.


We could already stop here, but the last part will put a finishing touch on our brush strokes and make them look much more realistic. In part 6 we draw multiple lines offset from each by a tiny amount and make them have variable thikness

