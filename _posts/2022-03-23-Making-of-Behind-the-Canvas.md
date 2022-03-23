---
title: Making of Behind the Canvas
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: false
exclude_rss: true
---

I am exhilarated to have had the opportunity to work on a sketch for Crayon Codes! First off, I'd like to thank Sinan for reaching out to me about this opportunity, and for his continuous feedback throughout the different iterations my sketch went through!

Secondly I'd like to thank all those who've collected an edition or two of 'Behind the Canvas'! It was a joy to make, and as always I learned a lot along the way. Making a sketch where the collector can specify parameters prior to collecting, was yet another beast to conquer.

There's a couple of cool things going on in the sketch, that are worth inspecting a little bit more closely. Such as:

1. Using multiple clip() calls after each other to obtain separate layers, each of which has a unique pattern
2. How each one of the patterns is made
3. An interesting way of scaling and resizing the sketch to the canvas




Inspiration

The blob Shape

Layering

Conditional Dropout

Cosine Palettes

Hiding the overlap point

Transparent texture



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
