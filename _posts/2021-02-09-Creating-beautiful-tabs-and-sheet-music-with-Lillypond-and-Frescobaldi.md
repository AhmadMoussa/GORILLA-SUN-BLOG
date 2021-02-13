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


<div class="row gtr-200">
			<div class="col-6 col-12-medium"><span class="image fit"><img src="https://gorillasun.de/post_images/2021-02-09-Creating-beautiful-tabs-and-sheet-music-with-Lillypond-and-Frescobaldi/Settings1.png" alt="" /></span></div>
			<div class="col-6 col-12-medium">
        <p>After installing both softwares, go ahead and open up Frescobaldi. We have to tell Frescobaldi where to find Lilypond on our device. To do that click edit then preferences.</p>
        
<ol>
  <li>Click on Lilypond Preferences</li>
  <li>Click the add button</li>
  <li>Look for the 'Lilypond-windows.exe' where you installed Lilypond. On windows this should be somewhere on your C drive.</li>
  <li>Make sure to check 'Run Lilypond with english English messages'</li>
</ol>
    </div>
</div>

<h3>Engraving a simple sheet</h3>

<div class="row gtr-200">
			<div class="col-6 col-12-medium"><span class="image fit"><img src="https://gorillasun.de/post_images/2021-02-09-Creating-beautiful-tabs-and-sheet-music-with-Lillypond-and-Frescobaldi/Settings2.png" alt="" /></span></div>
			<div class="col-6 col-12-medium">
        <p>We start engraving our music now. We can engrave notes by simply opening the brackets and typing in the appropriate letters.</p>
<pre><code>
\version "2.18.2"

{c d e f}
</code></pre>
    </div>
</div>




<h3>Engraving music</h3>
<p>As a guitarist that uses alternate tunings on a regular basis, Lilypond does not disappoint. I'll share the template that I use regularly and have modified over time.</p>

<pre><code>
\version "2.18.2"

\score{
  \relative {
    \tabFullNotation {
      \set Staff.stringTunings = \stringTuning <bes, f, bes, ees, f, d>
      \time 4/4
        \repeat volta 2{
          d16\4 (ees\4) g,\3 (bes\3) a\2 d\1 a\2 bes\3 \tuplet 3/2 {ees8\4 (f\4) g,\3} bes\2 \glissando d\2 \bar "|"
          
          f16\4 bes,\3 c\2 a'32\1 (bes\1 a16\1) c,\2 bes\3 f'\4 bes,8\6 ees\4 g,\3 f\2 \bar "|"
          g'4\1 (f4\1) f\1 f\1 \bar "|"
          
          \break
          
          ees16\6 d\4 bes\3 f\2 c'\1 f,\5 f'\4 g,\3 bes4\1 bes4\1 \bar "|"
          
          }	
          \break
          
          \grace bes16\6 f'\4 g,\3 bes\3\staccato bes\6 
          bes8\3\staccato \grace bes16\6 bes\3\staccato  c16\2 f\4 
          g,\3 <bes\3 f\2> \staccato bes\6 f8\2 \grace g'16\6 g\4 bes,\3 \bar "|"
          
          \break
          
          c\2 d\2 c\3 bes\3 bes8\3 \grace bes\6 bes\3 bes4\3 bes4\1 
          
    }
  }
  \header {
    piece = "Tuning:  A# F A# D# F D // Capo: 3rd fret"
  }
}


\header {
  title = "Cotton Candy Dream"
  composer = "Ahmad Y. Moussa"
}
</code></pre>
<span class="image fit"><img src="https://gorillasun.de/post_images/2021-02-09-Creating-beautiful-tabs-and-sheet-music-with-Lillypond-and-Frescobaldi/Sheet1.png" alt="" /></span>


And we obtain sheet music that looks like this:


<p>The number of ways you can format sheet music with Lilipond is insane and can be overwhelming at first. However there is an extensive documentation that is just a google search away in case you can't figure out how to do something specific.</p>
