---
title: Rectangle Packing&#58; An incredibly difficult problem that seems trivial at first
author: Ahmad Moussa
categories:
  - p5js
description: Sometimes problems can be much more difficult than they appear to be.
thumbnail_path: 2021-02-17-Linear-Interpolation-in-p5js.png
published: true
---

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles1.png" alt="" /></span>

<p>It often happens that I have an idea for a sketch, which in my mind, seems relatively simple to implement; however, when the time comes and I'm free to start working on it, I have literally no clue where to even begin. This is usually because I severly underestimated the complexity of the problem at hand.</p>
<p>For instance, recently I've stumbled across a number of P5JS and Processing sketches, that try to fit as many simple shapes as possible, where each shape has a different size, into an area of limited size. This is usually called packing, or more comonly known as packing problems. It seemed worthwhile to learn a little bit more about them.</p>

<h2>Packing Problems</h2>

<p>A little bit further down the rabbit hole, I find out that packing problems are actually actually a quite difficult family of mathematical optimization problems. This doesn't discourage me however, but rather makes the problem even more exciting as well as challenging. Wikipedia explains the problem much more eloquently than what I'm capable of:</p>

<blockquote>
Packing problems are a class of optimization problems in mathematics that involve attempting to pack objects together into containers. The goal is to either pack a single container as densely as possible or pack all objects using as few containers as possible. [...] Usually the packing must be without overlaps between goods and other goods or the container walls. In some variants, the aim is to find the configuration that packs a single container with the maximal density. More commonly, the aim is to pack all the objects into as few containers as possible.
</blockquote>

<p>Essentially, we'll want to jampack the largest possible number of items into the least amount of space. This type of problem is usually studied in the context where you have multiple bins or containers which you want to fill optimally with the items at hand, however we're only going to concern ourselves with the case were we have one bin (which is not to be confused with the knapsack problem, which might be a topic for a future post).</p>

<h2>Why is Rectangle Packing so difficult</h2>

<p>Optimization problems are generally not my cup of tea, and even though I've had to take some courses on algorithmic complexity as well as convex optimization during my time at university, it's usually way over my head.</p>

<p>Let's have look at some trivial examples. How would you optimally place a handful of rectangles, such that they take the least amount of space possible? For a small number of rectangles it's probably quiet easy to do by hand:</p>
  
  
 <div class="row gtr-50 gtr-uniform">
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example1.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example2.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example3.png" alt="" /></span></div>
 </div>
 
<p>But I found a number of interesting papers on the topic, one of them was very old: "The bottom-left bin-packing heuristic: An efficient Implementation" from 1983 by Bernard Chazelle. And I have a weak spot for old papers, especially when they have figures that must have taken a lot of effort to make back then. I found it through this heated stackoverflow thread that was also inquiring about a solution for this problem.</p>

<h2> Existing algorithms </h2>

<h2> Not reinventing the wheel </h2>
<p>After staring at my laptop screen for more than two hours with frustration, trying to implement some simple version of a greedy heuristic algorithm that will neatly pack my rectangles, I ultimately gave up and started looking for already implemented algorithms.</p>

<h2> Rectangle packing with python and P5JS </h2>

<h2> Partitioning rectangles for optimal packing </h2>

