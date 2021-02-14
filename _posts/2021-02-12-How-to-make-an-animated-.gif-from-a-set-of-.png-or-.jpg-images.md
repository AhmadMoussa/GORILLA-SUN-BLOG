---
title: How to make an animated .gif from a set of .png or .jpg images
author: Ahmad Moussa
categories:
  - ableton
description: Combining a number of individual frames can be tricky if you haven't done it before, in this article we discuss a number of ways to accomplish it.
thumbnail_path: 2021-02-12-How-to-make-an-animated-.gif-from-a-set-of-.png-or-.jpg-images.png
---

Gifs are cool, but making them can be tricky. In the past 2 years I've had to make a lot of powerpoint presentations for university presentations, and I've always wanted to make them the best they could possible be, aesthetically and information wise. And what's better than having a lot of cool images and graphs in your presentation? Visualizing processes with animated gifs. Since the focus of my studies were neural networks which involved training them, I would most often want to visualize this learning process by drawing a sample output from my model at every learning iteration (epoch), which in turn would allow me to qualitatively assess how well it was performing. This usually made me end up with a folder on my desktop that held hundreds to thousands of images. Including this sequence of frames as an animated GIF in your powerpoint is very impressive, and usually gives a lot of insight on the training process that you wouldn't usually see if you only inspected single frames.

Naturally, making gifs from a sequence of frames is not only useful academically, but can also be used for other purposes. Since I couldn't figure out how to capture a gif directly from the P5JS web editor of my canvas, I resorted to downloading individual frames and then concatenating them into a gif. Now the problem with gifs is that there has to be some compression algorithm involved that reduces the overall size of the final gif. Digital images can already take up a significant amount of space on your hard disk, depending on the resolution and encoding format, however it usually is the case that there is not a lot of change between two consecutive frames, for example, the backdrop of a scene doesn't always change significantly, only the main object that's moving. In this case it would be very wasteful to not encode some shared information jointly between different frames.

<h3>Gif making tools</h3>
We'll at some tools to create animated gifs from a sequence a frames, which already take care of compression, and provide of additional options for you to choose from:
<ul>
  <li>FFMPEG: which stands for Fast Forward MPEG and is designed to be an open source command-line tool for video and audio handling.</li>
  <li>ImageMagick: another command-line tool similar to FFMPEG, but specifically designed for manipulating images.</li>
  <li>Online Converters: my least favorite option, but there's a lot of websites out there where you can upload your sequence of frames and download the gif.</li>
  </ul>
