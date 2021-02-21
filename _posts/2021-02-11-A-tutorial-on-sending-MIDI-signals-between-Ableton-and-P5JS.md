---
title: Sending MIDI signals between Ableton and P5JS
author: Ahmad Moussa
categories:
  - ableton
description: A tutorial on sending MIDI signals between Ableton and P5JS.
thumbnail_path: 2021-02-11-A-tutorial-on-sending-MIDI-signals-between-Ableton-and-P5JS.png
---
<div class="row gtr-200">
	<div class="col-8 col-12-medium">
<p>In this post we have a look a WebMIDI.js, a javascript library that'll allow to handle MIDI signals in p5js. This is especially useful if you want to create a visual interactive midi controller, or control you sketches with MIDI signals sent from your daw. In this post I'll show you how to do the latter.</p>

<h4>Importing WebMIDI.js</h4>
<p>In an earlier blog post, I explain how one can send MIDI signals from a python script to ableton live, which requires us to create a virtual port.  This time around we'll also need the LoopMIDI virtual port that we have used for the python tutorial, so go ahead and download it from <a href='http://www.tobias-erichsen.de/software/loopmidi.html' target="_blank" rel="noopener noreferrer">here</a>.</p>

<p>Next we'll hop over to the web editor where we have to configure a couple of things. In the web editor, directly underneath the the large 'play' button, there is a smaller arrow button. Clicking on it reveals to use that there are other files, in addition to 'sketch.js' which we are currently editing. Click on 'index.html'. In the '<head>' tag you can a number of lines that are importing the P5JS library for us to use. Here we can also import other javascript libraries, such as WebMIDI.js. Go ahead and add the following line in the head tag of index.html:</p>
  
<pre><code>&lt;script src="https://cdn.jsdelivr.net/npm/webmidi"&gt; &lt;/script&gt;</code></pre>


<h4>Enabling WebMIDI</h4>
<p>The next thing we need to do is to check that WebMIDI works and that we can see the accesible MIDI ports. Inside the setup function we need to insert the following snippet of code:</p>

<pre><code>
WebMidi.enable(function(err) { //check if WebMidi.js is enabled
  if (err) {
    console.log("WebMidi could not be enabled.", err);
  } else {
    console.log("WebMidi enabled!");
  }
}); 
</code></pre>

<p>If you run your sketch now, and the console displays 'WebMidi enabled!' then you're good so far!</p>

<h4>Creating the MIDI listener</h4>
<p>The next thing we have to do is creating a MIDI listener that listens to a MIDI port for signals. Go ahead and fire up LoopMIDI and let it run in the background for now.</p>

</div>
</div>
