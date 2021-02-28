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

<p>Optimization problems are generally not my cup of tea, and even though I've had to take some courses on algorithmic complexity as well as convex optimization during my time at university, it's usually way over my head, however I will attempt to explain succinctly.</p>

<p>Let's have look at some trivial examples. How would you optimally place a handful of rectangles, such that they take the least amount of space possible? For a small number of rectangles it's probably quiet easy to do by hand:</p>
  
  
 <div class="row gtr-50 gtr-uniform">
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example1.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example2.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example3.png" alt="" /></span></div>
 </div>
 
 <p></p>
 
 <p>Now consider the case where you have to arrange more rectangles, it'll probably take you a lot more time to do so by trial and error, and you can see that some of the examples below are far from being optimal:</p>
 
  <div class="row gtr-50 gtr-uniform">
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example4.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example5.png" alt="" /></span></div>
      <div class="col-4"><span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-27-rectangle-packing/rectangles_example6.png" alt="" /></span></div>
 </div>
 
 <p></p>
 <p>You can see that the complexity of the problem scales with it's size. This innocent looking problem, reveals itself to be a monstrosity of a conundrum. Another problem is, can we even check that our arrangment is optimal? Well, technically yes, however, there is no known strategy to finding that optimal solution. In fact bin packing problems are at least NP-hard, and the best we can do currently is solving them with heuristic methods that find solutions relatively quick. The literature on packing problems is quite extensive, one really interesting paper on the topic is '<a href='http://pds25.egloos.com/pds/201504/21/98/RectangleBinPack.pdf'>A Thousand Ways to Pack the Bin - A Practical Approach to Two-Dimensional Rectangle Bin Packing</a>' by Jukka Jylänki, which is a relatively old paper from 2010. So you can see that I'm probably not going to be able to create a better algorithm than what the literature has already suggested. </p>
  
<p>Another interesting paper was quite old '<a href='https://www.cs.princeton.edu/~chazelle/pubs/blbinpacking.pdf'>The bottom-left bin-packing heuristic: An efficient Implementation</a>' from 1983 by Bernard Chazelle. And I do have a weak spot for old papers, especially when they have figures that must have taken a lot of effort to make back then. I found it through <a href='https://stackoverflow.com/questions/1213394/what-algorithm-can-be-used-for-packing-rectangles-of-different-sizes-into-the-sm'>this heated stackoverflow thread</a> that was also inquiring about a solution for the rectangle packing problem, and is as of now more than 11 years old.</p>

<h2> Creating the visualizations </h2>
<p>After staring at my laptop screen for more than two hours with frustration, trying to implement some simple version of a greedy heuristic algorithm that will neatly pack my rectangles, I ultimately gave up and started looking for already implemented algorithms.</p>

<h3>Python rectpacker</h3>
<pre><code>from rectpack import newPacker

rectangles = [(50,50),(100,50),(25,45),(80,60),(40,60),(40,30)]
bins = [(200, 200)]

packer = newPacker()

# Add the rectangles to packing queue
for r in rectangles:
	packer.add_rect(*r)

# Add the bins where the rectangles will be placed
for b in bins:
	packer.add_bin(*b)

# Start packing
packer.pack()

import matplotlib.pyplot as plt
from matplotlib import patches

output = []
for index, abin in enumerate(packer):
  bw, bh  = abin.width, abin.height
  print('bin', bw, bh, "nr of rectangles in bin", len(abin))
  fig = plt.figure()
  ax = fig.add_subplot(111, aspect='equal')
  for rect in abin:
    x, y, w, h = rect.x, rect.y, rect.width, rect.height
    output.append([x,y,w,h])
    plt.axis([0,bw,0,bh])
    print('rectangle', w,h)
    ax.add_patch(
        patches.Rectangle(
            (x, y),  # (x,y)
            w,          # width
            h,          # height
            facecolor="#00ffff",
            edgecolor="black",
            linewidth=3
        )
    )
  fig.savefig("rect_%(index)s.png" % locals(), dpi=144, bbox_inches='tight')

# printing the rectangle coordinates to plot them in P5JS
print(output)
</code></pre>

<pre><code>
import rectpack.guillotine as guillotine
import rectpack.maxrects as maxrects

#packer = newPacker(pack_algo=guillotine.GuillotineBafSas)
#packer = newPacker(pack_algo=guillotine.GuillotineBssfLas)
#packer = newPacker(pack_algo=guillotine.GuillotineBssfMaxas)
</code></pre>

<h3>Visualizing it in P5JS</h3>

<pre><code>
</code></pre>

