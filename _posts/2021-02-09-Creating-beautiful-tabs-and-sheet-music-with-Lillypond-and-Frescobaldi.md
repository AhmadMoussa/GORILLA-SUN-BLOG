---
title: Creating beautiful tabs and sheet music with Lillypond and Frescobaldi
author: Ahmad Moussa
description: We have a closer look at a free scripting language that allows us to create tablature and sheet music.
thumbnail_path: 2021-02-09-Creating-beautiful-tabs-and-sheet-music-with-Lillypond-and-Frescobaldi.png
---

<p> One of the things that I've found to be very difficult over the years, is finding a good system for archiving and documenting your own musical ideas. Often I would simply make a quick phone recording (voice note) at the end of a practice session, sometimes even a short video, shot from an ungodly angle, with horrible audio quality. It's probably evident that listening back after a couple of weeks to these recordings does not really help me in remembering what I was actually playing, and the thought process at the moment of recording has also disappeared. Sometimes if I'm really desperate I manage to salvage what I can by trying to figure out the tune by ear, but If you're someone who enjoys obscure and abstruse alternate tunings then you've just made it even harder on yourself.</p>

<p> Luckily, humanity has already found a convenient way to represent musical ideas in a symbolic way: sheet music and tablature! In addition there's a number of softwares that allow you to create them. If you're a guitarist you've probably used, or at least heard, of Guitar Pro. I remember getting a cracked copy of guitar pro 5 from one of my friends at school on a USB drive many years ago, and I was absolutely blown away. I didn't have an internet connection at home at the time, so I would ask my friend to download the tab files for me and pass them on, again via USB, over the course of a couple of days. I also remember it giving me access to songs that were way out of my playing ability at the time. </p>

<p>Until after some time, when my desktop broke and I got a laptop. During my college days I had a notebook, in which I would religiously jot down all the jazz licks that I could come up with. But I also forfeited this habit with time, I didn't have a clear systematic way of notating my ideas and it was very obvious how things were meant to be played when I came back to my notes. Which recently led me to explore again what other tools exist for notating music. One of first that'll pop up would be Muse Score, which I tried but decided that it wasn't for me. I just couldn't get the hang of it. In guitar pro you can simply punch in the fret number on a specific fret and that's it. Muse Score being designed not specifically for guitarists is a lot more versatile, which was way too much for my purposes. I re-downloaded Guitar Pro 7, and messed around a little with the different sounds that already come with it for around a month until my free trial expired. After which I didn't really feel like breaking the bank to get a license. Finally I found something called Lillypond which peaked my interest.</p>

<h3> Lillypond </h3>

<p> Lillypond is not a software per se, it is kind of a markup language. If you've never had to use one, you're better for it. Markup Languages can be thought of as a set of instructions that allow you to format and position elements in a document exactly the way you want. Personally, I use markup languages quite often as they're very convenient and allow you to create digital documents that look very slick. For example, I've come to prefer using Latex over MS Word. Documents made in Latex just look much better, and it makes positioning figures easier, especially when writing academic papers. Coming back from this tangent, Lillypond's primary goal is to create beautiful sheet music via a set of commands that specify what it should look like. We'll get into it in more depth in a minute. Lillypond can be used by itself, but to make easier to edit your script files you'll also want to download another software called Frescobaldi, which comes with a number of features. </p>

<h3>Downloading and Installing Lilypond and Frescobaldi</h3>

<ul>
  <li>Download Lilypond <a href='http://lilypond.org/'>Here</a></li>
  <li>Download Frescobaldi <a href='https://frescobaldi.org/download'>Here</a></li>
</ul>

<h3>Setting up Lilypond and Frescobaldi</h3>
<p>After installing both softwares, go ahead and open up Frescobaldi. We have to tell Frescobaldi where to find Lilypond on our device.</p>

<span class="image left"><img src="https://gorillasun.de/post_images/2021-02-09-Creating-beautiful-tabs-and-sheet-music-with-Lillypond-and-Frescobaldi/Settigs1.png" alt="" /></span>
