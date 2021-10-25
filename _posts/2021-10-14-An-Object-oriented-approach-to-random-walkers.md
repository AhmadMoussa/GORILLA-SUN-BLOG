---
title: Making of&#58; Grand Canyon
author: Ahmad Moussa
categories:
  - p5js
description: An aesthetically minimalistic sketch that is based on a boolean grid and perlin noise
thumbnail_path: https://gorillasun.de/assets/images/2021-08-25-Making-of-Gateway/gateway.gif
published: false
exclude_rss: true
---

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-08-25-Making-of-Parasite/parasite.gif" alt="" /></span>

Today we'll take a stab at a rather popular algorithmic problem in creative coding: the random walk! Before we get into the nitty gritty, here's a quick index:

<h3>Discussion</h3>
1. <a href='#inspiration'>Inspiration</a>
2. <a href='#description'>A Closer Look</a>
3. <a href='#strategizing'>Strategizing</a>
4. <a href='#OOP'>An OOP solution</a>

<h3>Getting our hands dirty</h3>
5. <a href='#grid'>The Grid</a>
6. <a href='#randomwalker'>The Random Walker Class</a>
7. <a href='#path'>Remembering and Drawing a Path</a>
8. <a href='#broken'>Animating a Broken Random Walker</a>
9. <a href='#gridhandler'>The gridHandler Class</a>
10. <a href='#ironing'>Ironing out some Bugs</a>
11. <a href='#backtrack'>The Backtracking Logic</a>





<h2><a name='inspiration'></a>Inspiration</h2>
The work that inspired this blog post is predominantly <a href=''>Kjetil Golid</a>'s work:

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">Each line segment is given a behavior upon creation: Move randomly, or move in a straight line when possible. The interplay between the two behaviors create some intriguing patterns. <a href="https://t.co/ar6MvTd5F9">pic.twitter.com/ar6MvTd5F9</a></p>&mdash; Kjetil Golid (@kGolid) <a href="https://twitter.com/kGolid/status/1413272810324176896?ref_src=twsrc%5Etfw">July 8, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

The first time I saw this, I was quite impressed by the unique style, and the variety of the generated patterns. I haven't seen anything quite like it on Twitter before. Kjetil's other works are also very refreshing and polished in general.

I was immediately inspired to attempt a recreation of the sketch, and figure out the code behind it. A while later I also noticed works from Clay Heaton, ruudotorg and vrnv as well as many others that have attempted something similar.

And as is often the case with creative coding sketches, this is again one of those problems that looks like a fun challenge when observed shallowly, but hides <a href='https://en.wikipedia.org/wiki/Self-avoiding_walk'>a monstrum of a mathematical problem</a> (graph theory to be precise) underneath. We'll brush aside this apparent simplicity for now, and tackle the task at hand with all sincerity, as always. Let's first talk through what we're trying to accomplish!

<h2>What's the task, chief?</h2>
We begin with an empty grid. To be precise, this is a grid of empty positions, that will be the foundation of everything that follows. It'll serve as the structure within which our random walkers are allowed to move. The positions that constitute the grid thus can either be occupied or vacant, depending on it having been traversed or not.

The entities that will now interact, move, traverse and populate these empty positions in the grid, are called <a href='https://en.wikipedia.org/wiki/Random_walk'>'random walkers'</a>. And as the name suggests, these walkers will be navigating our grid in a random manner.

We'll drop one of these random walkers at an arbitrary or at a chosen position into the grid. This position will then be marked as 'occupied' and serve as starting point for the random walker. Depending on where we dropped it in, it'll have up to four choices for directions into which it can move. Those choices being the immediately adjacent vacant positions in the grid. Once it moves to the next randomly chosen position, from among the options it has, that new position will then be marked as occupied as well.

From the new position, we'll repeat this process, where we'll have at least three adjacent vacant positions to move to next. Repeating this process a number of times, like an ever growing snake, the random walker will slowly expand and populate more and more positions in the grid, marking each one as occupied once it is traversed. In the manner that we've designed this task, it is evident that random walkers that live in our grid, are self-avoiding. Meaning that a random walker cannot traverse a spot, that it has already previously occupied, a second time. However, similar to the old game of 'Snake', our random walker might come to a halt, either if it gets cornered or wraps in on itself. This halt always occurs when the current position has no vacant adjacent positions.

There's many ways to get out of this state, a popular solution being the backtracking algorithm. As it's name suggests, the backtracking algorithm sets in motion a number of steps to revert to a previous state where the random walker wasn't stuck and resume from there.

As opposed to Daniel Shiffman's strategy in his <a href='https://www.youtube.com/watch?v=m6-cm6GZ1iw&ab_channel=TheCodingTrain'>video on self-avoiding walks (which is, btw, an awesome video that you should watch right now)</a>, we're approaching this in a little bit of a simpler manner. Dan's way tries to fit one single path to the entire grid, such that this path fills out all possible positions in this grid. As he mentions towards the end of his video, this is computationally quite difficult to achieve in a brute force manner. Basically, you could be waiting for years in front of your screen, waiting for the random walker to fill out the entire grid, when the grid has a moderate size.

Our approach, similar to what Kjetil does in his artwork, is that when the random walker reaches a dead end, it won't backtrack and erase that path. But rather, it will find a previous position that lies within it's path and that has adjacent vacant positions still. But more to that in a bit, let's first discuss how one would go about implementing this. Consider the code presented my take on it, which is merely <i>one</i> way to go about this.







<h2>Modeling the Problem</h2>

Similarly to the sketch "Grand Canyon" in one of my previous blog posts, the grid will be made of a 2D array of boolean values. Why booleans? Essentially, we'll denote grid positions that are empty by "True" in as "Yes, true, this position is empty", and "False" positions as occupied, in as "Nope, false, no free spot here".

Next, let's assume that we have a single random walker exploring this grid. As it is expanding it's path, we simultaneously toggle these grid positions from True to False as soon as they become explored. That doesn't sound too complicated, we just have to keep track of the direction the random walker is moving in.

Now, here's where it gets interesting. Assume we add a second random walker the mix. This random walker will be exploring it's own path, and we'd like this to happen in a concurrent manner to the first random walker. Both should be able to move at the same time, for them to have a <i>fair</i> chance to explore as much of the grid as possible.

How do we model this problem? What is the grid in relationship to the random walkers? Take a second to think about how you would solve this! Or maybe even take a couple of minutes, and try to come up with your own code that satisfies everything that's been discussed so far!

<h2>Thinking in OOP</h2>
Add diagram here

Well this is where Object Oriented Programming, or for short OOP, comes to our rescue. We'll split up the different parts of our code into different objects, each of which will deal with an individual problem. Essentially, we'll be separating our concerns.

The solution that I came up with, would essentially treat the boolean grid as a shared resource, which will be visible to each random walker when they advance their position. The grid thus will be a single object accessible to all random walkers, which in turn don't need to communicate with each other, and don't have to communicate. They can simply know valid vacant positions through the current state of the grid.

Each random walker will also be an object, that holds relevant information about the random walk as well as all the functionality that involves advancing the random walk. Additionally, the benefit of having the random walker as an object, is that it will allow us to create several instances, which we'll simply spawn in random positions on the board. Hence, we'll be able to have not only 2 random walkers, but many more than that!

And lastly, we'll also have an entity that takes care of communicating and scheduling between the random walkers and the grid.


Not that it really matters for what we're going to do, but the <a href='https://www.toptal.com/javascript/es6-class-chaos-keeps-js-developer-up'>intricacies of OOP in Javascript are quite interesting</a>.


<h2>The grid</h2>
For the remainder, I'll assume that you are familiar with javascript syntax. We'll begin with a grid. But this time we'll wrap our 2D boolean array inside of a function, that has a couple of properties and other functionality:

<pre><code>function makeGrid(w, h, spacing, offset){
  this.w = w
  this.h = h
  this.spacing = spacing
  this.offset = offset
  this.grid = []

  this.initGrid = function(){
    for(let x = this.offset; x<this.w-this.offset; x+=this.spacing){
      row = []
      for(let y = this.offset; y<this.h-this.offset; y+=this.spacing){
          row.push(random([1,1,1,1]))
      }
      this.grid.push(row)
    }
  }

  this.display = function(){
    strokeWeight(2)
    for(let i =0; i<this.grid.length; i++){
      for(let j = 0; j<this.grid[0].length; j++){
        if(this.grid[i][j]){
          point(i * this.spacing + this.offset, j * this.spacing + this.offset)
        }
      }
    }
  }
}
</code></pre>

The member variables h, w, offset and spacing are just our usual bread and butter that we'll use to determine the grid dimensions. The actual boolean values are now stored in the 'grid' member variable. To display this grid we also require some code:

<pre><code>function setup() {
  w = min(windowWidth, windowHeight)
  wx = w
  wy = w
  createCanvas(wx, wy);
  g = new makeGrid(wx,wy,8,8)
  g.initGrid()
}

function draw() {
  background(255)
  g.display()
}
</code></pre>

And overall we'd have some code that creates a boolean grid and displays it on the canvas:


<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  g = new makeGrid(wx, wy, 8, 8);
  g.initGrid();
}

function draw() {
  background(255);
  stroke(0);
  g.display();
}

</script>
<p></p>

<h2>The Random Walker</h2>

For starters, let's make a super simple walker:

<pre><code>function spawnWalker(x, y, col){
this.currX = x
this.currY = y
}
</code></pre>

The very first things we need are an x and y coordinate to determine where the head of our walker currently is. Next let's figure out a way to make the walker move in a certain direction. Before we actually move in a direction, we'd first need to know which directions are legal moves, and then we need to determine which of these legal moves we'll take:

<pre><code>this.advance = function(grid){
    opts = this.getOptions(grid)
    choice = random(opts)
}
</code></pre>

The 'advance' function shall be the heartpiece of our random walker. It accepts a grid object as input, which we'll require to check in which directions we can move. Then through a secondary helper function, we'll fetch the options that are available to us:

<pre><code>this.getOptions = function(grid){
    options = []
    if(grid[this.currX-1][this.currY]){
      options.push({dx: this.currX-1, dy: this.currY})
    }

    if(grid[this.currX][this.currY-1]){
      options.push({dx: this.currX, dy: this.currY-1})
    }

    if(grid[this.currX + 1][this.currY]){
      options.push({dx: this.currX + 1, dy: this.currY})
    }

    if(grid[this.currX][this.currY+1]){
      options.push({dx: this.currX, dy: this.currY+1})
    }

    return options
}
</code></pre>

Take a sec to see what we did here. Maybe looks like a lot, but we're checking if the adjacent grid positions are still vacant or not. If they are, we add their location to the options array which will be returned and passed to the advance function. To do these checks we can leverage the current position of the walker, and check the grid that we passed along. However, we also need to do an additional check, such that we don't exceed the bounds of the grid:

<pre><code>this.getOptions = function(grid){
  options = []
  if(this.currX > 0){
    if(grid[this.currX-1][this.currY]){
      options.push({dx: this.currX-1, dy: this.currY})
    }
  }

  if(this.currY > 0){
    if(grid[this.currX][this.currY-1]){
      options.push({dx: this.currX, dy: this.currY-1})
    }
  }

  if(this.currX < grid.length -1){
    if(grid[this.currX + 1][this.currY]){
      options.push({dx: this.currX + 1, dy: this.currY})
    }
  }

  if(this.currY < grid[0].length-1){
    if(grid[this.currX][this.currY+1]){
      options.push({dx: this.currX, dy: this.currY+1})
    }
  }

  return options
}
</code></pre>

<h2>Remembering and drawing a path</h2>
An important part of the random walk logic is now complete. Now we'll have to actually show the path of the random walker through the grid. For this we'll need to be able to actually remember the positions that were previously occupied. We can do this by adding every position taken to an array:

<pre><code>
function spawnWalker(x, y){
  this.currX = x
  this.currY = y

  this.path = [] // this will help remember the path

  this.advance = function(grid){
    // add current position to the path array
    this.path.push({dx: this.currX, dy: this.currY})

    // move and update position
    opts = this.getOptions(grid)
    choice = random(opts)

    this.currX = choice.dx
    this.currY = choice.dy

  }

  this.getOptions = function(grid){
    // get option code goes here
  }
}
</code></pre>

Having this trace of the path, we can now add a function that draws this path:

<pre><code>this.display = function(spacing, offset){
  for(let n = 0; n<this.path.length-1; n++){
    line(this.path[n].dx * spacing + offset, this.path[n].dy * spacing + offset,
      this.path[n+1].dx * spacing + offset, this.path[n+1].dy * spacing + offset)
  }
}
</code></pre>

Notice that we also need to multiply with the correct spacing and add an offset to get the correct position on the grid. This is far from complete, but for now let's start putting stuff together such that we can see what it looks like.

<h2>A broken random walker</h2>

Throwing together

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function spawnWalker(x, y, spacing, offset) {
  this.currX = x;
  this.currY = y;

  this.spacing = spacing;
  this.offset = offset;

  this.path = []; // this will help remember the path

  this.advance = function (grid) {
    // add current position to the path array
    this.path.push({ dx: this.currX, dy: this.currY });

    // move and update position
    opts = this.getOptions(grid);
    choice = random(opts);

    this.currX = choice.dx;
    this.currY = choice.dy;
  };

  this.getOptions = function (grid) {

    options = [];
    if (this.currX > 0) {
      if (grid[this.currX - 1][this.currY]) {
        options.push({ dx: this.currX - 1, dy: this.currY });
      }
    }

    if (this.currY > 0) {
      if (grid[this.currX][this.currY - 1]) {
        options.push({ dx: this.currX, dy: this.currY - 1 });
      }
    }

    if (this.currX < grid.length - 1) {
      if (grid[this.currX + 1][this.currY]) {
        options.push({ dx: this.currX + 1, dy: this.currY });
      }
    }

    if (this.currY < grid[0].length - 1) {
      if (grid[this.currX][this.currY + 1]) {
        options.push({ dx: this.currX, dy: this.currY + 1 });
      }
    }

    return options;
  };
  this.display = function (g) {
    for (let n = 0; n < this.path.length - 1; n++) {
      line(
        this.path[n].dx * this.spacing + this.offset,
        this.path[n].dy * this.spacing + this.offset,
        this.path[n + 1].dx * this.spacing + this.offset,
        this.path[n + 1].dy * this.spacing + this.offset
      );
    }
  };
}

function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  spacing = 8
  offset = 8

  g = new makeGrid(wx, wy, spacing, offset);
  g.initGrid();


  r = new spawnWalker(0, 0, spacing, offset)
}

function draw() {
  background(255);
  stroke(0);
  g.display();
  r.advance(g.grid)
  r.display()
}
</script>
<p></p>

Running this, you already see something go on... which is still quite far from being. Fret not, we'll fix it in a second. For now it's working correctly with the code we wrote. The random walker moves along the grid, and stays confined to the grid. Now why is crossing paths with itself? Simply because we are not setting the grid positions to False once the random walker traverses them. This is where the gridHandler comes into play.

Essentially, we'll create a third object that will take care of communicating between the random walkers and the grid. It probably isn't a good idea to allow the random walker itself to modify the grid. For this sketch you could very well do it, but from a design pattern point of view, it's quite messy. Having an central authority that takes care of exchanges is just easier to manage and makes it easier to track if something goes wrong:

<pre><code>
function gridHandler(grid, randomWalkers){
  this.grid = grid // the grid
  this.randomWalkers = [...randomWalkers]  // an array of random walkers

  // advance entire board state
  // checks off cells that have paths in them
  this.advanceGrid = function(){

    // go through all the random walkers and advance each one individually
    for(let n = 0; n<this.randomWalkers.length; n++){
      toCheck = this.randomWalkers[n].advance(this.grid.grid)
      this.grid.grid[toCheck.dx][toCheck.dy] = 0
    }
  }

  this.display = function(){
    for(let n = 0; n<this.randomWalkers.length; n++){
      this.randomWalkers[n].display()
    }
  }
}
</code></pre>

The advanceGrid function takes care of advancing all of the random walkers that currently exist within the grid. We loop through the randomWalkers array that contains all of instances of random walkers, call their advance function (which we'll modify in a sec) and retrieve the position that they moved to. Using this coordinate information we'll set the grid position to False.

Modifying the advance function to return the current position just add a return statement to the end of the function:
<pre><code>return {dx: this.currX, dy: this.currY}
</code></pre>

And our code will behave as follows:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function spawnWalker(x, y) {
  this.currX = x;
  this.currY = y;

  this.spacing = spacing;
  this.offset = offset;

  this.path = []; // this will help remember the path

  this.advance = function (grid) {
    // add current position to the path array
    this.path.push({ dx: this.currX, dy: this.currY });

    // move and update position
    opts = this.getOptions(grid);
    choice = random(opts);

    this.currX = choice.dx;
    this.currY = choice.dy;

    return {dx: this.currX, dy: this.currY}
  };

  this.getOptions = function (grid) {

    options = [];
    if (this.currX > 0) {
      if (grid[this.currX - 1][this.currY]) {
        options.push({ dx: this.currX - 1, dy: this.currY });
      }
    }

    if (this.currY > 0) {
      if (grid[this.currX][this.currY - 1]) {
        options.push({ dx: this.currX, dy: this.currY - 1 });
      }
    }

    if (this.currX < grid.length - 1) {
      if (grid[this.currX + 1][this.currY]) {
        options.push({ dx: this.currX + 1, dy: this.currY });
      }
    }

    if (this.currY < grid[0].length - 1) {
      if (grid[this.currX][this.currY + 1]) {
        options.push({ dx: this.currX, dy: this.currY + 1 });
      }
    }

    return options;
  };
  this.display = function (g) {
    for (let n = 0; n < this.path.length - 1; n++) {
      line(
        this.path[n].dx * this.spacing + this.offset,
        this.path[n].dy * this.spacing + this.offset,
        this.path[n + 1].dx * this.spacing + this.offset,
        this.path[n + 1].dy * this.spacing + this.offset
      );
    }
  };
}

function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function gridHandler(grid, randomWalkers){
  this.grid = grid // the grid
  this.randomWalkers = [...randomWalkers]  // an array of random walkers

  // advance entire board state
  // checks off cells that have paths in them
  this.advanceGrid = function(){

    // go through all the random walkers and advance each one individually
    for(let n = 0; n<this.randomWalkers.length; n++){
      toCheck = this.randomWalkers[n].advance(this.grid.grid)
      this.grid.grid[toCheck.dx][toCheck.dy] = 0
    }
  }

  this.display = function(){
    this.grid.display()
    for(let n = 0; n<this.randomWalkers.length; n++){
      this.randomWalkers[n].display()
    }
  }
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  spacing = 8
  offset = 8

  g = new makeGrid(wx, wy, spacing, offset);
  g.initGrid();

  r = new spawnWalker(0,0, spacing, offset)

  h = new gridHandler(g, [r])
}

function draw() {
  background(255);
  stroke(0);
  h.display()
  h.advanceGrid()
}
</script>
<p></p>

Now it behaves much more the way we intended. Try rerunning the sketch a couple of times to see how it behaves and ends in a error. We'll get to that in a second. Try adding a couple more random walkers at different positions! What we have so far is already really great, now we just need to think about the backtracking logic!


But first let's fix that error from before, we'll wrap two parts of our code in if coditions:
<pre><code>opts = this.getOptions(grid);
choice = random(opts);

// add current position to the path array
this.path.push({ dx: this.currX, dy: this.currY });

if(choice){
   this.drawnPath.push({ dx: this.currX, dy: this.currY });
   this.currX = choice.dx;
   this.currY = choice.dy;

   return {dx: this.currX, dy: this.currY}
 }
</code></pre>

This will ensure that if no choices are available, no coordinates are returned from the advance function. Next in the grid handler function:

<pre><code>this.advanceGrid = function(){
  // go through all the random walkers and advance each one individually
  for(let n = 0; n<this.randomWalkers.length; n++){
    toCheck = this.randomWalkers[n].advance(this.grid.grid)

    if(toCheck){
      this.grid.grid[toCheck.dx][toCheck.dy] = 0
    }
  }
}
</code></pre>

Here we'll make sure only if something is returned we attempt modify the grid positions' state. We'll also add another line to the main body of the gridHandler object:

<pre><code>// go through and set their starting positions in the grid to false
for(let n = 0; n<this.randomWalkers.length; n++){
    this.grid.grid[randomWalkers[n].currX][randomWalkers[n].currY] = 0
}
</code></pre>

Which, as the comment explains, sets the positions in which walkers are spawned. Now the error should have disappeared:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function spawnWalker(x, y) {
  this.currX = x;
  this.currY = y;

  this.spacing = spacing;
  this.offset = offset;

  this.path = []; // this will help remember the path

  this.advance = function (grid) {
    // add current position to the path array
    this.path.push({ dx: this.currX, dy: this.currY });

    // move and update position
    opts = this.getOptions(grid);
    choice = random(opts);

    if(choice){
      this.currX = choice.dx;
      this.currY = choice.dy;

      return {dx: this.currX, dy: this.currY}
    }
  };

  this.getOptions = function (grid) {

    options = [];
    if (this.currX > 0) {
      if (grid[this.currX - 1][this.currY]) {
        options.push({ dx: this.currX - 1, dy: this.currY });
      }
    }

    if (this.currY > 0) {
      if (grid[this.currX][this.currY - 1]) {
        options.push({ dx: this.currX, dy: this.currY - 1 });
      }
    }

    if (this.currX < grid.length - 1) {
      if (grid[this.currX + 1][this.currY]) {
        options.push({ dx: this.currX + 1, dy: this.currY });
      }
    }

    if (this.currY < grid[0].length - 1) {
      if (grid[this.currX][this.currY + 1]) {
        options.push({ dx: this.currX, dy: this.currY + 1 });
      }
    }

    return options;
  };
  this.display = function (g) {
    for (let n = 0; n < this.path.length - 1; n++) {
      line(
        this.path[n].dx * this.spacing + this.offset,
        this.path[n].dy * this.spacing + this.offset,
        this.path[n + 1].dx * this.spacing + this.offset,
        this.path[n + 1].dy * this.spacing + this.offset
      );
    }
  };
}

function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function gridHandler(grid, randomWalkers){
  this.grid = grid // the grid
  this.randomWalkers = [...randomWalkers]  // an array of random walkers

  // go through and set their starting positions in the grid to false
  for(let n = 0; n<this.randomWalkers.length; n++){
      this.grid.grid[randomWalkers[n].currX][randomWalkers[n].currY] = 0
  }

  // advance entire board state
  // checks off cells that have paths in them
  this.advanceGrid = function(){

    // go through all the random walkers and advance each one individually
    for(let n = 0; n<this.randomWalkers.length; n++){
      toCheck = this.randomWalkers[n].advance(this.grid.grid)

      if(toCheck){
        this.grid.grid[toCheck.dx][toCheck.dy] = 0
      }
    }
  }

  this.display = function(){
    this.grid.display()
    for(let n = 0; n<this.randomWalkers.length; n++){
      this.randomWalkers[n].display()
    }
  }
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  spacing = 8
  offset = 8

  g = new makeGrid(wx, wy, spacing, offset);
  g.initGrid();

  r = new spawnWalker(0,0, spacing, offset)
  r2 = new spawnWalker(20,0, spacing, offset)

  h = new gridHandler(g, [r,r2])
}

function draw() {
  background(255);
  stroke(0);
  h.display()
  h.advanceGrid()
}
</script>
<p></p>

<h2>Backtracking</h2>
When do we actually need to backtrack? When we get stuck. When do we get stuck? When we have no options to advance the walker. We'll add an else statement in our advance function, that will return to a previous position in our path that still has options available to it:

<pre><code>if(choice){
  this.currX = choice.dx;
  this.currY = choice.dy;

  return {dx: this.currX, dy: this.currY}
}else{
  this.path.pop()
}
</code></pre>

If you try this you'll note that nothing really happens. And that's because we also need to move the line that adds the current position to the path array, needs to be moved inside the if statement, like so:

<pre><code>if(choice){
  // add current position to the path array
  this.path.push({ dx: this.currX, dy: this.currY });

  this.currX = choice.dx;
  this.currY = choice.dy;

  return {dx: this.currX, dy: this.currY}
}else{
  this.path.pop()
}
</code></pre>

Even though this achieves backtracking, visually it does not correspond to what we want:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function spawnWalker(x, y) {
  this.currX = x;
  this.currY = y;

  this.spacing = spacing;
  this.offset = offset;

  this.path = []; // this will help remember the path

  this.advance = function (grid) {


    // move and update position
    opts = this.getOptions(grid);
    choice = random(opts);

    if(choice){
      // add current position to the path array
      this.path.push({ dx: this.currX, dy: this.currY });

      this.currX = choice.dx;
      this.currY = choice.dy;

      return {dx: this.currX, dy: this.currY}
    }else{
      backtracked = this.path.pop()

      this.currX = backtracked.dx
      this.currY = backtracked.dy
    }
  };

  this.getOptions = function (grid) {

    options = [];
    if (this.currX > 0) {
      if (grid[this.currX - 1][this.currY]) {
        options.push({ dx: this.currX - 1, dy: this.currY });
      }
    }

    if (this.currY > 0) {
      if (grid[this.currX][this.currY - 1]) {
        options.push({ dx: this.currX, dy: this.currY - 1 });
      }
    }

    if (this.currX < grid.length - 1) {
      if (grid[this.currX + 1][this.currY]) {
        options.push({ dx: this.currX + 1, dy: this.currY });
      }
    }

    if (this.currY < grid[0].length - 1) {
      if (grid[this.currX][this.currY + 1]) {
        options.push({ dx: this.currX, dy: this.currY + 1 });
      }
    }

    return options;
  };
  this.display = function (g) {
    for (let n = 0; n < this.path.length - 1; n++) {
      line(
        this.path[n].dx * this.spacing + this.offset,
        this.path[n].dy * this.spacing + this.offset,
        this.path[n + 1].dx * this.spacing + this.offset,
        this.path[n + 1].dy * this.spacing + this.offset
      );
    }
  };
}

function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function gridHandler(grid, randomWalkers){
  this.grid = grid // the grid
  this.randomWalkers = [...randomWalkers]  // an array of random walkers

  // go through and set their starting positions in the grid to false
  for(let n = 0; n<this.randomWalkers.length; n++){
      this.grid.grid[randomWalkers[n].currX][randomWalkers[n].currY] = 0
  }

  // advance entire board state
  // checks off cells that have paths in them
  this.advanceGrid = function(){

    // go through all the random walkers and advance each one individually
    for(let n = 0; n<this.randomWalkers.length; n++){
      toCheck = this.randomWalkers[n].advance(this.grid.grid)

      if(toCheck){
        this.grid.grid[toCheck.dx][toCheck.dy] = 0
      }
    }
  }

  this.display = function(){
    this.grid.display()
    for(let n = 0; n<this.randomWalkers.length; n++){
      this.randomWalkers[n].display()
    }
  }
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  spacing = 8
  offset = 8

  g = new makeGrid(wx, wy, spacing, offset);
  g.initGrid();

  r = new spawnWalker(0,0, spacing, offset)
  r2 = new spawnWalker(20,0, spacing, offset)

  h = new gridHandler(g, [r,r2])
}

function draw() {
  background(255);
  stroke(0);
  h.display()
  h.advanceGrid()
}
</script>
<p></p>

Can't draw the path if we don't have that information stored in the path array anymore. We can remedy this with a secondary array that holds the path information but doesn't get popped:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
function spawnWalker(x, y) {
  this.currX = x;
  this.currY = y;

  this.spacing = spacing;
  this.offset = offset;

  this.path = []; // this will help remember the path
  this.drawnPath = [];

  this.advance = function (grid) {

    // move and update position
    opts = this.getOptions(grid);
    choice = random(opts);

    if(choice){
      // add current position to the path array
      this.path.push({ dx: this.currX, dy: this.currY });
      this.drawnPath.push({ dx: this.currX, dy: this.currY });

      this.currX = choice.dx;
      this.currY = choice.dy;

      return {dx: this.currX, dy: this.currY}
    }else{
      backtracked = this.path.pop()

      this.currX = backtracked.dx
      this.currY = backtracked.dy
    }
  };

  this.getOptions = function (grid) {

    options = [];
    if (this.currX > 0) {
      if (grid[this.currX - 1][this.currY]) {
        options.push({ dx: this.currX - 1, dy: this.currY });
      }
    }

    if (this.currY > 0) {
      if (grid[this.currX][this.currY - 1]) {
        options.push({ dx: this.currX, dy: this.currY - 1 });
      }
    }

    if (this.currX < grid.length - 1) {
      if (grid[this.currX + 1][this.currY]) {
        options.push({ dx: this.currX + 1, dy: this.currY });
      }
    }

    if (this.currY < grid[0].length - 1) {
      if (grid[this.currX][this.currY + 1]) {
        options.push({ dx: this.currX, dy: this.currY + 1 });
      }
    }

    return options;
  };
  this.display = function (g) {
    for (let n = 0; n < this.drawnPath.length - 1; n++) {
      line(
        this.drawnPath[n].dx * this.spacing + this.offset,
        this.drawnPath[n].dy * this.spacing + this.offset,
        this.drawnPath[n + 1].dx * this.spacing + this.offset,
        this.drawnPath[n + 1].dy * this.spacing + this.offset
      );
    }
  };
}

function makeGrid(w, h, spacing, offset) {
  this.w = w;
  this.h = h;
  this.spacing = spacing;
  this.offset = offset;
  this.grid = [];

  this.initGrid = function () {
    for (let x = this.offset; x < this.w - this.offset; x += this.spacing) {
      row = [];
      for (let y = this.offset; y < this.h - this.offset; y += this.spacing) {
        row.push(1);
      }
      this.grid.push(row);
    }
  };

  this.display = function () {
    strokeWeight(2);
    for (let i = 0; i < this.grid.length; i++) {
      for (let j = 0; j < this.grid[0].length; j++) {
        if (this.grid[i][j]) {
          point(i * this.spacing + this.offset, j * this.spacing + this.offset);
        }
      }
    }
  };
}

function gridHandler(grid, randomWalkers){
  this.grid = grid // the grid
  this.randomWalkers = [...randomWalkers]  // an array of random walkers

  // go through and set their starting positions in the grid to false
  for(let n = 0; n<this.randomWalkers.length; n++){
      this.grid.grid[randomWalkers[n].currX][randomWalkers[n].currY] = 0
  }

  // advance entire board state
  // checks off cells that have paths in them
  this.advanceGrid = function(){

    // go through all the random walkers and advance each one individually
    for(let n = 0; n<this.randomWalkers.length; n++){
      toCheck = this.randomWalkers[n].advance(this.grid.grid)

      if(toCheck){
        this.grid.grid[toCheck.dx][toCheck.dy] = 0
      }
    }
  }

  this.display = function(){
    this.grid.display()
    for(let n = 0; n<this.randomWalkers.length; n++){
      this.randomWalkers[n].display()
    }
  }
}

function setup() {
  w = min(windowWidth, windowHeight);
  wx = w;
  wy = w;
  createCanvas(wx, wy);

  spacing = 8
  offset = 8

  g = new makeGrid(wx, wy, spacing, offset);
  g.initGrid();

  r = new spawnWalker(0,0, spacing, offset)
  r2 = new spawnWalker(20,0, spacing, offset)

  h = new gridHandler(g, [r,r2])
}

function draw() {
  background(255);
  stroke(0);
  h.display()
  h.advanceGrid()
}
</script>
<p></p>

And those ugly lines can then be fixed by a simple condition in the display function:

<pre><code>if(dist(this.drawnPath[n].dx * this.spacing + this.offset,
      this.drawnPath[n].dy * this.spacing + this.offset,
      this.drawnPath[n + 1].dx * this.spacing + this.offset,
      this.drawnPath[n + 1].dy * this.spacing + this.offset)<this.spacing+0.1){
    line(
      this.drawnPath[n].dx * this.spacing + this.offset,
      this.drawnPath[n].dy * this.spacing + this.offset,
      this.drawnPath[n + 1].dx * this.spacing + this.offset,
      this.drawnPath[n + 1].dy * this.spacing + this.offset
    );
}
</code></pre>
