---
layout: default
title: "Delaunay Triangulation and Voronoi Diagrams"
author: Ahmad Moussa
categories:
  - blog
description: This is the first in a series of blog posts on Delaunay triangulations and Voronoi Diagrams. This first part covers some artworks that have been created with these techniques as well as some of their history and origins.
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/delaunay-intro/mountains.jpg
published: true
exclude_rss: true
legacy: false
listed: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/mountains.jpg" alt="">
</span>
<p></p>


I have to confess... I've been a little obsessed with triangular shapes.

This obsession actually goes back a couple of months ago, when I worked on a little sketch where I tried to pack as many triangles as possible, as closely as possible, somehow coming up with a peculiar triangle packing algorithm. While this take on triangle packing was super fun to make and nets you visually interesting results, an alternative method to split a plane into triangles would be the construction of a Delaunay Triangulation. I attached some of the artworks I've made with Delaunay triangulations to this post, as well as the sketch where this entire triangle thing started:

<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/tri1.png" alt="">
		</span>
	</div>
  	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/tri2.png" alt="">
		</span>
	</div>
</div>
<p></p>

Consider this post an initiation into Delaunay triangulations and related concepts like Voronoi tessellations, as well as the many methods with which they can be achieved. So much interesting art has come from these two techniques, so much so that I believe the topic requires a little attention from everyone who wants to seriously get into creative coding. In terms of different things you can do with these techniques, we've got quite some ground to cover.

Delaunay triangulations were coined after <a href='https://en.wikipedia.org/wiki/Boris_Delaunay'>Boris Delaunay</a> for his research on this topic in 1934, and we'll talk a little bit more about him in one of the following sections, as well as <a href='https://en.wikipedia.org/wiki/Georgy_Voronoy'>Georgy Voronoy</a>, both of which actually knew each other.

<div class="row gtr-50 gtr-uniform">
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/nepal.jpg" alt="">
		</span>
	</div>
	<div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/matterhorn.jpg" alt="">
		</span>
	</div>
  <div class="col-4">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/seceda.jpg" alt="">
		</span>
	</div>
</div>
<p></p>

For now, you just need to know that for our purposes a triangulation simply means the subdivision and/or partitioning of a geometric shape into smaller triangles. Delaunay triangulations additionally  also have many geometric properties that are useful for a plethora of applications across various fields, and can in many ways be used to derive a dual Voronoi diagram, and vice versa!  Let's first have a look at some creative things that have been created with these two space partioning algorithms.

<h2>Creative coding with Delaunay Triangulations</h2>

One artwork that really stands out, and that probably serves as a good starting point would be <a href='https://www.ignaziolucenti.it/'>Ignazio Lucenti's</a> amazing Delaunified portraits:

<iframe title="vimeo-player" src="https://player.vimeo.com/video/141426099?h=8642337800" width="100%" height="460" frameborder="0" allowfullscreen></iframe>

<p></p>

I find these to be incredibly visually appealing, almost like the faces are woven out of thin threads. This is done by first sampling some points on top of the portrait, where the points are more frequent over the coloured regions of the image and then connecting them with a Delaunay triangulation.


As mentioned earlier, delaunay Triangulations always bring with them a dual Voronoi Tesselation, and we'll talk more about the technical aspects of this later, but for now it's interesting to have a look at some more pieces of art that also make use of Voronoi diagrams. The most mind-blowing piece I've seen using Voronoi tessellations is <a href='https://ravenkwok.com/'>Raven Kwok</a>'s generative music video for the track Skylines by Karma Fields, which is just really brilliant on so many levels:

<iframe src="https://player.vimeo.com/video/139977231?h=6660746263" width="100%" height="460" frameborder="0" allow="autoplay; fullscreen; picture-in-picture" allowfullscreen></iframe>
<p><a href="https://vimeo.com/139977231">Skyline</a> from <a href="https://vimeo.com/ravenkwok">Raven Kwok</a> on <a href="https://vimeo.com">Vimeo</a>.</p>


Another artist who's really harnessed Voronoi Tesselations as an expressive medium is <a href='https://www.instagram.com/p/CZh1f4hB98W/'>Michael Kennan</a>. I remember first coming across his work on the generative subreddit, where he posted a piece titled 'Im Sturz durch Raum und Zeit':

<iframe id="reddit-embed" src="https://www.redditmedia.com/r/generative/comments/nxhafx/oc_caution_vertigo_im_sturz_durch_raum_und_zeit/?ref_source=embed&amp;ref=share&amp;embed=true" sandbox="allow-scripts allow-same-origin allow-popups" style="border: none;" height="621" width="100%" scrolling="no"></iframe>

This must have been one of the earliest sketches that I've come across on the subreddit, and left me utterly clueless as to how something like that would be possible. At this point I believe that it makes use of <a href='https://en.wikipedia.org/wiki/Lloyd%27s_algorithm'>Lloyd's algorithm</a> with which you can turn a Voronoi diagram into a centroidal Voronoi diagram. More on that in a future post though.


With a few modifications Voronoi diagrams also allow for interesting stippling and pointillist effects and artworks, which is a huge topic in of itself:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/stippling.png" alt="">
</span>

Stippling is the creation of a pattern simulating varying degrees of solidity or shading by using small dots. And it turns out that Voronoi diagrams can be used to achieve this effect, turning a given source image into one that is made out of many dots. I think that at this point it's clear, that spending some time with Delaunay triangulations and all the related topics is a worthwhile endeavour for creating interesting artworks.

Besides being aesthetically pleasing, Delaunay triangulations are also really useful for a lot of practical applications, for example terrain generation, or creating minimum spanning trees that connect rooms in a <a href='https://www.gamedeveloper.com/programming/procedural-dungeon-generation-algorithm'>procedurally generated dungeon</a>:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
 <img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/room2.gif" alt="">
</span>

One last thing worth mentioning here is that Delaunay triangulations are probably also great tools for creating low-poly graphics and aesthetics, as an ode to an aesthetic from an era long past where game devs had to make do with limited computational resources. Nowadays low-poly games don't always entirely use triangular shapes, <a href='https://blog.displate.com/low-poly-art/'>here</a>'s a really cool post by Martyna Stachowiak from Displate on this aesthetic in any case.


<h2>Origins</h2>

Delaunay Triangulations and Voronoi Diagrams are not only mathematically closely related but also historically. The most comprehensive resource on this relationship is Liebling's and Pournin's paper titled 'Voronoi Diagrams and Delaunay Triangulations: Ubiquitous Siamese Twins'. The very first paragraph reads beautifully:

<blockquote>
Concealing their rich structure behind apparent simplicity, Voronoi diagrams
and their dual Siamese twins, the Delaunay triangulations constitute remarkably powerful and ubiquitous concepts well beyond the realm of mathematics.
This may be why they have been discovered and rediscovered time and again.
They were already present in fields as diverse as astronomy and crystallography centuries before the birth of the two Russian mathematicians whose names they carry. In more recent times, they have become cornerstones of modern disciplines such as discrete and computational geometry, algorithm design, scientific computing, and optimization.
</blockquote>

I really recommend reading at the least the first two sections of <a href='https://www.math.uni-bielefeld.de/documenta/vol-ismp/60_liebling-thomas.pdf'>the paper</a> as it draws a beautiful picture of how both Delaunay triangulations and Voronoi diagrams were discovered and re-discovered over the years, in addition to some interesting anecdotes and stories around the people that explored this field of mathematics. Unsurprisingly Voronoi was actually a friend of Delone's (Boris Delaunay) father, which had a big influence on his later work and can be inferred from the title of his 1934 paper 'Sur la sphere vide. A la memoire de Georges Voronoi.' (French for: On the empty Sphere. In memory of Georges Voronoi.)

At this point in time it is hard to say exactly what kind of relationship Voronoi and Delone had, and it seems like Voronoi was not actually Delone's supervisor (based on the paper I just mentioned). I genuinely couldn't find out more about it however. An additional fun fact here is that Georgy Voronoy was a student of Andrey Markov, who worked on the concept that is better known today as Markov Chains.

The very first instance of Voronoi diagrams dates back much further than Voronoi's times, all the way back to the Renaissance where Renes Descartes created the first Voronoi Diagram on record. Image from the same paper:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/renes.png" alt="">
</span>

Another historical record of Voronoi diagrams is the figure to the right by John Snow (not the guy from Game of Thrones), where he was able to pinpoint a  defective water pump with a Voronoi diagram. That defective pump was at the root of a cholera outbreak in Victorian England. Actually a very interesting story, you can read more about it <a href='https://mbeconsulting.com/lifesaving-maths-you-know-something-john-snow/'>here</a> and <a href='ph.ucla.edu/epi/snow/broadstreetpump.html'>here</a>. I'm not exactly sure of the timeline here, but Dirichlet was working with Voronoi diagrams around the same time, which is why Voronoi diagrams are also sometimes known as Dirichlet Tesselations. Voronoi then later extended Dirichlets ideas to higher dimensions.

<h2>So what's a Delaunay Triangulation?</h2>

<blockquote>A Delaunay triangulation for a given set P of discrete points in a general position is a triangulation DT(P) such that no point in P is inside the circumcircle of any triangle in DT(P).</blockquote>


<div class="row gtr-50 gtr-uniform">
	<div class="col-3">

	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/example.png" alt="">
		</span>
	</div>
  <div class="col-3">

	</div>
</div>
<p></p>



Wikipedia sums up Delaunay triangulations succinctly. A valid Delaunay triangulation hence needs to satisfy the condition that none of it's vertices are inside the circumcircle of any of the triangles that make up this triangulation.

 In other words, given a set of points, we need to draw edges between these points in such a manner that a triangular mesh emerges. None of the initial points are allowed to be contained within the diameter of any circumcircle of the triangles that make up this mesh. Ultimately, all points should lie <b>on</b> these circumcircles, where the circumcircle is the circle centered at the circumcenter of a triangle, which is a point that is equidistant from all three vertices of the triangle.


There's a number of procedures for constructing such a triangulation, one of them being the Bowyer-Watson algorithm, which I'll cover in the next post. Furthermore, as mentioned a couple of times already, we can derive the dual Voronoi diagram from this Delaunay triangulation, simply by connecting the circumcenters of adjacent triangles:

<div class="row gtr-50 gtr-uniform">
	<div class="col-1">

	</div>
	<div class="col-10">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/example2.png" alt="">
		</span>
	</div>
  <div class="col-1">

	</div>
</div>
<p></p>

<h2>Voronoi Diagrams</h2>

A Voronoi diagram can also be constructed without the delaunay triangulation. Given a set of points, we want to partition and assign a portion of the plane to each one of those points, in such a manner that each position in a given region is closer to that point than any other:

<div class="row gtr-50 gtr-uniform">
	<div class="col-3">

	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/delaunay-intro/example3.png" alt="">
		</span>
	</div>
  <div class="col-3">

	</div>
</div>
<p></p>

And again there's a number of algorithms to achieve Voronoi diagrams, I haven't decided yet on which one to cover, but a popular sweep line algorithm is fortune's algorithm (obviously in addition to the Bowyer-Watson algorithm as an indirect approach).

And that's all I've got for you this time around. Keep your eyes peeled though there's gonna be a bunch of new posts very soon on Delaunay triangulations and Voronoi Diagrams, as well as Lloyd's relaxation and weighted Voronoi stippling! Thank you for reading, and if you've enjoyed reading, consider supporting by sharing this post or following me on my social media accounts, cheers!
