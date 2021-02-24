---
title: Brian Eno's Endless Music Machines
author: Ahmad Moussa
categories:
  - music
description: The idea of procedurally generated music is interesting, in this article we have a look at how Brian Eno sparked this idea into life and how he approached it, as well as how you can start making something similar.
thumbnail_path: 2021-02-13-Brian-Eno's-Endless-Music-Machines.png
---

<blockquote>“One of my long-term interests has been the invention of ‘machines’ and ‘systems’ that could produce musical and visual experiences… [T]he point of them was to make music with materials and processes I specified, but in combinations and interactions that I did not.” — From “Generative Music” in A Year With Swollen Appendices</blockquote>

<iframe src="https://open.spotify.com/embed/album/063f8Ej8rLVTz9KkjQKEMa" width="100%" height="200" frameborder="0" allowtransparency="true" allow="encrypted-media"></iframe>

<p></p>
<h2>An endless stream of ever changing music</h2>
<h3>My journey into Ambient Music</h3>
My love for ambient music started when I first listened to artists like Tycho and Jake Bowen. When listening to some of their pieces, I often feel like I'm being taken away into a distant world, entirely drawn through the narrative that the music is telling. In this captivated state I can't help but feel a deep sense of serenity and melancholy. Among others, they were maybe the first two artists that inspired me to get into electronic music myself. And I couldn't help but be mind-blown when I saw all the possibilities that modern softwares offer.</p>

<p>For instance, I can have an entire orchestra at my disposition at any time of the day. Which makes me think how tedious it must have been for composers before our time, we now have the incredible luxury of not just being able to hear our compositions at any given instant, but we can also manipulate sound as if it were a physical entity. Lest I go on a tangent, with these modern tools many have attempted the creation of systems that are capable of generating streams of music, all by themselves, and without any further human input. The most noteworthy individual among them might be Brian Eno.

<h2>The Origins of Generative Music</h2>
<h3>Who's Brian Eno?</h3>
Generative Music finds it's beginnings with Brian Eno, an English musician and record producer, who first got interested in Generative Music by observing wind chimes, and how they could endlessly create different sounds, without ever repeating themselves. By design, they are incapable of creating the same sound twice as they react to some current of wind that brings them into motion. This sparked in Brian Eno an idea of creating a system that was capable of continuously generating a stream of music that was ever changing. His first experiments involved playing around with tape recorders, that would play melodic parts he pre-recorded, which would musically work together when played at the same time. Playing these tape recorder at modulating speeds and different offsets made it such that an entirely new musical piece emerged that never repeated itself.

<h3>What is Generative Music Exactly?</h3>
Generative music, also known as procedurally generated music, is created by a system according to some pre-defined algorithmic rules, that are capable of arranging musical ideas in a coherent manner. This arrangement happens in such a manner that the emerging musical patterns may or may not repeat over the course of some time.</p>

<p>'Algorithmic rules' can generally mean a broad number of things, ranging from simple rules such as simple ratios and probabilities but also more complex types of sequencing and automatic composition. In this manner, the generated music is never truly 'random', it does follow specific patterns, which should not be immediately evident to the listener.

<h3>Eno's work</h3>
<p></p>

<h2>The thought process behind Eno's Work</h2>
<h3>Evolving metaphors, in my opinion, is what artists do</h3>
<p>In his 1996 talk "Evolving metaphors, in my opinion, is what artists do." he explains that one of his inspirations were pieces of music such as 'It's gonna rain' by steve reich. It's a fairly long piece, built on a fairly simple idea. It essentially loops the same sample over and over again played back on two machines at the same time, due to the minute differences in speed between the two machines it makes it such that the sample slips in and out of sync with itself, creating very interesting textures. </p>
<p>Whilst reading the transcript of the speech, I quickly opened Youtube and listened a little bit to the piece, and skimmed back and forth a bit. Back to the transcript, Brian gives a visual analogy. Usually when you stare at something for a long time, you start noticing things that you wouldn't have immediately noticed from a first glance. Your ear works in the same manner, if you listen to the same thing over and over again, you become infinitely more aware of the small details that are present in the repeated sample. This is what happens in Steve Reich's track, even though the sample might seem a little silly at first, it is incredibly rich with information. Brian mentions that there are sounds of pigeons on the track, which I didn't hear in my first listening. Which made me go back for a second listening, and indeed after a while you start hearing some bird like sounds, even though them being very feeble</p>

<h3>Moiree Patterns</h3>
<p>He gives another visual analogy, that of Moiree Patterns. Moiree patterns are interference patterns, patterns that emerge from overlapping two grid-like patterns:</p>
<blockquote>Now a moire pattern is when you overlay two identical grids with one another. Here's one, here's the other. Now when I overlay them, see what happens, you get a very complicated interaction. You get something that actually you wouldn't have predicted from these two original identical sheets of paper. This is actually a very good analog of the Steve Reich piece in action. Something happens because of one's perception rather than because of anything physically happening to these two sheets of plastic which produce an effect that you simply couldn't have expected or predicted.
</blockquote>

<p>And here's an example of a Moiree Pattern:</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-02-13-Brian-Eno's-Endless-Music-Machines/moiree.png" alt="" /></span>

<h2>The mechanics of Eno's work</h2>
<h3>Musical Moiree Patterns</h3>
Being inspired and impressed by Steve Reich's track and the concept of Moiree Patterns, that is, complex patterns emerging from overlapping simple patterns, he attempted to approach composition in his manner. In a similar manner to the picture that you can see above, Eno tried to overlap simple patterns and obtain a more intricate whole. Naturally, music is something that progresses as time goes on, and one can only experience a single moment of the piece at any given time, as opposed to an image, which you can observe in it's entirety at any given time. To simulate Moiree Patterns in a temporal manner, Eno recorded several different notes, each on a separate tape recorder, and configured them such that they looped at different intervals, thus creating a stream of music that seems to have an infinite number of variety as the played notes interact differently with each other over time.

<blockquote> Music for Airports, at least one of the pieces on there, is structurally very, very simple. There are sung notes, sung by three women and my self. One of the notes repeats every 23 1/2 seconds. It is in fact a long loop running around a series of tubular aluminum chairs in Conny Plank's studio. The next lowest loop repeats every 25 7/8 seconds or something like that. The third one every 29 15/16 seconds or something. What I mean is they all repeat in cycles that are called incommensurable -- they are not likely to come back into sync again. </blockquote>

<p>In a way, this is very similar to polyrhythms, but on a much larger scale, which is ingenious to me.
  
<h2>Creating my own generative music system</h2>
<h3>Using sequencers and python scripts</h3>
<p>For a while now, I've been becoming more obsessed with creating a system that can generate or at least simulate a stream of music with endless variety. However I've found it quiet difficult to find the right tools for it. For example, even though Ableton Live provides a sequencer, it doesn't allow for much control on the sequencing, such as assigning a probability to each step in the sequence, making it such that the loop doesn't repeat the exact same way every iteration. After discovering that I can send MIDI signals from a python script to Ableton I attempted writing scripts that would send random MIDI notes quantized to a scale and play them. Random MIDI notes, even if they're quantized don't sound very good, and designing intricate melodic sequences that are proceurally generated or at least procedurally conditional is very difficult. There are  many more ways in which I attempted to tackle this problem, all while not having to spend any money on gear or software, which I will probably talk about in one of my future posts.</p>

<p>Eno goes on to talk about other inspirations that he encountered in the coming years of his career, such as Conway's Game of Life (which I will definitely discuss in a future post) amongst others. The entire transcript of the talk can be found <a href='https://inmotionmagazine.com/eno1.html'>here</a>, it's a very interesting and very entertaining talk and I recommend having a look at it.</p>
