---
layout: default
title: "A Simple Solution for Shape Packing in 2D"
author: Ahmad Moussa
categories:
  - blog
description: Shape packing is a popular type of sketch in gen-art, but is often trickier than it might seem when you try to pack anything more complicated than a simple circle. In this post we'll have a look what goes into collision detection as well as a strategy to pack any arbitrary shape in 2D.
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/shape-packing/thumbnail.jpg
published: true
exclude_rss: false
legacy: false
listed: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/banner2.jpg" alt="">
</span>
<p></p>

This blog post is about the age-old creative coding problem of shape packing! Even though it might seem like a simple task at first, it is actually quite an involved undertaking. It also usually entails having a broader look at how collision detection algorithms work. In the following sections we'll work our way up to  an algorithm with which you can essentially pack any sort of geometric shapes, and combinations thereof.


<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/example1.jpg" alt="">
		</span>
	</div>
  	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/example2.jpg" alt="">
		</span>
	</div>
</div>
<p></p>

Before we begin, I want to point out that I had a first encounter with this algo back in February of 2022. I never completed that particular idea, but it led to an interesting packing scheme. You can see some of the artworks my code produced above.


<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/maplands.jpg" alt="">
</span>

I also recently learned that a few others have made their own version of this idea. In June I faced a packing problem where I had to draw all sorts of geometric shapes to the canvas. I was left scratching my head at first and it took me quite a while to find a solution. While researching different methods I came across <a href='https://twitter.com/amygoodchild'>Amy Goodchild’s</a> <a href='https://www.amygoodchild.com/blog/maplands'>writeup</a> on a wonderful sketch of hers called "Maplands" where she used a similar strategy to what I ultimately did to implement my idea.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/fruitpacking.jpg" alt="">
</span>

Later on I also re-discovered <a href='https://twitter.com/tarwin'>Tarwin Stroh-Spijer's</a> <a href='https://openprocessing.org/sketch/1533577'>fruit salad packing sketch</a>, which initially evolved from <a href='https://twitter.com/ippsketch'>Jeff's (aka ippsketch)</a> <a href='https://ippsketch.com/posts/non-circular-packing/'>banana packing sketch</a>. I had a thorough look at Tarwin's beautifully written packing library, and a lot of the code in later sections of this post are based on Tarwin's method. Go check out the folks mentioned above, they're all awesome creative individuals!


<h3>Discussion:</h3>

1. <a href='#1'>Packing Algorithms in Generative Art</a>
2. <a href='#2'>Strategies for Collision Detection</a>
3. <a href='#3'>Collision Detection in Video Games</a>
4. <a href='#4'>Proposed Solution</a>


<h3>Getting our hands dirty:</h3>

5. <a href='#5'>A closer look at Tarwin's Circle Packing Library</a>
6. <a href='#6'>Generalizing for other Shapes</a>
7. <a href='#7'>Compositing Shapes with Circles</a>
8. <a href='#8'>The Shape Packing Procedure</a>
9. <a href='#9'>Complete Example</a>
10. <a href='#10'>Closing Notes</a>

<p></p>

<h2><a name='1'></a>Packing Algorithms in Generative Art</h2>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/tripack.jpg" alt="">
</span>

Packing problems come in various flavours, but generally their overarching concern is positioning items as densely as possible within a given area. Despite its deceptive simplicity, bin packing is actually an <a href='https://stackoverflow.com/a/19510170'>NP-Hard problem</a>, with it's associated decision problem (checking if the items to be packed fit in a certain area), is NP-Complete. I wrote a little about what this means <a href='https://gorillasun.de/blog/rectangle-packing-an-incredibly-difficult-problem'>here</a>, understanding this is not necessary for what follows however. I just find it incredibly interesting though, I keep finding myself coming back to this problem thinking: "Surely this can't be this difficult!"

Packing algorithms are useful for a range of real life scenarios, such as packing boxes in a warehouse, or positioning shapes on a sheet of laser cutting material such that the amount of wasted material is minimised. Packing is a heavily researched area of mathematics and fairly advanced algorithms have been developed to solve different variations of it. In generative art the primary goal of packing algorithms is often the creation of visually pleasing and interesting packing patterns. We also often don't start with shapes that have a pre-determined size (usually real life objects have a pre-determined size duh), but make them up on the go.


Hence gen-art packing algorithms vary a little in that regard. I'd like to show off a few artworks that have in one another or another been made with packing algorithms. Here's two stunning artworks from <a href='https://twitter.com/iso_hedron'>Caleb Ogg</a>:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/Lens3.jpg" alt="">
		</span>
	</div>
  	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/Colorshift2.jpg" alt="">
		</span>
	</div>
</div>
<p></p>

Caleb's pieces <a href='https://superrare.com/0x6125e13fc97f6fc97ea14cf925211e4dc0e99f0f/lens-3-18'>Lens 3</a> and <a href='https://superrare.com/0x6125e13fc97f6fc97ea14cf925211e4dc0e99f0f/colorshift-2-12'>Colorshift 2</a>, part of his ongoing 'Coalesce' series, are some of the most interesting circle packing sketches I've seen in a while. Note that the images shown here (2000x2000px) don't do the originals justice which are absolutely gigantic (16000x16000px). Built on a system of circles and rings, Caleb makes use of an underlying noise field for a nifty creative effect. I especially like how Lens conveys the illusion of multiple overlapping lenses that appear to contort the coloration of the underlying circles, which is done by scaling and multiplying the underlying noise field. Excited to see where this series is heading, always a pleasure when Caleb's sketches bless my timeline.

A little less recent <a href='https://www.infinite.center/2015/08/08/circles-inside-circles/'>example</a> of a circle packing sketch is by none other than Jared Tarbell:
<p></p>
<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/javadpack.jpg" alt="">
</span>

As well as this <a href='https://www.reddit.com/r/generative/comments/ibk6pf/circle_packing_%C3%A0_la_tarbell/'>beautiful homage</a> to Jared's sketch by prolific creative coder <a href='https://twitter.com/dmitricherniak'>Dmitri Cherniak</a>:
<p></p>
<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/dimitripack.webp" alt="">
</span>

If you like these patterns you should also check out the <a href='https://en.wikipedia.org/wiki/Apollonian_gasket'>Appolonian Gasket</a> (alternatively Appolonian circle packing). And for good measure we also need to mention <a href='https://twitter.com/Yann_LeGall'>Yann LeGalle</a> who just utterly blows my mind on a regular basis:

<div style='position:relative; padding-bottom:calc(500px + 22px)'><iframe src='https://gfycat.com/ifr/UnevenOrderlyKodiakbear' frameborder='0' scrolling='no' width='100%' height='500' style='position:absolute;top:0;left:0;' allowfullscreen></iframe></div>

<p></p>

And one of the earliest ones that captivated me and initially motivated me to explore circle packing for myself was this sketch by Piter Pasma's:

<div align="center">

<blockquote class="instagram-media" data-instgrm-captioned data-instgrm-permalink="https://www.instagram.com/p/CLEl7WGH6j8/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/CLEl7WGH6j8/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">View this post on Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center; margin-bottom: 24px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 224px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 144px;"></div></div></a><p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/CLEl7WGH6j8/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">A post shared by Piter Pasma (@piterpasma)</a></p></div></blockquote> <script async src="//www.instagram.com/embed.js"></script>

</div>
<p></p>

I really love this one. The squares are positioned in such a way that circles emerge in a wiggly centered wiggly line, as if an invisible force was repelling them. And indeed that invisible force is most likely an additional constraint that doesn't allow rectangles to be placed if the interesect with the phantom circles. I also really love the hachure lines that get more pronounced the close they get to the circles. All in all a really beautiful composition. And for the plotter lovers we need to also mention plotter afficionado <a href='https://twitter.com/greweb'>Gaëtan Renaudeau (aka Greweb)</a> who's made some really gorgeous plots in that regard:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
  <div align="center">

<blockquote class="instagram-media" data-instgrm-captioned data-instgrm-permalink="https://www.instagram.com/p/CfMciMcNtvE/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/CfMciMcNtvE/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">View this post on Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center; margin-bottom: 24px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 224px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 144px;"></div></div></a><p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/CfMciMcNtvE/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">A post shared by @greweb (@greweb)</a></p></div></blockquote> <script async src="//www.instagram.com/embed.js"></script>

</div>
</div>
<div class="col-6">
<div align="center">

<blockquote class="instagram-media" data-instgrm-captioned data-instgrm-permalink="https://www.instagram.com/p/CfMcZ9ZtAnG/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/CfMcZ9ZtAnG/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">View this post on Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center; margin-bottom: 24px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 224px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 144px;"></div></div></a><p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/CfMcZ9ZtAnG/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">A post shared by @greweb (@greweb)</a></p></div></blockquote> <script async src="//www.instagram.com/embed.js"></script>

</div>
</div>
</div>

<p></p>

In the next section we'll have a closer look at collision detection. We usually require a way to check for collisions between the shapes that we are packing, to see if we inserted a shape at a valid location and that it isn't intersecting or overlapping with other shapes. In this blog post we'll only consider static examples where things don't move around once they've been placed on the canvas. Collision detection however generally is a topic accompanied by moving things, as it is often the case in video games. Think of a classic game like Space Cadet Pinball, or, to give another example, top down 2D billiard simulations. What goes into making one of those games? How do we check if a ball collides with another one, or the boundary of the table? In the next section we'll talk a little about why collision detection is a difficult problem, followed by a little section about how video games approach collision detection. The video game example is relevant because it illustrates an important optimisation that we can make to speed up our packing sketches.




<h2><a name='2'></a>Strategies for Collision Detection</h2>

Collision detection is a difficult problem to solve due to the countless number of shapes that an item could possibly have. Shapes can range from simple circles and squares to something much more complicated like a convex polygon with an arbitrary number of sides, and mathematically it’s tricky to determine if two such shapes have overlapping or intersecting areas. To effectuate such a collision check you would have to define rules for every single way that two shapes could potentially intersect. This check would have to consider their orientation and shape, and finding a set of rules that can do this is just not straightforward, or even possible in some cases. There are just too many ways that two shapes could technically collide. This will become clearer as we have a look at some examples and solutions.

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/circlecollision1.jpg" alt="">
		</span>
	</div>
  	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/circlecollision2.jpg" alt="">
		</span>
	</div>
</div>
<p></p>


One of the simplest types of collisions to compute is that between two circles (or spheres if we're in 3D). This can be done by simply checking if the distance between their centers is less than the sum of their radii. This is the fundamental rule upon which you can build a circle packing algorithm and probably the reason why circle packing sketches occur with an overwhelming abundance in gen-art. A circle packing algorithm in this sense would attempt to iteratively place circles of various sizes on the canvas all the while checking if they ‘collide’ with any of the other circles that have already been drawn to the canvas. It is important to point out that this can become quite slow as the number of circles increases because for each circle we need to check if it collides with any of the other circles. Worth mentioning here again that this illustrates why we need some sort of smarter data structure to keep track of the shapes we've packed as the number of collision checks grows quadratically to the number of shapes that we've added if we're approaching it in a brute force manner. This is a common problem with many algorithms, and also many sketches in creative coding could benefit from this type of optimization. We’ll have a look at a solution later on. For starters, here's a very simple circle packing sketch:

<iframe src="https://openprocessing.org/sketch/1612431/embed/?plusEmbedHash=M2Y2NzViMjhjM2RmM2E3OGJjNzA0NWJjMDMyNzY3ZDAyYWMwMDFhMDFkN2UxYjc2NTZmYjYxNTAwZDZiNDVkNWQ1OTY4ZDcyZmVlYTQ3MzdiMmYzOTBjNDEwYWY4MjhhYTljMGNiZWE3N2U2MDI1MmMzYzc5NDRiMjMyMmU3NTNUeWxPSjVHbmc3YmUzcTNsa2ttUVdobGFHRmlWNlNhWGxNdG02K1RGWUtPRjcyT2g5NHczck9tRW1EUjh2c1hrTFBNUWtOcVhpSXBBUE5XaklObmRiQT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

To go on a little tangent here, instead of predetermining the circle’s size we could alternatively select random positions and grow our circles outward until they hit another circle. This approach is discussed in this coding train video: https://www.youtube.com/watch?v=QHEQuoIKgNE&ab_channel=TheCodingTrain

<iframe src="https://openprocessing.org/sketch/1612473/embed/?plusEmbedHash=YzhiYzk0Y2NhODYxNmE4OTkwMzc5OGNhOTkyYzI0OWEyZTc4ZjRkMWFiNGM1M2ZhMzU3ODNlZDU5Nzk2Y2UxZTA2OTc5MDRhODMxNDc4Y2I1ZmMyYzBkYzZjYTU0ZWI5ZmY4YTkxZDM4YzA0ODMzYmM0ZDNhNDYwNjk3OWE5NmZ5VnZjOWtxRFFwMjh6RlhWd25hSHo0S3cyZ3ZSbmF0MUZPbnpWMExLdkJqODl5V1lnZ24vaUR1Z2drYVR5a1ZyWmFXU3Ixek1aMkNJalpLVElMaEpMQT09&plusEmbedTitle=true" width="100%" height="500"></iframe>
<p></p>

Increasing the difficulty a little bit, a more complicated example is the intersection of two rectangles. The advantage of that circles and spheres have over rectangles and boxes is that they're invariant to rotation, which can not be said about rectangles. Checking collisions between rectangles is relatively easy when both of them are axis-aligned, meaning that both of them are oriented in the same manner as the x and y coordinate axes, and in simpler words tells us that they're not rotated. We would just have to check if the positional coordinate of one of them is within the area of the other, which amounts to one check per axis (2 checks in 2D). This becomes much more difficult however when they’re not axis aligned, in this case, they can be overlapping in all sorts of ways, with one corner protruding from the other rectangle. https://stackoverflow.com/questions/22512319/what-is-aabb-collision-detection


Now let’s assume we’re dealing with a scene containing all sorts of shapes: circles, rectangles, triangles, and other polygons. Let’s also add here that these shapes can have any random orientation. We would need to define a set of rules for each pair of these shapes, which just makes it an incredibly difficult task, especially since there are so many ways that two objects can touch, overlap, and intersect. Defining intersection rules between simple geometric shapes like circles, rectangles, and triangles is probably still manageable, but how would you do it for two arbitrarily shaped polygons? The next section takes a peek at how physics engines in video games solve this problem.

<h2><a name='3'></a>Collision Detection in Video Games</h2>

If you’re a gamer, then you’ve probably at least once in your life had the thought ‘those were some bullsh*t hitboxes’. Hitboxes in video games, are essentially invisible collision boxes, you’ll also often hear the term bounding box, which essentially means the smallest enclosing box that contains a particular object (or set of points). These invisible collision boxes often only approximate the shape of 3D objects in the game world, which in turn causes collisions to not always be incredibly accurate. This sometimes leads to frustratingly unwarranted hits, even when it appears that items haven't touched, and can adversely also lead to instances where items seem to have most certainly touched without any hit being registered.  

The most no-brain solution for solving collision detection is probably drawing an invisible circle underneath a given shape, roughly approximating its collision boundary. In that manner we would just need to check if the collision circles of two items overlap. However this is pretty bad actually, because you’ll often end up with a lot of wasted space in between items when you’re dealing with shapes that aren’t very circular. Imagine a long and skinny shape, the bounding circle would be enormous. We can do better than this, and we'll take some inspiration from physics engines in video games for that purpose. Rectangular boxes are usually a much better fit.

Physics engines in video games often need to balance between accuracy and performance. Generally, collision detection procedures are split into two phases, a ‘broad’ preliminary phase that yields shapes that are closely positioned on the canvas and could potentially be colliding, followed by a ‘narrow’ phase that doubles down on the results of the broad phase and checks if two objects are really touching. For 2D purposes, AABBs, short for Axis-aligned bounding boxes, which we've already discussed earlier are very popular for determining collisions in the broad phase, as they provide a cheap way to roughly estimate if two items are colliding. Additionally, we can throw a space partitioning algorithm on top of this broad phase so that we don’t have to check each shape against all of the other shapes, there isn't really any point in doing a collision check if things are on opposite ends of the screen. As for the narrow phase algorithm a popular choice is the SAT algorithm, short for Separating Axis Theorem, which can accurately determine if two convex polygons intersect. McroPixel has one of the best visual explanations of this:

<iframe width="100%" height="400" src="https://www.youtube.com/embed/oOEnWQZIePs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<p></p>

I’m not going to go too deep into this because we’re not trying to build a full-blown physics engine, but rather a packing procedure that can be extended to arbitrary shapes, and which doesn’t have to run in real time. For a really good explanation adn tutorial about all of the things I mentioned above check out <a href='https://www.toptal.com/game/video-game-physics-part-ii-collision-detection-for-solid-objects '>Nilson Souto’s wonderful post</a>, which helped me understand these concepts.


<h2><a name='4'></a>Proposed Solution</h2>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/shape-packing/meme.jpg" alt="">
</span>
<p></p>

The solution here, basically is that everything can be trivialized to a circle packing problem. Even if we’re dealing with non-circular shapes, we just need to find a way to represent the shapes at hand with a bunch of circles. This can either be done by algorithmically tracing a shape’s outline by many small circles similar to what Amy did in her Mapland’s sketch, by manually defining those positions in a photo editing software like Tarwin did in the initial version of his fruit salad packer, or by using a more elaborate method like in Tarwin’s improved version that uses ray-casting to automatically fill a transparent png with circles. Tarwin's ray-casting code is a bit outside the scope of this post, so we'll limit ourselves to tracing simple geometric shapes algorithmically.

First things first, above I have already shown a circle packing sketch, and we mentioned that it is relatively slow due to it requiring to check all of the other circles before it can place the current circle at an empty spot. We can accelerate this by utilising the space partitioning strategy similarly to what Tarwin uses in his CirclePacker library, which essentially splits up the canvas into a grid on which we will operate, such that instead of having to check circles on the entire canvas, we just need to check circles that fall into the cell that we're trying to insert into, and possibly also it's neighbouring adjacent cells.

<blockquote>The main optimisation is having a grid lookup - coming from the games space it just made sense to me. [...]
</blockquote>

For this space partitioning approach we require a grid-like data structure, where each cell has knowledge of which circles it contains, and each circle has knowledge of which grid cells it falls into. This makes the objects involved a bit more complicated to manage, and has a larger memory overhead. This overhead is incomparable however in comparison to the speed improvements we obtain. In this manner we only need to check for collisions with circles that fall into any of the tiles that the to-be-placed circle is touching, this makes the entire procedure magnitudes faster. To insert composite shapes (that are composited from multiple circles), we then only need to check if all the circles that make up a shape can be placed or not, if only one of them collides with a circle on the canvas, we need to try again. This is easier said than done however, let's tackle the code in the next section.


<h2><a name='5'></a>A closer look at Tarwin's Circle Packing Library</h2>

Tarwin's sketch uses an object called circlePacker that handles everything we described in the previous section with a few useful member functions. Let's have a look at how this works by first creating the circlePacker object and it's member variables:

<pre><code>// the circlePacker object
function makeCirclePacker(wid, hei, gridDivs, gridPadding) {
  // width and height of the circle packing area
  this.wid = wid
  this.hei = hei

  // gridDivs (grid divisions) number of cells we want to split the grid into
  this.gridDivs = gridDivs

  // spacing between circles
  this.pad = gridPadding

  // actual size of the grid cells
  this.gridSizeX = this.wid / this.gridDivs
  this.gridSizeY = this.hei / this.gridDivs

  // array that will contain all of the circles that we'll pack
  // as well as other shapes composited out of circles
  this.items = []

  /*
    function that creates the grid, essentially a 2d array
    each cell is an object that holds it's x and y coordinates
    as well as an array that contains all the circles that exist in this cell
  */
  this.generateGrid = function() {
    const grid = []
    for (let x = 0; x < this.gridDivs; x++) {
      grid[x] = []
      for (let y = 0; y < this.gridDivs; y++) {
        grid[x][y] = { x, y, c: [] }
      }
    }
    this.grid = grid
  }

  // we still need to call the grid creation function
  this.generateGrid()
}
</code></pre>

For clarity's sake I've added a comment to each line that explains what it does and what it is used for. The grid essentially consists of a 2d array of objects, each of which knows it's own x and y coordinates, and additionally holds an array 'c' of all the circles that it contains. This grid structure will greatly facilitate and lessen the number of collision checks that we need to do while inserting a new circle.

As for the functions we've got a couple, the most important probably being the calculation of the distance between two circles. For this we can use p5's inbuilt dist() function. We can also pre-emptively subtract the sum of the radi of the two circles, meaning that we later just need to check if the result is negative or not to see if the circles touch:

<pre><code>// utility to check distance between two circles
this.circleDistance = function(c1, c2) {
  return dist(c1.x, c1.y, c2.x, c2.y) - (c1.r/2 + c2.r/2)
}
</code></pre>

Following this we need two fetcher functions. Firstly we require a function which, given a coordinate, can fetch the grid cell this coordinate falls into, this is needed to see which tile a circle is contained in:

<pre><code>// given an x and y coordinate, returns the grid cell it falls into
this.getTile = function(x, y) {
  return this.grid[floor(x / this.gridSizeX)][floor(y / this.gridSizeY)]
}
</code></pre>

<!--
We also need a function that returns all the circles that collide with a certain position. For this we use the previous function that fetches us the tile of a given coordinate and then loop over it's circle array and check with the circleDistance function to see if they collide, we'll then add them to an array and return all those that give a positive hit:

<pre><code>// get all circles which collide with the current position
this.getCircles = function(x, y) {
  const tile = this.getTile(x, y)
  const circles = []
  tile.c.forEach(
    c => { if (this.circleDistance(c, { x, y, r: 0 }) < 0) circles.push(c) }
  )
  return circles
}
</code></pre>
-->

Because a circle can technically touch multiple grid cells, we also need a function that can fetch all of those cells:

<pre><code>// gets tiles that are touched by a certain circle
this.getGridTilesAround = function(x, y, r) {
  const tl = [
    floor((x - r - this.pad) / this.gridSizeX),
    floor((y - r - this.pad) / this.gridSizeY),
  ]

  const br = [
    floor((x + r + this.pad) / this.gridSizeX),
    floor((y + r + this.pad) / this.gridSizeY),
  ]

  const tiles = []
  for (let i = tl[0]; i <= br[0]; i++) {
    for (let j = tl[1]; j <= br[1]; j++) {
      if (i < 0 || j < 0 || i >= this.gridDivs || j >= this.gridDivs) continue
      tiles.push(this.grid[i][j])
    }
  }

  return tiles
}
</code></pre>

And to put it all together we need a function that actually attempts to add new circles. The function here is called <b>tryToAddCircle</b> because it also takes in an optional parameter called <b>actuallyAdd</b>. We can use this function to either simply check if a circle fits, or to actually add that circle and grow it until it's occupying the most space it can.

<pre><code>// function that attempts to add a circle
this.tryToAddCircle = function(x, y, minRadius = 5, maxRadius = 200, actuallyAdd = true) {

    // create a new object that represent a new circle
    let c1 = { x, y, r: minRadius, t: [] }

    while (true) {
      // break early if outside the circle packing area
      if ( c1.x - c1.r < 0 || c1.x + c1.r > this.wid || c1.y - c1.r < 0 || c1.y + c1.r > this.hei ) {
        return null
      }

      // Get grid cells that the to-be-placed circle touches
      const gridTiles = this.getGridTilesAround(x, y, c1.r)

      // Check against other circles in these tiles
      for (let tile of gridTiles) {
        for (let c2 of tile.c) {

          // get distance between circles
          const d = this.circleDistance(c1, c2)

          // check if this distance is negative ( minus padding )
          // this means a collision was registered
          if (d - this.pad < 0) {

            // if there was a collision and the circle size is still minimum
            // there is no space for the circle to grow so we discard and try again
            if (c1.r === minRadius) {
              return null
            } else {

              // if the circle had a collision and has grown already
              // we either add it or return depending on the actuallyAdd toggle
              if (actuallyAdd) {
                // add to tiles, and tiles to circles
                gridTiles.forEach(t => {
                  this.grid[t.x][t.y].c.push(c1)
                  c1.t.push(`${t.x},${t.y}`)
                })
                this.items.push(c1)
              }

              return c1
            }
          }
        }
      }

      // grow the circle radius
      c1.r += 1

      // if we reach maximum radius size we either add it
      // or return it
      if (c1.r > maxRadius) {
        if (actuallyAdd) {
          // add to tiles, and tiles to circles
          gridTiles.forEach(t => {
            this.grid[t.x][t.y].c.push(c1)
            c1.t.push(`${t.x},${t.y}`)
          })
          this.items.push(c1)
        }

        return c1
      }
    }
  }
</code></pre>

This function looks intricate but it's flow is straight forward really. This function takes as input an x and y coordinate, a minimum and maximum radius and an optional boolean parameter called 'actuallyAdd'. When actuallyAdd is set to false we will not actually create new circles, we will simply check if a circle can fit in a certain position, and how much it can grow it's radius before it collides with another circle.

We create an object to represent the current circle that we're attempting to place. In a while loop we need to do a few things, the first of which is fetching all of the grid cells that this circle intersects. We will need to loop over all of the circles within those tiles and check if our current circle collides with any of them. There are a couple of outcomes:

1. We register a collision while the circle is still at minimum radius. This means that the circle has not grown and there is no room for it to grow. We can break the while loop, return and try again with other coordinates.

2. We register a collision and the circle has a radius larger than the minimum radius that we've specified. If the <b>actuallyAdd</b> toggle is true, we need to do a couple of things. We add the circle to the circle arrays of all the tiles it falls into, add the tiles it touches to it's own tile array, and we also add it to the array of packed items of the circlePacker class.

3. If no hit is registered and the circle keeps growing, we stop when it reaches a maximum circle size and again if the <b>actuallyAdd</b> toggle is true we need to update the appropriate arrays.

In case the <b>actuallyAdd</b> toggle is false we just return the circle object if it can be placed, and null otherwise. This will come in handy in the next section. Now this is really efficient, go ahead and try it for yourself, first run the <b>circlePacker</b> with with the <b>gridDivs</b> parameter set to 1 and then again run it with it set to 100. Now slowly ramp up the circle count for both, you'll quickly see that with a finer <b>gridDivs</b> number it's way faster!



<h2><a name='6'></a>Generalizing for Other Shapes</h2>

Now what Tarwin has setup so far is a great chunk of code, because generalizing it for other shapes is only a little step away. We already discussed that we simply need to composite a shape out of several smaller circles that trace out it's form, or alternatively it's outline, to be able to pack it. In this manner we just need to check if all the circles that make up this shape are 'addeable' according to our circle-packing procedure. For this we will reuse the <b>tryToAddCircle</b> method in a loop and this time actually pass false as the <b>actuallyAdd</b> input parameter. In this manner, if any one of the checks fails we terminate the function and need to try again:

<pre><code>// function that checks if a shape can be placed
tryToAddShape(circles, actuallyAdd = true) {

  // 'circles' here is the array of circles that makes up a specific shape
  for (let c of circles) {
    if (!this.tryToAddCircle(c.x, c.y, c.r, c.r, false)) {

      // if one of these circles can't be placed we return
      // shape can't be placed
      return null
    }
  }

  // if there were no problems then we can safely add all circles
  if (actuallyAdd) {
    circles.forEach(c => this.addCircle(c))
  }
  return circles
}
</code></pre>

To actually add the circles of the shape we require another function that simply adds the circles (second part of the <b>tryToAddShape</b> function):

<pre><code>this.addCircle = function(c) {
  // Break early if out of grid
  if (c.x - c.r < 0 || c.x + c.r > this.wid ||
    c.y - c.r < 0 || c.y + c.r > this.hei
  ) {
    return null
  }

  // Get grid cells it intersects
  const gridTiles = this.getGridTilesAround(c.x, c.y, c.r)

  // Update appropriate arrays
  gridTiles.forEach(t => {
    this.grid[t.x][t.y].c.push(c)
    if (!c.t) c.t = []
    c.t.push(`${t.x},${t.y}`)
  })
  this.items.push(c)
  return c
}
</code></pre>



<h2><a name='7'></a>Compositing Shapes with Circles</h2>

With the previous code in place, we can now actually create a shape made out of circles. Let's have a look at an example, we'll be creating a rectangular shape:


<pre><code>// function that makes a rectangle from circles adapted from Tarwin's 'Shape' class
function makeRectangularShape(sizeW, sizeH) {

  this.original = [] // original unmodified circles

  this.circles = [] // circles after scaling, rotating and translating

  // dimensions of the rectangles
  this.sizeW = sizeW
  this.sizeH = sizeH

  // Create circles that trace outline of the rectangle
  // This is a bit backhanded with trigonometric functions
  // https://math.stackexchange.com/a/279209/673569
  this.makeOriginal = function() {

    for (let a = PI / 4; a < TAU + PI / 4; a += TAU / 4) {

      // a vertex of the rectangle
      let ax = this.sizeW * Math.sign(cos(a)) / 2
      let ay = this.sizeH * Math.sign(sin(a)) / 2

      // the next vertex of the rectangle
      let axn = this.sizeW * Math.sign(cos(a + PI / 2)) / 2
      let ayn = this.sizeH * Math.sign(sin(a + PI / 2)) / 2

      // interpolate between them (can be improved)
      // basically increase number of steps the longer the edge length is
      for (let inter = 0; inter < 1; inter += .02) {
        let xi = ax * inter + axn * (1 - inter)
        let yi = ay * inter + ayn * (1 - inter)

        // push circle to original
        this.original.push({x: xi, y: yi, r: this.sizeW/24})
      }
    }

    // copy original into circles
    this.circles = this.original.map(c => ({
      ...c
    }))
  }

  // scale, rotate and translate
  // function that's used to actually place the shape
  this.scaleRotateTranslate =
   function(scale, rotateRadians, translateX, translateY) {

    // reset circle array
    this.circles = []

    // apply transforms and re-add
    this.original.forEach(c => {
      const x = c.x * scale
      const y = c.y * scale
      const r = c.r * scale
      // rotate and translate each x and y
      const x2 = x * cos(rotateRadians) - y * sin(rotateRadians) + translateX
      const y2 = x * sin(rotateRadians) + y * cos(rotateRadians) + translateY

      this.circles.push({ x: x2, y: y2, r })
    })
  }
}
</code></pre>

Shapes will consist of object that hold two member functions. The first of which is responsible for creating the circles that trace the shape, whereas the second takes care of positioning and transforming the shape. The latter is required to pack a multitude of different variations of the same shape.

Making rectangles, as opposed to squares, with trigonometric functions is a bit tricky. Due to their width and height not being the same we need to use a set of parametric functions that make use of the <a href='https://math.stackexchange.com/a/279209/673569'>sign of <b>cos()</b> and <b>sin()</b></a>. It could be done in an easier manner by defining the corner points of the rectangle and then spanning circles between those. As for the <b>scaleRotateTranslate</b> function, it's shape agnostic. It will simply take the original circles and apply the transforms according to the input parameters we've passed.

<h2><a name='8'></a>The Shape Packing Procedure</h2>
The final piece of the puzzle is to define the shape packing procedure. This is somewhat similar to the <b>tryToAddCircle</b> function, with a few changes.

<pre><code>// We need to declare a few global parameters that will determine the look of our sketch
const MIN_SCALE = 5
const MAX_SCALE = 300
const SCALE_INCREMENT = 0.1
const NUM_ITEM_PLACE_TRIES = 200000

function packShapes() {
  for (let i = 0; i < NUM_ITEM_PLACE_TRIES; i++) {

    // set the shape to have a minimum size
    let currentScale = MIN_SCALE

    // random coordinates
    let x = random(w)
    let y = random(w)

    // random rotation
    let rotateRadians = random(TAU)

    // We need these two variables to determine
    let lastAdded = null
    let lastAddedImage = null

    // random rectangle dimensions
    let randW = random(1,15)
    let randH = random(1,15)

    // let's assume we have two types of shapes rectangles 'R' and triangles 'T'
    let type = random(['R','T'])

    let currentShape = (type=='R')?new makeRectangularShape(randW, randH):new makeTriangularShape(randW);

    // create it's original circles
    currentShape.makeOriginal()

    while (currentScale < MAX_SCALE) {
      currentShape.scaleRotateTranslate(currentScale, rotateRadians, x, y)
      const added = circlePacker.tryToAddShape(currentShape.circles, false)

      if (!added && !lastAdded) break

      if (!added && lastAdded) {
        lastAdded.forEach(c => {
          circles.push({ x: c.x, y: c.y, r: c.r })
        })

        lastAdded.forEach(c => circlePacker.addCircle(c))
        shapes.push(lastAddedImage)
        break

      } else if (added) {
        lastAdded = [...added]

        lastAddedShape = {
          type: type,
          x: x, y: y,
          scale: currentScale, rotation: rotateRadians,
          sizeW: randW, sizeH: randH,
          triangleSizes: (!currentShape.sizeParam)?0:currentShape.sizeParam
        }
      }
      currentScale += SCALE_INCREMENT
    }
  }
}

</code></pre>

In this shape packing function, similarly to the tryToAddCircle function, we're trying to grow shapes starting from a minimum scale. We use two variables <b>added</b> to keep track of each shape, and decide if it can or cannot be placed. In a while loop we attempt to add the shape via the <b>tryToAddShape</b> function, if it can be added it will return an array of of circles, which makes the <b>added</b> variable truthy. If it is not possible to add the shape the <b>tryToAddShape</b> function will return null, which is faulty. In this case if lastAdded is also still null it means that the shape is at minimum scale and can't grow, which indicates that the location is not a good fit. Hence we break and try again. We resume similarly to the the <b>tryToAddCircle</b> function. If <b>added</b> is falsy but lastAdded is truthy it means that the shape has in fact grown, in that case we can terminate the function and add the shape, by adding all of the circles it is made of. We also need to store the positional and transform information of the shape!

If added and lastAdded are both truthy, then the shape still has room to grow and we need to update the lastAdded object that stores information of the previous version of the shape.


<h2><a name='9'></a>A Complete Example</h2>

Albeit having a grid-lookup, it still takes a few moments to run the sketch, which is still better than having to wait several minutes (or hours). Here's a complete example of everything we've discussed so far:

<iframe src="https://openprocessing.org/sketch/1617607/embed/?plusEmbedHash=M2QwN2FjYmM3YWY3MDdmZGM1MDMyNjg5MTIyYjdhYWM2YjlkOTI0MmQ4ZTE3ZDFmZTVlNTVkYjc1MTYxMzViODFiNGQ3NzcwYTY5MmQ4YjcyM2FlY2EwM2YxZmQ1ZDFhYWY4MGEzMzkyMjAxZWMyOTU1M2MwN2E0MDgyNTZhYjRFVnUrOVV3cFRzZEpUWThwWko3TFJ4Q0E2eklxaVp3a1hYL0JXL3RsVVc5YlJYZG1JRHMwaFROZ2RFcHdXUUVmYUdYRmo3SlVuNmRNUkROQnBScWxVQT09&plusEmbedTitle=true" width="100%" height="600"></iframe>


<p></p>

<h2><a name='10'></a>Closing Notes</h2>

For more complicated shapes, like in Tarwin's original fruit salad sketch you have the option of compositing these circles in a photo editing software, then exporting them as array an loading them in. Alternatively you can go and give Tarwin's <a href='https://openprocessing.org/sketch/1546264'>circle fill algorithm</a> a look.

If you want to do real-time collision detection you’d better stick to an already established physics engine, like Matter.js that allows you to create all sorts of rigid bodies. I found it a bit difficult to fine-tune though, the bodies were a bit too elastic for my taste, but I didn't spend much time with it, so you might get better results! If you want to learn about collision detection between simple shapes and how to do it in p5js, there is an excellent book by Jeff Thompson on this topic <a href='https://www.jeffreythompson.org/collision-detection/index.php'>here</a>.

And that's it, if you've reached here kudos to you and many thanks for reading! If you've learned something and enjoyed this post consider sharing it with your friends, giving me a follow on Twitter or IG. Thanks again for reading and happy sketching, cheers!

<!--
Other interesting thing: https://mary.codes/blog/art/shape_packing_with_p5js/

Fangkai powerpoint: https://www.kth.se/social/files/574684e8f2765458722b5565/DH2323%20Collision%20Detection%20I.pdf
-->
