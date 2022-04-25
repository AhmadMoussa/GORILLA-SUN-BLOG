---
title: An Algorithm for Irregular Grids
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: false
exclude_rss: true
legacy: false
listed: false
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


Grids, in their various shapes and forms, have been an important backbone for my sketches since I started. Some of the best sketches that I've seen to date begin with a grid and evolve from there, for example Julien Gachadoats 'Umwelt', or Jeff's (aka ippsketch) Bent series, or William Mapan's Generative Tapestry just to name a few among many, many others.


Lately I've been thinking a lot about generative art 'archetypes', and I believe that grids have secured their place as such, as they constitute an important backbone that many sketches build upon. Two influential examples from an early generative art period would be Georg Nees' 1968 work Schotter (german word for Gravel) and Vera Molnár's 1974 artwork (Dés)Ordres:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/nees.png" alt="">
		</span>
	</div>
  <div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/molnar.png" alt="">
		</span>
	</div>
</div>


Grids are by nature 'orderly'. We generally use grids and tables to neatly display information and delimit text/numbers in such a way that it is easy for the eye to follow. Observing these two artworks reveals a characteristic that is inherent to many contemporary generative pieces: the interplay between order and chaos. Our brains are pattern recognition machines, and introducing twists and irregularities in repetitive patterns most likely triggers something within us.


More contemporary pieces that make use of grids in various flavours are <a href='https://feralfile.com/artworks/umwelt-hyf?fromExhibition=graph-eg6'>Julien Gachadoat's Umwelt</a>, <a href='https://ippsketch.com/bent/'>ippsketch's Bent series</a> as well as <a href='https://twitter.com/williamapan/status/1516831897334210570' >William Mapan's generative tapestry</a>, just to name a few:

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/umwelt.png" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/bent.png" alt="">
		</span>
	</div>
  <div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/mapan.png" alt="">
		</span>
	</div>
</div>

I also recommend reading ippsketch's excellent article on how Bent was made! Here I'd like to conclude this little interlude and dedicate the rest of the post towards an grid-construction algorithm that I have used extensively in the past weeks. The algo is relatively straight-forward and unoptimized, but quite versatile. Here's a little index:



Explanations:
1. <a href='#grids'>Interlude</a>
2. <a href='#irregular'>Irregular Grids?</a>
3. <a href='#strat'>A strategy for constructing Irregular Grids</a>

The Algorithm:
1. <a href='#bool'>A Boolean Grid</a>
2. <a href='#pack'>The Packing Procedure</a>
3. <a href='#gap'>Leftover Gaps?</a>
4. <a href='#styles'>Different Grid Styles</a>

Extras:
1. <a href='#indx'>Indexing Cells and Adjacency</a>
2. <a href='#recursive'>Recursive Subdivision</a>



<h2><a name='regular'></a>Grids and their Variations</h2>

Most of my first sketches were based on simple grids:

They allowed me to explore concepts like <a href='https://gorillasun.de/blog/Making-of-Gzork'>SDFs</a> and <a href='https://gorillasun.de/blog/Introduction-to-Perlin-Noise-in-P5JS-and-Processing'>noise fields</a>. These two posts are also good starting points if you'd like to learn how to construct grids! I have also experimented quite a bit with hexagonal grids and written about different strategies to construct them <a href='https://gorillasun.de/blog/A-guide-to-Hexagonal-Grids-in-P5JS'>here</a>.


First, I'd like to discuss a little what I mean with an irregular grid because I have something very specific in mind. When you google 'irregular grid' you get a bunch of odd looking grid layouts, some of them look like this:


Here are a few strategies for creating odd looking grids. <b>Cick on the thumbnail of each grid and it will take you to it's corresponding code in the p5 editor</b>:

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/gF1cgJ1rX'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/grid.png" alt="">
  		</span>
    </a>
	</div>

  <div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/LfCknGtBU'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/recursiveregular.png" alt="">
  		</span>
    </a>
	</div>

	<div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/HicH5_LjC'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/recursiveirregular.png" alt="">
  		</span>
    </a>
	</div>
</div>


<div class="row gtr-50 gtr-uniform">

  <div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/EKXXHHhkt'>
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/randomspacing.png" alt="">
      </span>
    </a>
  </div>

  <div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/59wTZ0i_s'>
      <span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
        <img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/quad.png" alt="">
      </span>
    </a>
  </div>

	<div class="col-4">
    <a href='https://editor.p5js.org/AhmadMoussa/sketches/iNhMm5J8v'>
  		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  			<img class="viewable" src="https://gorillasun.de/assets/images/irregular_grids/distorted.png" alt="">
  		</span>
    </a>
	</div>
</div>







<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/behind_the_canvas/cut.mp4" type="video/mp4">
  </video>
</div>
