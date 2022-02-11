---
title: Making of Vestige
author: Ahmad Moussa
categories:
  - p5js
description: Notes and thoughts on my generative fxhash token 'Vestige'
thumbnail_path: https://gorillasun.de/assets/images/2022-01-20-An-algorithm-for-polygons-with-rounded-corners/sand.mp4
published: true
exclude_rss: true
---

<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/75.png" alt="">
</span>
<p><i>Vestige #75</i></p>
<p></p>

<h2>A bit of history</h2>
Ever since I've come across Tyler Hobbs' essay <a href='https://tylerxhobbs.com/essays/2020/how-to-hack-a-painting'>'How to hack a painting'</a>, I've been utterly obsessed with simulating watercolor-esque textures in p5. Following the pointers in the essay and spending a long time experimenting and tweaking I think I've had some success at it. Here's one render that I'm really proud of:

<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/gorilla sun 2021/November/painterly.jfif" alt="">
</span>
<p><i>Painterly</i></p>
<p></p>

Some have described this texture as 'painterly', which I think is an apt description. I reused tweaked versions of this technique on several occasions, such as the 30th prompt of genuary: 'Organic looking output only using rectangles'. In some way this was a little bit of a flex, but also a demonstration of the versatility of this technique:

<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/Furboland.png" alt="">
</span>
<p><i>Organic looking output using only rectangular shapes</i></p>
<p></p>

<h2>Making of Vestige</h2>
This technique was the starting point for vestige. Depending on the positioning, the number of layers and the type of shape used, you can obtain various different textures. Even though it's takes a moment to run, the generated backgrounds are so much more interesting than plain regular background:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/watercol.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/watercol2.png" alt="">
		</span>
	</div>
</div>

This type of layering also enabled me to get away with bolder color choices, in that manner each Vestige is assigned two random color palettes that will be used to color the different regions of the background.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/watercolor.png" alt="">
</span>

In some of them it might be a bit more obvious than others, but we partition the background in horiyontal regions. Each ergion obtains two distinct colors, one to color the top part and another than colors the botton part:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/regions.png" alt="">
</span>

In each of these regions we'll draw many rectangles, and randomiye their shape and size. This looks very ugly, but we're not done just yet. Next we'll have to play with the transparency of the color. In p5 the fourth argument of the fill() and stroke() function calls allow you to control this transparency. Let's try assigning a random value between 0 and 255 to this channel:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/regionstransp255.png" alt="">
</span>

This doesn' change much, since if only one of the drawn rectangles botains an alpha value of 255, the other transparent layers won't affect it anymore. We need to bring down this range way down. Here the alpha value can obtain a random value between 0 and 6:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/regionstrasnp6.png" alt="">
</span>

Afterwards we can also play a bit with the blend mode in p5, such that prior to drawing some rectangles we call a different blendMode than the default BLEND one. Here I give a 50/50 chance to using the HARD_LIGHT and BLEND blend modes. The difference is subtle however:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/regionsblend.png" alt="">
</span>

And to glue everything together we need to add some grain. Now we have a noticeable difference, and obtain these silky smooth and grainy gradients:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/regionsgrain.png" alt="">
</span>


<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/25.png" alt="">
</span>
<p><i>Vestige #25</i></p>
<p></p>


<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/22.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/16.png" alt="">
		</span>
	</div>
</div>
<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<p><i>Vestige #22</i></p>
	</div>
	<div class="col-6">
		<p><i>Vestige #16</i></p>
	</div>
</div>
<p></p>


<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/81.png" alt="">
</span>
<p><i>Vestige #81</i></p>
<p></p>

<span class="image fit" style="margin: 0 0 0 0; padding: 0 0 0 0;">
	<img class="viewable" src="https://gorillasun.de/assets/images/vestiges/93.png" alt="">
</span>
<p><i>Vestige #93</i></p>
<p></p>
