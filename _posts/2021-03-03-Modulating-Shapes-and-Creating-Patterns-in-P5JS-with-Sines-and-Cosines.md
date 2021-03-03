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
<p>Essentially, we're creating a grid of objects, each of which holds a number of parameters and <code>display()</code> function to draw itself to the canvas. The parameters include it's position and size, and optionally also it's color. The heavy lifting happens in the <code>display()</code> function. Since every rectangle is continuously redrawn, what if we were to change their size and color over time? We could simply do so by modulating the size attribute with a sine function. Here's an example:</p>

<div style="width:100%; height:200px; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;">
<iframe style="width:100%; height:100%; text-align:center; overflow:hidden; display: block; margin-left: auto; margin-right: auto;" src="https://editor.p5js.org/AhmadMoussa/embed/HaWoY9swq"></iframe>
  </div>


<pre><code>

</code></pre>
