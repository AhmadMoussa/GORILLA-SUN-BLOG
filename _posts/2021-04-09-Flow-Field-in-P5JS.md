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

Back to the topic of this blog post, in the aforementioned essay by Tyler Hobbs, he describes how he approaches the programmatic creation of Flow Fields. And yet again, we will be using a grid to start off (I seriously can't state how important grids are in creative coding). This time around, we're creating a grid of angles, that we're not actually going to display, they will however serve as a guiding frame for the 'flows' that we will display.

Let's begin with the grid:
<pre><code>var listOfColors = ["#335c67","#fff3b0","#e09f3e","#9e2a2b","#540b0e"]

class Pnt {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.c = listOfColors[int(random(listOfColors.length))]

    this.angle = 3.14 * 0.25;
    this.r = 10;
    this.v1 = createVector(this.x, this.y)
    this.v2 = createVector(this.x + this.r * sin(this.angle), this.y + this.r * cos(this.angle))
  }

  move() {}

  display() {
    stroke(color(this.c))
    strokeWeight(3)

    noFill();

    line(this.v1.x, this.v1.y, this.v2.x, this.v2.y);

  }
}

class Field {
  constructor() {
    this.squares = []
    this.gridSize = 30
    this.squareSize = 55
    this.spacing = 15

    this.curveLength = 200;
    this.numCurves = 500;
    this.positionX = width / 2 - ((this.gridSize - 1) * (this.spacing)) / 2
    this.positionY = height / 2 - ((this.gridSize - 1) * (this.spacing)) / 2

    for (let i = 0; i < this.gridSize; i++) {
      let row = []
      for (let j = 0; j < this.gridSize; j++) {
        row.push(
          new Pnt((this.positionX + this.spacing * i), (this.positionY + this.spacing * j))
        )
      }
      this.squares.push(row)
    }

  }

  move() {
    for (let i = 0; i < this.gridSize; i++) {
      for (let j = 0; j < this.gridSize; j++) {
        this.squares[i][j].move()
      }
    }
  }

  display() {
    for (let i = 0; i < this.gridSize; i++) {
      for (let j = 0; j < this.gridSize; j++) {
        this.squares[i][j].display()
      }
    }
  }
}

function setup() {
  createCanvas(600, 600);
  sg = new Field();
}

function draw() {
  background(155);
  sg.display();
  noLoop();
}
</code></pre>
Make sure to include the 'noLoop()' statement at the end of the draw function. We're just drawing a static image this time around and not animating anything (we will however in subsequent blog posts).

