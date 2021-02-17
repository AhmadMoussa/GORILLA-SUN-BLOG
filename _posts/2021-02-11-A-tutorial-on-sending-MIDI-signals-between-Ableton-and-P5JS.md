---
title: Sending MIDI signals between Ableton and P5JS
author: Ahmad Moussa
categories:
  - ableton
description: A tutorial on sending MIDI signals between Ableton and P5JS.
thumbnail_path: 2021-02-11-A-tutorial-on-sending-MIDI-signals-between-Ableton-and-P5JS.png
---
In this post we have a look a WebMIDI.js, a javascript library that'll allow to handle MIDI signals in p5js. This is especially useful if you want to create a visual interactive midi controller, or control you sketches with MIDI signals sent from your daw. In this post I'll show you how to do the latter.

In an earlier blog post, I explain how one can send MIDI signals from a python script to ableton live, which requires us to create a virtual port.  This time around we'll also need the LoopMIDI virtual port that we have used for the python tutorial, so go ahead and download it from <a href='http://www.tobias-erichsen.de/software/loopmidi.html' target="_blank" rel="noopener noreferrer">here</a>.

Next we'll hop over to the web editor where we have to configure a couple of things. 
