---
title: An Algorithm for Irregular Grids
author: Ahmad Moussa
categories:
  - p5js
description: An in depth look into the inner workings of my crayon codes sketch Behind the Canvas
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: true
exclude_rss: true
listed: true
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

You can check out the entire collection <a href='https://openprocessing.org/crayon/28'>here</a>. I think that there's a couple of cool things going on in the sketch, that from a technical and ideative point of view are worth inspecting a little bit in more detail:

1. <a href='#idea'>From initial idea to final sketch</a>
2. <a href='#opc'>Setting up our sketch with the OPC Configurator 3000</a>
3. <a href='#smooth'>Creating smooth Polygons</a>
4. <a href='#determinism'>Notes on preserving sketch determinism</a>
5. <a href='#resize'>Resizing and Rescaling</a>
6. <a href='#end'>Closing Notes</a>

<h2><a name='idea'></a>From initial idea to final sketch</h2>

<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/behind_the_canvas/cut.mp4" type="video/mp4">
  </video>
</div>
