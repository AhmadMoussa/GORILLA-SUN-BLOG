---
title: Rotation and Circular Positioning
author: Ahmad Moussa
categories:
  - p5js
description: This blog post explains how to rotate objects around a circle. It also discusses how to position items equidistantly around a circle.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

<div style="width:100%;height:0;padding-bottom:100%;position:relative;"><iframe src="https://giphy.com/embed/xUOwGnC7Jqlgj7FvSE" width="100%" height="100%" style="position:absolute; pointer-events:none;" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div>

<h2>Rotating and positioning elements along the circumference of a circle</h2>
<p>So far, in most of my blog posts we've been dealing with arrangement of elements on a grid, however, creating circular motion should be another weapon in your creative coding arsenal. This light blog post explains how to achieve it in P5JS in a number of different ways. Starting with the simplest form: rotating a point around a circle.</p>

<p>To create a rotating point, we usually need 3 parameters: the center of the circle around which we are rotating, the radius, and the angle. To make this point actually move around the circle, the parameter for the angle needs to be a linearly increasing parameter, which, as always, is time in form of the millis() function. The snippet of code that follows the comment defines the rotating point:</p>

<pre><code>function setup() {
  createCanvas(400, 400);
  centerX = width/2
  centerY = height/2
  radius = 100
}

function draw() {
  background(220);
  stroke(0)
  point(centerX, centerY)
  strokeWeight(10)
  stroke(255,0,0)
  // for what follows simply replace this with the provided snippet
  point(
        centerX + radius * cos(millis()/1000),
    centerY + radius * sin(millis()/1000)
  )   
  // end of what you should replace
}
</code></pre>
<p></p>
<p>The coordinates of the rotating point are defined in terms of sin() and cos(), plugging time into the two functions representing the angle(divided by some arbitrary value to slow down the animation), the visualization that we obtain looks like this:</p>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/Simple.gif" alt="" /></span>
<p></p>

<p>Now, If you're not very familiar with sine and cosine I recommend reading up on them in one of my previous blog posts that explains them in a little more detail <a href='https://gorillasun.de/blog/Continuous-oscillating-motion-in-P5JS-with-Sine-functions'>here</a>. In simple terms, all you need to know is that given a center point and a radius, you can draw a point on the circle (defined by this center and radius) using the sine and cosine functions for a specific angle.</p>
<pre><code>// ----
point(centerX + radius * cos(0), centerX + radius * sin(0))
  point(centerX + radius * cos(HALF_PI), centerX + radius * sin(HALF_PI))
  point(centerX + radius * cos(PI), centerX + radius * sin(PI))
  point(centerX + radius * cos(PI+QUARTER_PI), centerX + radius * sin(PI+QUARTER_PI))
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/sin_n_cos.png" alt="" /></span>
<p></p>

<h2>Intuition and Math behind rotational</h2>
<p>You might ask now for a more detailed definition of sin() and cosine(), and how they allow you to find points on a circle. Essentially it has something to do with triangles, angles and ratios. First we should look at the definition of sine as a trigonometric function:</p>

<blockquote>Sine: The trigonometric function that is equal to the ratio of the side opposite a given angle (in a right-angled triangle) to the hypotenuse.</blockquote>

<p>In this case we already have the angle (we simply choose one, depending where we want to position the point on the circle), and the hypotenuse (the radius), which will allow us to find the opposite side. Since we're dealing with ratios here, we still need to multiply by the radius, to find the exact position and add an offset, which is the center of the circle. This gives us one of the coordinates for our point, we repeat the same thing for the cosine. Putting them together will allow us to rotate a point around a circle.<p> 
  

<p>To explain it in another way, we're calculating the vertical offset from the horizontal line that cuts the circle in two parts, as well as the horizontal offset from the vertical line that cuts the circle in two equal parts, and draw a point at these coordinates. This animation from wikipedia was too compelling not to include, as it perfectly exemplifies what is going on:</p>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/Circle_cos_sin.gif" alt="" /></span>
 
<p>Great, now you might be either satisfied that you can position something on a circle, OR you might be more confused and still curious as to how we found out that we can do such positioning with sine and cosine. I'd like to include this excerpt from <a href='https://www.quora.com/profile/Robert-Cruikshank-2'>Robert Cruikshank</a>'s Quora Answer:</p>

<blockquote>
Always remember: mathematics is part discovery, part invention, and part convention.

Given the Pythagorean theorem and the definition of a circle, one can discover (and prove) that the ratio of circumference to diameter is π.

The definition of cosine and sine for arbitrary angles is just that—a definition, an invention. We chose it that way because it is useful. Likewise, I always get school kids arguing with me that 1^0 should be 0, not 1. I explain to them that they CAN define 1^0 to be 0, it’s just that the math they get will be less streamlined, more clunky, the rules for exponents will have exceptions, etc.
</blockquote>

<p>If all of this is still hazy, then I recommend playing around with sin() and cos() to internalize their behaviour, soon enough you'll have an intuitive understanding, and when you do, I recommend approaching the math behind it again and I promise that it will feel much easier.</p> 

<h2>Applications</h2>
<p>This is kind of the basis for a lot of other things that you can do. For example, what would you have to do to have a 3rd point rotate around the already rotating point? The snippet I provided already has the answer to it!</p>

<pre><code>//--------
  point(
        centerX + radius * sin(millis()/1000),
    centerY + radius * cos(millis()/1000)
  )
  stroke(255,0,255)
  point(
        centerX + radius * sin(millis()/1000) + radius/2 * sin(millis()/500),
    centerY + radius * cos(millis()/1000) + radius/2 * cos(millis()/500)
  )

</code></pre>

<p>You need to be mindful about the speed of the new point, here I'm dividing the new point's time by 500. If both were in sync we wouldn't observe an additional orbiting motion. Very simple, yet very powerful!<p>

<h2>Positioning items around a circle</h2>

<p>Let's go back a little and see how to position an arbitrary number of points around a circle in an equidistant manner. The circumference of a full circle can be expressed in either 2xPI RADIANS or 360 Degrees. Positioning an arbitrary number of points around a circle requires splitting 2xPI evenly among them.</p>

<pre><code>numPoints = 5
  for (i = 0; i < numPoints; i++) {
    point(
      centerX + radius * sin(2 * PI / numPoints * i + millis() / 1000),
      centerY + radius * cos(2 * PI / numPoints * i + millis() / 1000)
    )
  }

</code></pre>

<p>In this manner we simply divide 2xPI by the number of points and multiply by the number of the current point. If you prefer doing this in degrees, P5JS has a function that allows you to set this. This concept also applies if you want to position certain elements along the arc of a circle, for example fanning out a number of lines:</p>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-22-Rotation-along-the-circumference-of-a-circle/fan.png" alt="" /></span>

<pre><code>// ------
strokeWeight(2)
for(i = 0; i < 20; i++){
    line(centerX,centerY,
        centerX+100*sin(HALF_PI+HALF_PI/20*i),
         centerY+100*cos(HALF_PI+HALF_PI/20*i))
  }
</code></pre>

<p>As you can see P5JS also conveniently provides pre-defined constant that define different portions of PI (QUARTER_PI, HALF_PI, PI and TWO_PI).</p>

<h2>Rotation in the WEBGL mode</h2>

<p>Alternatively, you could simply achieve rotation by using the WEBGL mode that provides a conveninent rotate function. This is quite fun and very easy, but allows you to create incredible cool sketches.</p>



