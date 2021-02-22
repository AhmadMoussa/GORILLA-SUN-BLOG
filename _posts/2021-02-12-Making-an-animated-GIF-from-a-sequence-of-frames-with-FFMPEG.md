---
title: Making an animated GIF from a sequence of frames with FFMPEG
author: Ahmad Moussa
description: Combining a number of individual frames can be tricky if you haven't done it before, in this article we show how to accomplish it with FFMPEG.
thumbnail_path: 2021-02-12-Making-an-animated-GIF-from-a-sequence-of-frames-with-FFMPEG.png
---
<section>
<div style="width:100%;height:0;padding-bottom:56%;position:relative;"><iframe src="https://giphy.com/embed/l0HlvFUHvDB16UOwU" width="100%" height="100%" style="position:absolute" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div><p><a href="https://giphy.com/gifs/cyberwar-l0HlvFUHvDB16UOwU">via GIPHY</a></p>
  
<p>Gifs are cool, but making them can be tricky. In the past 2 years I've had to make a lot of powerpoints for university presentations, and I've always wanted to make them the best they could possible be, aesthetically and information wise. And what's better than having a lot of cool images and graphs in your presentation? Visualizing processes with animated gifs. Since the focus of my studies were neural networks which involved training them, I would most often want to visualize this learning process by drawing a sample output from my model at every learning iteration (epoch), which in turn would allow me to qualitatively assess how well it was performing. This usually made me end up with a folder on my desktop that held hundreds to thousands of images. Including this sequence of frames as an animated GIF in your powerpoint is very impressive, and usually gives a lot of insight on the training process that you wouldn't usually see if you only inspected single frames.</p>

<p>Naturally, making gifs from a sequence of frames is not only useful academically, but can also be used for other purposes. Since I couldn't figure out how to capture a gif directly from the P5JS web editor of my canvas, I resorted to downloading individual frames and then concatenating them into a gif. Now the problem with gifs is that there has to be some compression algorithm involved that reduces the overall size of the final gif. Digital images can already take up a significant amount of space on your hard disk, depending on the resolution and encoding format, however it usually is the case that there is not a lot of change between two consecutive frames, for example, the backdrop of a scene doesn't always change significantly, only the main object that's moving. In this case it would be very wasteful to not encode some shared information jointly between different frames.</p>

<h2>FFMPEG</h2>

<p>In this post we will mainly focus on using FFMPEG for creating a GIF from a sequence of individual frames. FFMPEG stands for Fast Forward MPEG and is designed to be an open source command-line tool for video and audio handling. In other terms, it allows us to convert video and audio media from any format to any other format. For example, it allows us to convert mp4 videos to the avi format, or wav audio files to mp3, etc. and additionally has a plethora of options to choose from, which allows us to exactly encode data in the exact format that we desire. I often use it to convert m4a audio recordings from my phone to mp3 recordings, which can then be imported in Ableton Live.</p>

<h2>Installation</h2>

<p>Simply head over to <a href='https://ffmpeg.org/download.html'> https://ffmpeg.org/download.html </a> to download the latest version and follow the <a href='https://www.wikihow.com/Install-FFmpeg-on-Windows'> WikiHow </a> steps on how to install it. There would be no point in me repeating the steps here as the WikiHow tutorial already does a great job at it. However the trickiest part of installing FFMPEG might be adding the exectuable to your Path. This step is necessary since FFMPEG only comes as source code, and adding it to your path will allow you to use FFMPEG from the command line from any directory.</p>

<h2>Using FFMPEG</h2>

<p>Once that's done, you'll have to open up a terminal. Go ahead and navigate to the folder where your sequence of frames is, and I'll give you a Pro Tip: you can type 'cmd' in the address bar of the windows file explorer and it'll open a terminal that's already pointed at the directory that you've invoked it from. Next, we'll actually use ffmpeg. Making a gif with ffmpeg is a two step process, first you'll want to turn the sequence of frames into a video, and then convert that video to the .gif format. The first step is done with the following command:</p>

<pre><code>ffmpeg -f image2 -framerate 30 -i image%d.jpg video.mp4
</code></pre>

<p>The first command is 'ffmpeg' which tells the terminal that what follows is an ffmpeg command. '-f image2' is an option that specifies the file type of the input. Here we are indicating that what follows will be a sequence of images. '-framerate 60' specifies the target framerate that we want to have in our output video. This is important as it indicates how many images per second we want to show, and essentially allows you to control the playback speed, if you set it to higher values. '-i image%d.jpg' specifies the name of each image in the sequence of frames in your directory. This is quite important, you want to make sure that your sequence of frames are named in increasing order, so for example 'image%d.jpg' means that ffmpeg expects the frames to be name as follows: image0.jpg, image1.jpg, image2.jpg, ... and so on. 'video.mp4' and lastly we have to specify the name and file exetension of the output, which in this case will be .mp4, but you can try other video formats and see if they work. If not ffmpeg will throw an error.</p>

<p>The next step consists of converting the video into a GIF, and keeping the size of the GIF on disk reasonable. GIFs are very bad at compression, in comparison to the .mp4 format. The gif that I used for testing to write this post had a little over 1000 frames all of which amount to roughly 20mb on disk. The encoded .mp4 at 60 frames per second would only be 150kbs in comparison. This is a considerable difference in size (even though the reduction depends on what is happening in the frames of the video). To convert the .mp4 to a .gif and keep it's size reasonable we can use the following command:</p>

<pre><code>ffmpeg -i video.mp4 -vf "fps=10,scale=320:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -pix_fmt rgb24 -loop 0 out.gif
</code></pre>

<p>This will drastically affect the quality of the .gif and not have a size as small as the .mp4, but will do the job. Credit where credit is due, the command is explained in detail in <a href='https://superuser.com/a/556031' target="_blank" rel="noopener noreferrer">this StackExchange answer</a></p>

</section>
