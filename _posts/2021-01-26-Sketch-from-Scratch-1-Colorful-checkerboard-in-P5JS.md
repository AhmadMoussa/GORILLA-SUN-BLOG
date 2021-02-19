---
title: Sketch from Scratch 1 Colorful checkerboard in P5JS
author: Ahmad Moussa
categories:
  - P5JS
description: Delve with me into the wonderful world of P5JS. Procedural and generative art are becoming a lot more popular these days, so why not learn how to make some?
thumbnail_path: 2021-01-26-Sketch-from-Scratch-1-Colorful-checkerboard-in-P5JS.png
---
<span class="image right"><img src="https://gorillasun.de/post_images/2021-01-26-Sketch-from-Scratch-1-Colorful-checkerboard-in-P5JS/out.gif" alt="" /></span>
<p>Twitter and Instagram are primarily visual mediums and are not extremely well suited for sharing audio content. Digital artists, designers and models can get a lot of traction from posting beautiful pictures or videos, especially since it would be something that other people would want to re-tweet or share in their stories. As a musician or producer, screen recordings of your DAW playing back your track is usually not extremely aesthetically pleasing. Personally, it's been extremely difficult to find something visually interesting and also thematically appropriate to use as cover art for your social media post.</p>
<p>Another restriction that I restrained myself with was that everything that I post had to be completely original, which naturally includes the cover art. This led me to experiment with different programming languages and tools that could technically be used for creating these visuals. I've seen a lot of posts that use touchdesigner to create visuals for their music and it's quite fantastic in that regard. Touchdesigner is a python powered node based visual programming language that's seeing a lot of use by DJs and visual performers, however I found it too difficult to use for my purposes, as I wanted something quick and simple. I also experimented with OpenGL and GLSL, all the time while reminiscing about the computer graphics class that I've taken a few years ago. But shading languages are also way overkill for simple visuals. I also attempted to use OpenFramenworks, a creative coding library in c++ and built on top of OpenGL, but I found it equivalently too difficult.</p>
<p>However recently I discovered P5JS, another creative coding library built with Javascript. What I found most exciting about it was that it had online editor where you could code up sketches very quickly and run them directly in your browser. No hassle with any downloads or installation, no dealing with a compiler on system, and tons of tutorials and sketches from other people that you can simply view and get inspired by.</p>

<h3>How does P5JS work?</h3>

<p>Comprehensive and exhaustive tutorials on P5JS can be found on The Coding Train's Youtube channel, and the complete P5JS can be found on the official website. Quick sketches can be spun up in the web editor.</p>
<p>An empty sketch starts with two functions. Setup and Draw. The setup functions contains the canvas() statement that creates the (surprise!) canvas and should usually contain everything that you want to do before drawing to the canvas. When you hit the run button in the top left corner of the web editor, the setup function is executed, and then immediately after, the draw function is called. The draw function will then continuously execute it's contents, and depending on what that is, will make something appear on screen.</p>	

<blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/CKnnXsFAtNQ/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="13" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/CKnnXsFAtNQ/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto 12px; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.899 532.1,66.113 541,66.113 C549.9,66.113 557.115,58.899 557.115,50 C557.115,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.022 564.756,66.606 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.607,73.757 556.021,74.244 553.102,74.378 C549.944,74.521 548.997,74.552 541,74.552 C533.003,74.552 532.056,74.521 528.898,74.378 C525.979,74.244 524.393,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.606 516.755,65.022 516.623,62.101 C516.479,58.943 516.448,57.996 516.448,50 C516.448,42.003 516.479,41.056 516.623,37.899 C516.755,34.978 517.244,33.391 517.654,32.338 C518.197,30.938 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.393,26.244 525.979,25.756 528.898,25.623 C532.057,25.479 533.004,25.448 541,25.448 C548.997,25.448 549.943,25.479 553.102,25.623 C556.021,25.756 557.607,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.938 564.346,32.338 C564.756,33.391 565.244,34.978 565.378,37.899 C565.522,41.056 565.552,42.003 565.552,50 C565.552,57.996 565.522,58.943 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C550.169,20.033 549.148,20 541,20 C532.853,20 531.831,20.033 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.035,40.831 511,41.851 511,50 C511,58.147 511.035,59.17 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C531.831,79.965 532.853,80.001 541,80.001 C549.148,80.001 550.169,79.965 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.966,59.17 571,58.147 571,50 C571,41.851 570.966,40.831 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;"> View this post on Instagram</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 20px; width: 20px;"></div> <div style=" width: 0; height: 0; border-top: 2px solid transparent; border-left: 6px solid #f4f4f4; border-bottom: 2px solid transparent; transform: translateX(16px) translateY(-4px) rotate(30deg)"></div></div><div style="margin-left: auto;"> <div style=" width: 0px; border-top: 8px solid #F4F4F4; border-right: 8px solid transparent; transform: translateY(16px);"></div> <div style=" background-color: #F4F4F4; flex-grow: 0; height: 12px; width: 16px; transform: translateY(-4px);"></div> <div style=" width: 0; height: 0; border-top: 8px solid #F4F4F4; border-left: 8px solid transparent; transform: translateY(-4px) translateX(8px);"></div></div></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center; margin-bottom: 24px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; margin-bottom: 6px; width: 224px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; flex-grow: 0; height: 14px; width: 144px;"></div></div></a><p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/CKnnXsFAtNQ/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">A post shared by Ahmad Moussa (@gorilla.sun)</a></p></div></blockquote> <script async defer src="https://platform.instagram.com/en_US/embeds.js"></script>
<p>In the embedded instagram post above you can see the final product. The warbly music that's playing alongside the animation will be the topic for another blog post.</p>

<h3>Creating the checkerboard</h3>

<!-- Preformatted Code -->
<h3>The Colors</h3>
<p>
The first thing we'll want to do is grab a bunch of harmonious colors from <a href='https://coolors.co/generate'>Coolors</a>, which conveniently lets us export the hex codes. We can later on randomly draw colors from this array to color the squares in our grid:
</p>
<pre><code>var listOfColors = ["#1c77c3", "#39a9db", "#40bcd8", "#f39237", "#d63230", "#540d6e", "#ee4266", "#ffd23f", "#f3fcf0", "#1f271b"];
</code></pre>
<p>This array is in the global scope and therefore accesible everywhere throughout the rest of the code.</p>
<h3>The Square</h3>
<p>
Next we create a template square class which we can reuse to create each individual square in our grid. For now the square will contain it's position, it's size and it's color:
</p>
<pre><code>
class Square{
  constructor(px,py,s){
    this.positionX = px
    this.positionY = py
    this.size = s
    this.c = listOfColors[int(random(0, listOfColors.length))]
  }

  display(){
    fill(this.c)
    rect(this.positionX-this.size/2, this.positionY-this.size/2, this.size, this.size)
  }
}
</code></pre>

<p>The move function will be empty for now, as the individual squares do not have any moving behaviour. Experiment with it though, maybe you can come up with something interesting!</p>

<h3>The Grid</h3>
<p>
Next we create an object that creates, arranges and shows the individual squares in our grid. The tricky part about it is calculating the space between all the squares as well as their respective widths, such that the grid is always centered:
</p>
<pre><code class='javascript'>
class SquareGrid{
  constructor(){
    this.squares = []
    this.gridSize = 8
    this.squareSize = 13
    this.spacing = 16
    this.positionX = width/2 - ((this.gridSize-1) * (this.spacing))/2
    console.log(this.positionX)
    this.positionY = height/2 - ((this.gridSize-1) * (this.spacing))/2

    for(let i=0; i&ltthis.gridSize; i++){
      let row = []
      for(let j=0; j&ltthis.gridSize; j++){
        row.push(
          new Square((this.positionX + this.spacing * i),(this.positionY + this.spacing * j ),this.squareSize)
        )
      }
      this.squares.push(row)
    }
  }

  display(){
    for(let i=0; i&ltthis.gridSize; i++){
      for(let j=0; j&ltthis.gridSize; j++){
        this.squares[i][j].display()
      }
    }
    this.squares[int(random(this.gridSize))][int(random(this.gridSize))].c = listOfColors[int(random(0, listOfColors.length))]
  }
}
</code></pre>

<p>The final statement at the bottom of the display function is there to make the grid appear animated. Simply after drawing the entire grid, one cell will be selected at random and it's color will be updated by a random color from the color array.</p>
<h3>Setup and Draw and Voila</h3>
<p>
The last thing we still need to do is instantiate the grid and we'll obtain the grid that you've already seen above.
</p>
<pre><code>function setup() {
  createCanvas(256, 256);
  grid = new SquareGrid()
}

function draw() {
  background(220);
  grid.display()
}</code></pre>
<p> The entire code can run in the web editor <a href='https://editor.p5js.org/AhmadMoussa/sketches/j_CIeN6aU'>here</a>. If there's something that's not very clear leave me a comment below. And if you enjoyed this post consider following me on Instagram or Twitter, or alternatively shoot me an email. Cheers and thanks for reading.</p>
