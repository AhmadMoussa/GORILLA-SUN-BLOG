---
title: A guide to making GIFs in P5JS and Processing
author: Ahmad Moussa
description: This article is a run down of all the possible ways you can turn your sketch into a GIF.
thumbnail_path: 2021-02-12-Making-an-animated-GIF-from-a-sequence-of-frames-with-FFMPEG.png
published: true
---


<div style="width:100%;height:0;padding-bottom:56%;position:relative;"><iframe src="https://giphy.com/embed/l0HlvFUHvDB16UOwU" width="100%" height="100%" style="position:absolute; pointer-events:none;" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div>
<p></p>
  
<div class='box'>
  <strong>Update 06/16/2021: 
    <ul>
      <li>Title changed from 'Making an animated GIF from a sequence of frames with FFMPEG'</li>
      <li>Fixed typos, added subheadings and line breaks.</li>
      <li>Rewrote sections and added significantly more info and other useful FFMPEG commands.</li>
   </ul>
  </strong> 
</div>

1. <a href='#turn'>Turning your sketches into GIFs</a>
2. <a href='#frames'>Exporting your Sketches as a Series of Individual Frames</a>
3. <a href='#gif'>Converting a Series of Frames into a GIF</a>
4. <a href='#Useful'>Other useful commands!</a>
  
<p></p>

<!--
<h2><a name='intro'>GIFs. GIFs. GIFS</a></h2>
<p>If you've been on the Internet in the past decade or so, then you've probably used a GIF before.</p> 
  
<p>GIFs have become incredibly popular in recent years, to such an extent that GIFs have earned their didcated integration in most modern messaging and social media apps. And deserrvedly so, sometimes a GIF can express that you wouldn't otherwise be able to express with words. </p>
  
<p>Very often, a well timed GIF in a heated whatsapp conversation can be incredibly funny. Other times, a GIF of an iconic scene from a beloved movie or series can encapsulate a specific feeling incredibly well. GIFs also play an integral role in meme culture, but more to that on another day!</p>
 
<h2>What are GIFs... like actually? </h2>

<p>Gifs are cool, but making them can be tricky. In the past 2 years I've had to make a lot of powerpoint presentations for univeristy, and I've always wanted to make them the best they could possible be, aesthetically and information wise. And what's better than having a lot of cool images and graphs in your presentation? Visualizing processes with animated gifs.</p>
  
<p>Since the focus of my studies were neural networks which involved training them, I would most often want to visualize this learning process by drawing a sample output from my model at every learning iteration (epoch), which in turn would allow me to qualitatively assess how well it was performing. This usually made me end up with a folder on my desktop that held hundreds to thousands of images. Including this sequence of frames as an animated GIF in your powerpoint is very impressive, and usually gives a lot of insight on the training process that you wouldn't usually see if you only inspected single frames.</p>


<h2>GIFs from p5js and processing</h2>
<p>Naturally, making gifs from a sequence of frames is not only useful academically, but can also be used for other purposes. Since I couldn't figure out how to capture a gif directly from the P5JS web editor of my canvas, I resorted to downloading individual frames and then concatenating them into a gif. Now the problem with gifs is that there has to be some compression algorithm involved that reduces the overall size of the final gif. Digital images can already take up a significant amount of space on your hard disk, depending on the resolution and encoding format, however it usually is the case that there is not a lot of change between two consecutive frames, for example, the backdrop of a scene doesn't always change significantly, only the main object that's moving. In this case it would be very wasteful to not encode some shared information jointly between different frames.</p>

-->

<h2><a name='intro'>Why make GIFs from your sketches?</a></h2>
You spent hours coding up a nice sketch in P5JS or Processing, ironed out those last few bugs, and made it run smoothly at a crispy 50FPS... but now what?

Share it on Twitter, of course!

Wait a second... how do you actually do that?

Making GIFs out of your creative coding sketches can be tricky and tedious. If you haven't done it before. But there are a number of tools and tricks that can make our life easier in this regard, which we'll go over in this blog post. 

Actually, the really hard part is making your GIFs smaller than 15mb. Screw you Twitter. But fret not, everything in due time. Let's take this one step at a time.

<h2><a name='frames'></a>Exporting your Sketches as a Series of Individual Frames</h2>

Generally the first step is to extract individual frames from  we'll first have to extract and save individual frames from our animation and then convert them into a complete GIF. We'll tackle these two steps, one at a time!

<h3><a name='framesPro'></a>In Processing:</h3>
In processing, saving an indivdiual frame is very straight forward. We can simply call the save("frame.png"); function in the draw loop, and an image will appear in the corresponding sketch folder. Obviously, we'll have to pass a different name for each frame, otherwise we would be overwriting the same file over and over again. We can do this with the help of a counter that gets incremented every draw loop:

<pre><code>int counter = 0;
void draw(){
   save(counter + ".png");
   counter++;
}
</code></pre>

Notice that in java it will implicitely cast the counter integer to a string. 

<h3><a name='framesP5'></a>In P5JS:</h3>
In p5js it's a little bit trickier, but we have a couple of options here, in no particular order:

<ul>
  <li>Making use of the saveCanvas function. Golan Levin has a fantastic <a href='https://github.com/golanlevin/LoopTemplates/blob/master/animgif_p5js/sketch.js'>loop template</a> for this purpose (among others)</li>
  <li>Using CCapture, to export the frames. For this one, Jeff++ has made a fantastic loop template and in depth explanation for this which can be found <a href='https://github.com/ippsketch/p5js-animation-to-png-template'>here</a> and an in depth explanation <a href='https://ippsketch.com/posts/making-gifs-with-p5js/'>here</a></li>
  <li>If you're lazy like me, you can use the createLoop package, which can be found <a href='https://www.npmjs.com/package/p5.createloop'>here</a></li>
</ul>

<h3>The saveCanvas() function</h3>
Golan Levin's template works well, however you might have to rate limit the frame rate of your sketches for this to work well. In other words, running your sketch at full 50-60FPS, and trying to save each individual frame in quick succession might be taxing on your browser. I've tried with chrome and microsoft edge and in both cases they tried to throttle these quick downloads, making it such that many frames were missing in the final output folder.

After some help from the birbs nest discord it seems that setting the FPS via the frameRate() command to something low like 1-5 frames per second, makes it much more consistent and reliable. However it takes a tad bit longer that way.

<h3>Saving frames with CCapture</h3>
CCapture is a javascript library for capturing canvas based animations, such as the P5JS canvas for example. The library can be found <a href='https://github.com/spite/ccapture.js/'>here</a>.

Jeff's <a href='https://github.com/ippsketch/p5js-animation-to-png-template'>template</a> is simply fantastic and everything you basically need. After running your sketch and setting the parameters in the code, you'll receive a .tar file that holds all the frames that you require to make your GIF.

<h3>Using the createLoop package</h3>
This one doesn't give you the individual frames per se, but directly creates a GIF underneath the P5JS canvas, which you can then save to your device. Since a GIF is merely a concatenation of frames, you could then load that GIF into your editor of choice to make some changes, or manipulate it with a command line tool like FFMPEG.

The createLoop package can be used as follows in your sketch, where you'll have to add a new script tag in the html file, and a single line in the setup function of your sketch. The html:
<pre><code>&lt;html&gt;
  &lt;head&gt;
    &lt;script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"&gt;&lt;/script&gt;
    &lt;script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/addons/p5.sound.min.js"&gt;&lt;/script&gt;
    
    &lt;!-- The createloop tag --&gt;
    &lt;script src="https://unpkg.com/p5.createloop@0.2.8/dist/p5.createloop.js">&lt;/script&gt;
    
  &lt;/head&gt;
  &lt;body&gt;
    &lt;script src="sketch.js"&gt;&lt;/script&gt;
  &lt;/body&gt;
&lt;/html&gt;
</code></pre>

And your setup function will look as follows:
<pre><code>function setup() {
    createCanvas(400, 400)
    /*
      other stuff you want to do in the setup function
    */
    
    FPS = 50
    frameRate(FPS)
    createLoop({duration:3, gif:true})
}
</code></pre>

The createLoop function will automatically render your GIF at the frame rate given to the frameRate() function. Also note that the createLoop() function has a number of other options, a rundown of which you can find in the documentation <a href='https://www.npmjs.com/package/p5.createloop'>here</a>. 

One downside that I want to point out, is that when you try to create large GIFs with createLoop(), for example a 10 second GIF at 50 FPS, it will take a significant amount of time. And there is no indication of progress. However it's still being worked on, and might be my go to way in the future.

Next up, concatenating your frames into an actual GIF!

<h2><a name='gif'>Converting a Series of Frames into a GIF</a></h2>

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

