---
title: A simple guide to FFmpeg
author: Ahmad Moussa
categories:
  - p5js
description: ffmpeg is an amazing tool, that will make your life so much easier as a creative coder. This blog post can be considered as a primer on using FFmpeg, and showcases some of it's capabilities.
thumbnail_path: https://media.giphy.com/media/tGiJyldyNUJXO/giphy.gif?cid=ecf05e47xxf9duke1suqh8gs3vbp28xssx1mse2k10bxgu4k&rid=giphy.gif&ct=g
published: true
exclude_rss: true
---

<span class="image fit"><img src="https://media.giphy.com/media/tGiJyldyNUJXO/giphy.gif?cid=ecf05e47xxf9duke1suqh8gs3vbp28xssx1mse2k10bxgu4k&rid=giphy.gif&ct=g" alt="" /></span>
<p></p>

"This file format is not supported!" If you've ever received a notification like this, and you create content on a regular basis, then this blog post is probably for you!

Being a creative of any sort in this digital age, requires you to juggle a variety of different file formats across different platforms. On top of that, platforms (such as Twitter) have very strict format rules for things you want to upload, this makes it crucial to have an easy way to manage and convert different files to different formats at will.

In this blog post I'll gently introduce you to ffmpeg and how it can help you with converting and editing your files!

<h3>Getting started with FFmpeg</h3>
1. <a href='#ffmpeg'>What is FFmpeg?</a>
2. <a href='#install'>Installing FFmpeg</a>
3. <a href='#syntax'>FFmpeg command syntax</a>
4. <a href='#convert'>File conversion example</a>
5. <a href='#option'>Command Options</a>

<h3>Generally useful FFmpeg commands</h3>
6. <a href='#frames'>Collating frames into a GIF</a>
7. <a href='#scalecrop'>Cropping and Resizing</a>
8. <a href='#shorten'>Shorten duration</a>
9. <a href='#websafe'>Converting to a web safe video format</a>
10. <a href='#multiple'>Converting multiple files in a directory</a>


<h3><a href='#cheatsheet'>Cheat Sheet</a></h3>


<h2><a name='ffmpeg'>1.</a> What's ffmpeg?</h2>
Simply put, FFmpeg is an open source video and audio processing tool. It allows you to convert between different encoding formats, in addition to editing video and audio files in a number of ways. It's mainly designed to be a command-line interface (CLI) that you execute from your terminal (this is how we'll be using it in this blog post), however it can also be integrated as a part of other software.

Know those fancy apps on your phone, that help you format your videos for IG? A lot of them probably use FFmpeg in the background. Can't blame them though, why reinvent the wheel when FFmpeg exists? It's super fast and versatile.

How can WE use FFmpeg? Well, for example, you were just about to upload a GIF file to some website when it notifies you that this file format is not supported. With FFmpeg we could easily convert this GIF file into an mp4 file, or a mov file, or an avi file, etc...

Video file too long? No problem we can trim it with FFmpeg. Video file size too large? No problem we can reduce it's size with ffmpeg. You get the idea! In the rest of this blog post I'll run over the most useful FFmpeg commands (especially for creatives).

<h2><a name='install'>2.</a> Installing FFmpeg</h2>
Maybe the hardest part about using FFmpeg is installing it. This will vary on the operating system that you currently have.

On windows (what I'm using), you'll need to make sure that you create a new environment variable, such that you can FFmpeg from your terminal now matter what file you open the terminal from.

<h2><a name='syntax'>3.</a> FFmpeg command syntax</h2>
Before you have a heart attack, writing FFmpeg commands is actually super easy, but to elucidate how these commands are composed we'll have to have a look at the general FFmpeg command syntax:
<pre><code>ffmpeg [global_options] {[input_file_options] -i input_url} ...
 {[output_file_options] output_url} ...
</code></pre>

FFmpeg commands essentially always consists of two parts, an input stream and an output stream. With streams we generally mean input and output files, and it's improtant to note that you we can have multiple input and multiple output streams. For example, you'd like to overlay two videos and merge them into a single video file. 

For each one of these streams we can specify a number of conversion options that, in addition to converting between two formats, allow us to manipulate specific properties of the file. For example, you want the output file to have a secific frame rate, this could then be specified as an option. As you might've noticed, there are options that are applied specifically to the input stream, others specifically to the output stream, and others to the entire command.

<h2><a name='convert'>4.</a> Converting files with ffmpeg</h2>
The simplest and quintessential ffmpeg command would be the simple conversion command. It goes as shown:
<pre><code>ffmpeg -i input.mp4 output.gif
</code></pre>

In this case we would be converting an mp4 file into a gif file. Notice the statement preceding the input file '-i', this is how an option is specified FFmpeg (I also often call them flags). Here we're telling FFmpeg that what follows is the input file. There are many such options in ffmpeg, each of which has it's own purpose.

Aside videos and gifs, FFmpeg also allows you to convert audio files! I often record my guitar with my samsung phone. It's recording app stores these files in the m4a format, which isn't supported by sound editing applications and DAWs like Ableton Live. A much more sensible format would be MP3 or WAV:

<pre><code>ffmpeg -i recording.m4a output.wav
</code></pre>

Same command as before, only thing that changed are the file types! FFmpeg will automatically handle the conversion between differen file types.

<h2><a name='option'>5.</a> FFmpeg options</h2>
To see useful options that are avaiable in ffmpeg, you can simply type in 'ffmpeg -h' and it will print a relatively long list of different options as well as a description of what they do. If you want to see more or ALL options you can type in 'ffmpeg -h long' and 'ffmpeg -h full' respectively. Let's have a look at some of them:

<pre><code>ffmpeg -i input.mp4 -r 25 output.gif
</code></pre>

Say you have a video file that you'd like to turn into a GIF with a framerate of 25 fps. To achieve that, you'd add the '-r' flag in front of the output file name, along with the rate you'd like to set it to.

Another command that I






<hr class='major'/>







<h2><a name='frames'>6.</a> Converting a series of frames into a gif/video</h2>
One super useful command that I often require for my p5js sketches, is collating/combining a sequence of frames into a video/gif:

<pre><code>ffmpeg -i frame%d.png output.gif
</code></pre>

<a href='https://trac.ffmpeg.org/wiki/Slideshow'>(FYI this is also sometimes called a slideshow, for googling purposes)</a>. 

By manner of using a percentage sign '%' in the input file stream, FFmpeg understands that it should be looking for multiple files that need to be processed. If you've exported them from p5js or processing, usually you'll already have them appropriately named and numbered, generally in a sequential order, something like: frame1.png, frame2.png, frame3.png, ...

Here the '%d' signifies that FFmpeg should expect a sequentially increasing number in this position of the file name. You can learn more about these patterns in <a href='http://ffmpeg.org/ffmpeg-all.html#image2-1'>this section</a> of the official documentation. 

Optionally you may also add the framerate flag to the command, otherwise the default framerate will be 25 fps. And if for any reason whatsoever, you need to convert a single image into a video or gif, you can also do that:

<pre><code>ffmpeg -i img.png output.gif
</code></pre>




<h2><a name='scalecrop'>7.</a> Scaling and Cropping/video</h2>
<h3>Cropping</h3>
The best run-down of cropping with ffmpeg, I've found to be <a href='https://video.stackexchange.com/a/4571'>this stackexchange answer</a>.

The command that allows you to crop a file is the following:

<pre><code>ffmpeg -i in.mp4 -vf crop="out_w:out_h:x:y" out.mp4
</code></pre>

Here we're using the vf flag, which stands for 'video filter', basically applying a video filter to the output stream. This flag is followed by 'crop', which tells FFmpeg that we're using the crop video filter, and we also need to pass some parameters to this cropping filter.

Here the parameters 'out_w' and 'out_h' stand for the width and height of the area that you want to crop out, and equivalently 'x' and 'y' represent the coordinates of the top left corner of the cropping area. Essentially you're recording a rectangular area of the input video, and you're positioning this rectangular area with the parameters passed to the filter.

<h3>Scaling and Resizing</h3>
In a similar manner we can resize an input stream as follows:

<pre><code>ffmpeg -i in.mp4 -vf scale="out_w:-1" out.mp4
</code></pre>

Here we use the 'scale' video filter. To the scale video filter we pass two arguments: a width and a height. In this case we're choosing a specific output width 'out_w' and set the output height to '-1'. -1 essentially tells ffmpeg to calculate the correct output height, such that the output video has the same aspect ratio as the input video. Of course you can also do this the other way around '-1:out_h' as well. Or set both width and height to different values.










<h2><a name='shorten'>8.</a> Shorten Length</h2>

Another command that I've found useful a couple of times:

<pre><code>ffmpeg -i input.gif -ss 00:00:00 -to 00:00:03 output.gif
</code></pre>

Which essentially allows you to truncate the duration of a gif or video file, by setting a specific start and end time stamp in seconds. Trimming the video based on exact frames is a little bit more complicated, and is elucidated in <a href='https://superuser.com/a/459488'>this stackexchange answer</a>.








<h2><a name='shorten'>9.</a> Converting to a web safe format</h2>
Displaying full size GIFs on your website is a bad idea. Their size is huge, and if things don't load quickly, it makes an immediate bad impression. A couple of weeks ago I decided to really clean up my blog and compress my GIFs into a much more manageable file format like MP4. It worked like a charm, but then I opened my blog on my phone to see how it looks, and it turns out that certain browsers only support certain types of pixel formats.

This was a problem that actually drove me a little bit mad for a while, since I couldn't pinpoint what the issue was for a while, until I stumbled upon <a href="https://sidneyliebrand.io/blog/converting-gif-to-web-safe-video-formats-using-ffmpeg">this blog post</a> by Sidney Liebrand, that explains a little what web safe formats are, and provides an FFmpeg command to convert to an appropriate MP4 version:

<pre><code>ffmpeg -i file.gif -movflags +faststart -pix_fmt yuv420p -vf scale="trunc(iw/2)*2:trunc(ih/2)*2" file.mp4
</code></pre>

The moveflags option is not necessary, but is nice to have, since it'll automatically make your video run once it starts loading. The yuv420p  pixel format is necessary for your video to be displayed in certain browsers. And an additional necessity of this pixel format is that the video needs to be resized such that it's width and height are divisible by 2. The <a href='https://trac.ffmpeg.org/wiki/Encode/H.264'>FFmpeg documentation</a> offers a little explanation on this in a note called 'encoding for dumb players':

<blockquote>
You may need to use -vf format=yuv420p (or the alias -pix_fmt yuv420p) for your output to work in QuickTime and most other players. These players only support the YUV planar color space with 4:2:0 chroma subsampling for H.264 video. Otherwise, depending on your source, ffmpeg may output to a pixel format that may be incompatible with these players.
</blockquote>

To display your video on your website, and have it behave like a gif in disguise, is with the following html snippet:

<pre><code>&lt;video autoplay="" loop="" muted="" playsinline="" draggable="true"&gt;
	&lt;source src="path/to/video" type="video/mp4"&gt;
&lt;/video&gt;
</code></pre>

This is also very useful for perfectly looping GIFs. The 'loop' statement allows for seamless transitions from end to start.








<h2><a name='multiple'>10.</a> Converting multiple files in a directory</h2>
This isn't really a command specific to ffmpeg, but neat to have in your inventory. Let's assume we have a folder full of gif files that we'd like to convert to mp4 files, and we don't want to tediously do this file by file. We can do this with some additional syntax as follows: 

<h3>On windows</h3>
<pre><code>FOR /F "tokens=*" %G IN ('dir /b *.gif') DO ffmpeg -i "%G" "%~nG.mp4"
</code></pre>

And for macOS as well as Linux:

<h3>On macOS and Linux</h3>
<pre><code>for i in *.gif; do ffmpeg -i "$i" "${i%.*}.mp4"; done
</code></pre>



<!-- I think this should be it's own blog post "Dithering with FFmpeg" and another about "Motion Blur" with FFmpeg
<h3>Palettegen and Paletteuse</h3>
This is essentially a combination of two commands, one that will extract a color palette from the input video, and another that will reduce the number of colors in a given video to only use the colors in a given palette. Why does this reduce the size of a gif file? Usually the less colors a video/gif file has, the less amount of storage is needed for it to be stored.

The first command makes use of the 'vf' flag, which stands for 'video filter', and uses the palettegen filter, where 'palettegen' stands for palette generate: 

<pre><code>ffmpeg -i file.mp4 -vf palettegen palette.png
</code></pre>

As you might have guessed already, this will extract a representative color from the input video and saves it as a png file. Additionally, the number of colors in this palette can be specified with the 'max-colors' flag. Next we'll want to apply this extracted palette to the original video:

<pre><code>
ffmpeg -i file.mp4 -i palette.png -filter_complex paletteuse file.gif
</code></pre>


The vf flag actually invokes the filtergraph that allows you to chain several video filter operations together. Outside their use for reducing the size of gif and video files, I think that they can also be used for stylistic and artistic pruposes as well.

-->







<h2><a name='cheatsheet'></a>Cheat Sheet</h2>
A summary of the aforementioned commands and what they do:

<div class="table-wrapper">
	<table>
		<thead>
			<tr>
				<th>Use case</th>
				<th>Command</th>
				<th>Description</th>
			</tr>
		</thead>
		<tbody>
			<tr>
				<td>Conversion</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i input.gif output.mp4</code></pre></td>
				<!-- <td>Basic example of an FFmpeg conversion command.</td> -->
			</tr>
			<tr>
				<td>Frames to GIF</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i frame%d.png output.gif</code></pre></td>
				<!-- <td>Combine frames into a GIF.</td> -->
			</tr>
			
			<tr>
				<td>GIF to frames</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i input.gif frame%d.png</code></pre></td>
				<!-- <td>Split a GIF into it's constituent frames.</td> -->
			</tr>
			
			<tr>
				<td>Scaling/Resizing</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i in.mp4 -vf scale="out_w:-1" out.mp4</code></pre></td>
				<!-- <td>Resizes input stream. -1 keeps the aspect ratio along that axis.</td> -->
			</tr>
			<tr>
				<td>Cropping</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i in.mp4 -vf crop="out_w:out_h:x:y" out.mp4</code></pre></td>
				<!-- <td>Crops out rectangular area from input stream.</td> -->
			</tr>
			
			<tr>
				<td>Trim duration</td>
				<td><pre style="margin: 0 0 0 0"><code>ffmpeg -i input.gif -ss 00:00:00 -to 00:00:03 output.gif</code></pre></td>
				<!-- <td>Trims duration of input stream based on start and stop time stamps.</td> -->
			</tr>
			
			<tr>
				<td>Web Safe</td>
				<td><pre style="margin: 0 0 0 0;"><code>ffmpeg -i file.gif -movflags +faststart -pix_fmt yuv420p \
				-vf scale="trunc(iw/2)*2:trunc(ih/2)*2" file.mp4</code></pre></td>
				<!-- <td>Trims duration of input stream based on start and stop time stamps.</td> -->
			</tr>
			
			<tr>
				<td>Multiple Files</td>
				<td>
					Windows <pre style="margin: 0 0 0 0;"><code>FOR /F "tokens=*" %G IN ('dir /b *.flac') \ 
										DO ffmpeg -i "%G" -acodec mp3 "%~nG.mp3"</code></pre>
					<br/>
					MacOs & Linux <pre style="margin: 0 0.5em 0 0;"><code>for i in *.avi; do ffmpeg -i "$i" "${i%.*}.mp4"; done</code></pre>
				</td>
				<!-- <td>Trims duration of input stream based on start and stop time stamps.</td> -->
			</tr>
			
		</tbody>
		<tfoot>
		</tfoot>
	</table>
</div>


<h2><a name='size'></a>Reducing the size of gifs</h2>
This isn't a single command per se, but we'll have a quick look at some strategies that can potentially reduce the size of your gif files. I'd like to give a shout-out to two superb posts on the topic of quality and size of gif files with FFmpeg, this <a href='http://blog.pkh.me/p/21-high-quality-gif-with-ffmpeg.html'>one</a> as well as this <a href='https://cassidy.codes/blog/2017/04/25/ffmpeg-frames-to-gif-optimization/'>one</a>. 

The topic of gifs, compression and size/quality tradeoffs are a can of worms, that better be opened in a separate future post, but for the sake of this section let's have a look at some ways to reduce the file size of gifs. 

One quick and dirty trick that I like a lot, is simply scaling the resolution of gif files down (which we have covered above). This tends to have a dramatic effect on the quality of the gif, but sometimes I just can't be bothered when my GIF file size is just barely above the 15mb size limit of Twitter. Shaving off those 1-2mbs can usually be achieved by scaling down around 10%.




<h2>End notes</h2>
If you've made it this far, I sincerely hope this run down of FFmpeg was useful and learned something new! I'm planning on making another blog post about FFmpeg, where we'll use it for creative purposes and video effects, rather than just using it as a tool for conversion operations.

If you've enjoyed this blog post consider sharing it with a friend! Otherwise subscribe to newsletter or come and say hi on Twitter! Cheers, happy sketching!
