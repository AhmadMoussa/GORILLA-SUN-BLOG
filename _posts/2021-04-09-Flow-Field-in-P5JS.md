---
title: Flow Fields in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: Flow Fields are a creative coding concept that are tricky to implement but yield rewarding and interesting artworks. This blog post explains their intricacies.
thumbnail_path: 2021-04-09-Flow-Fields-in-p5js.png
published: false
---

This is another one that is largely inspired by the great Tyler Hobbs and the pseudo code in one of his essays. The cool thing about his essays is that he doesn't share the code for his artworks directly, which is something I'm considering on doing from now on. This way, you, the reader, would be encouraged to discover the algorithm behind a certain artwork for yourself, which I find is the best way to learn creative coding. Even though I also find it important to read and get better at reading code.

Back to the topic of this blog post, in the aforementioned essay by Tyler Hobbs, he describes how he approaches the programmatic creation of Flow Fields. And yet again, we will be using a grid to start off (I seriously can't state how important grids are in creative coding). This time around, we're creating a grid of angles, that we're not actually going to display, they will however serve as a guiding fram for the 'flows' that we will display.
