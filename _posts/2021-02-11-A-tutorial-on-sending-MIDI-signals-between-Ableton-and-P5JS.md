---
title: Sending MIDI signals between Ableton and P5JS
author: Ahmad Moussa
categories:
  - ableton
description: A tutorial on sending MIDI signals between Ableton and P5JS.
thumbnail_path: https://gorillasun.de/assets/images/thumbnails/MIDI2.webm
exclude_rss: false
listed: true
legacy: false
---

<div style="width:100%;height:0;padding-bottom:69%;position:relative;"><iframe src="https://giphy.com/embed/FP2tlzlCyplK0NhGaE" width="100%" height="100%" style="position:absolute; pointer-events:none;" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></div>
<p></p>
<p>In this post we have a look a WebMIDI.js, a javascript library that'll allow to handle MIDI signals in p5js. This is especially useful if you want to create a visual interactive midi controller, or control you sketches with MIDI signals sent from your daw. In this post I'll show you how to do the latter.</p>

<h2>Importing WebMIDI.js</h2>
<p>In an earlier blog post, I explain how one can send MIDI signals from a python script to ableton live, which requires us to create a virtual port.  This time around we'll also need the LoopMIDI virtual port that we have used for the python tutorial, so go ahead and download it from <a href='http://www.tobias-erichsen.de/software/loopmidi.html' target="_blank" rel="noopener noreferrer">here</a>.</p>

<p>Next we'll hop over to the web editor where we have to configure a couple of things. In the web editor, directly underneath the the large 'play' button, there is a smaller arrow button. Clicking on it reveals to use that there are other files, in addition to 'sketch.js' which we are currently editing. Click on 'index.html'. In the '<head>' tag you can a number of lines that are importing the P5JS library for us to use. Here we can also import other javascript libraries, such as WebMIDI.js. Go ahead and add the following line in the head tag of index.html: </p>
  
<pre><code>&lt;script src="https://cdn.jsdelivr.net/npm/webmidi"&gt; &lt;/script&gt;</code></pre>

<h2>Enabling WebMIDI</h2>
<p>The next thing we need to do is to check that WebMIDI works and that we can see the accesible MIDI ports. Inside the setup function we need to insert the following snippet of code:</p>

<pre><code>WebMidi.enable(function(err) { //check if WebMidi.js is enabled
  if (err) {
    console.log("WebMidi could not be enabled.", err);
  } else {
    console.log("WebMidi enabled!");
  }
}); 
</code></pre>

<p>If you run your sketch now, and the console displays 'WebMidi enabled!' then you're good so far!</p>

<h2>Creating the MIDI listener</h2>
<p>The next thing we have to do is creating a MIDI listener that listens to a MIDI port for signals and reacts on specific MIDI triggers.</p>
<h3>Setup</h3>
<p>Go ahead and fire up LoopMIDI and let it run in the background for now. Still within the setup function and inside the <code>WebMidi.enable(function(err){})</code> clause we add this snippet of code:</p>

<pre><code>//name our visible MIDI input and output ports
console.log("---");
console.log("Inputs Ports: ");
for (i = 0; i < WebMidi.inputs.length; i++) {
  console.log(i + ": " + WebMidi.inputs[i].name);
}

console.log("---");
console.log("Output Ports: ");
for (i = 0; i < WebMidi.outputs.length; i++) {
  console.log(i + ": " + WebMidi.outputs[i].name);
}
</code></pre>

<p>Running this code you should get this output in the console:</p>
<pre>
WebMidi enabled! 
--- 
Inputs Ports:  
0: loopMIDI Port 
--- 
Output Ports:  
0: loopMIDI Port 
</pre>

<h3>Selecting the correct port</h3>
<pre><code>//Choose an input port
inputSoftware = WebMidi.inputs[0];
//The 0 value is the first value in the array
//meaning that we are going to use the first MIDI input we see
//which in this case is 'LoopMIDI port'
</code></pre>

<h3>The listener</h3>
<p>And lastly we need to create the actual listener:</p>
<pre><code>//listen to all incoming "note on" input events
inputSoftware.addListener('noteon', "all",
  function(e) {
    //Show what we are receiving
    console.log("Received 'noteon' message (" + e.note.name + e.note.octave + ") " + e.note.number + ".");

    //the function you want to trigger on a 'note on' event goes here
  }
);

//The note off functionality will need its own event listener
//You don't need to pair every single note on with a note off
inputSoftware.addListener('noteoff', "all",
  function(e) {
    //Show what we are receiving
    console.log("Received 'noteoff' message (" + e.note.name + e.note.octave + ") " + e.note.number + ".");

    //the function you want to trigger on a 'note on' event goes here
  }
);

//
//end of MIDI setup
//
</code></pre>
<p>And we're good! Hit the run button and hop over to Ableton.</p>

<h2>Configuring Ableton</h2>
<p>In ableton we now need to create a MIDI track, arm it and set the output of it to the virtual port, which is 'LoopMidi port'. Lastly also set the input of the track to your MIDI controller, or to your computer keyboard. Now if you hit a button or key, the console in P5JS should print the corresponding note name. Now you can do all sorts of crazy stuff, making your animations react to MIDI inputs from Ableton, or from other softwares. Next blog post will have a hand on tutorial for making a sketch that is in sync with your MIDI signals. Thanks for reading!</p>
