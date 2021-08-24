---
title: A simple guide to using ffmpeg
author: Ahmad Moussa
categories:
  - p5js
description: ffmpeg is an amazing tool, that will make your life so much easier as a creative
thumbnail_path: https://media.giphy.com/media/tGiJyldyNUJXO/giphy.gif?cid=ecf05e47xxf9duke1suqh8gs3vbp28xssx1mse2k10bxgu4k&rid=giphy.gif&ct=g
published: true
---


"This file format is not supported!" If you've ever received a notification like this, and you create content on a regular basis, then this blog post is probably for you!

Being a creative of any sort in this digital age, requires you to juggle a variety of different file formats across different platforms. On top of that, platforms (such as Twitter) have very strict format rules for things you want to upload, this makes it crucial to have an easy way to manage and convert different files to different formats at will.

<span class="image right"><img src="https://media.giphy.com/media/tGiJyldyNUJXO/giphy.gif?cid=ecf05e47xxf9duke1suqh8gs3vbp28xssx1mse2k10bxgu4k&rid=giphy.gif&ct=g" alt="" /></span>

In this blog post I'll gently introduce you to ffmpeg and how it can help you with converting and editing your files!

1. <a href='#ffmpeg'>What's ffmpeg?</a>
2. <a href='#install'>Installing ffmpeg</a>
3. <a href='#syntax'>ffmpeg command syntax</a>
4. <a href='#convert'>Converting Files with ffmpeg</a>
5. <a href='#option'>ffmpeg options</a>
6. <a href='#frames'>Collating frames into a GIF</a>
7. <a href='#size'>Reducing file sizes</a>

<h2><a name='ffmpeg'></a>What's ffmpeg?</h2>
Simply put, ffmpeg is an open source video and audio processing tool. With it you can convert between different encoding formats, in addition to editing video and audio files in a number of ways. It is mainly designed to be a command-line interface (CLI) that you execute from your terminal, however it can also be integrated as a part of other software.

Know those fancy apps on your phone, that help you format your videos for IG? A lot of them probably use ffmpeg in the background. Can't blame them though, why reinvent the wheel when ffmpeg exist? It's super fast and gets the job done, save very exceptional cases.

How can WE use ffmpeg? Well, for example, you were just about to upload a GIF file to some website when it notifies you that this file format is not supported. With ffmpeg we could easily convert this GIF file into an mp4 file, or a mov file, or an avi file, etc...

Video file too long? No problem we can trim it with ffmpeg. Video file size too large? No problem we can reduce it's size with ffmpeg. You get the idea! In the rest of this blog post I'll run over the most useful ffmpeg commands for creatives.

<h2><a name='install'></a>Installing ffmpeg</h2>
Maybe the hardest part about using ffmpeg is installing it.

<h2><a name='syntax'></a>ffmpeg command syntax</h2>
Before you have a heart attack, writing ffmpeg commands is super easy, but to elucidate how ffmpeg commands are composed we'll have to have a look at the general ffmpeg command syntax:
<pre><code>ffmpeg [global_options] {[input_file_options] -i input_url} ...
 {[output_file_options] output_url} ...
</code></pre>

ffmpeg commands essentially always consists of two parts, an input stream and an output stream. With streams we mean input and output files. For these streams we can also specify a number of conversion options that, in addition to converting between two formats, allow us to manipulate specific properties of the file.

So for example, if you'd like to change the framerate of the converted file, you can specify that as well. That would be just one example of MANY things that you can specify.

<h2><a name='convert'></a>Converting files with ffmpeg</h2>
The simplest and quintessential ffmpeg command would be the simple conversion command. It would look as follows:
<pre><code>ffmpeg -i input.mp4 output.gif
</code></pre>

In this case we would be converting an mp4 file into a gif file. Notice the statement preceding the input file '-i', this is called an option in ffmpeg (or alternatively a flag), and specifies that what follows is the input file. There are many such options in ffmpeg, each of which has it's own purpose.

ffmpeg also allows you to convert audio files! I often record my guitar with my samsung phone. It's recording app stores these files in the m4a format, which isn't supported by sound editing applications and DAWs like Ableton Live. A much more sensible format would be MP3 or WAV:

<pre><code>ffmpeg -i recording.m4a output.wav
</code></pre>

Same command as before, only thing that changed are the file types!

<h2><a name='option'></a>ffmpeg options</h2>
To see useful options that are avaiable in ffmpeg, you can simply type in 'ffmpeg -h' and it will print a relatively long list of different options as well as a description of what they do. If you want to see more or ALL options you can type in 'ffmpeg -h long' and 'ffmpeg -h full' respectively. Let's have a look at some of them:

<pre><code>ffmpeg -i input.mp4 -r 25 output.gif
</code></pre>

Say you have a video file that you'd like to turn into a GIF with a framerate of 25 fps. To achieve that, you'd add the '-r' flag in front of the output file name, along with the rate you'd like to set it to.

Another command that I

<h2><a name='frames'></a>Converting a series of frames into a gif/video</h2>
One super useful command that I often require for my p5js sketches, is collating a sequence of frames into a video/gif:

<pre><code>ffmpeg -i %03d.png output.gif
</code></pre>

<h2><a name='size'></a>Reducing the size of gifs</h2>
