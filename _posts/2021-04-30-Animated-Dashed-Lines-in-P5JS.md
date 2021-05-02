---
title: Dashed Lines in P5JS
author: Ahmad Moussa
categories:
  - p5js
description: Achieving dashed lines in P5JS was more difficult than I thought it would be, here's a tutorial on how to do it.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---

Okay, so this is a long one. Drawing dashed lines in P5 from scratch and animating them was a tough one, but I'm super happy with my results and stoked to share the code in this post, especially with the insights I've gained from writing this code. I highly recommend attempting to make what you see above from scratch on your own, and then come back when you hit a roadblock, otherwise enjoy the post! Let's go:

1. <a href='#1'>Dashed Lines with the drawingcontext</a>
2. <a href='#2'>Dashed Line from Scratch</a>
3. <a href='#3'>Fixing the final protruding dash</a>
4. <a href='#4'>Pumping forward Animation</a>
5. <a href='#5'>Reverse Speed</a>

<h2 name='1'>Dashed Lines with drawingcontext</h2>
Drawing a line in P5JS is very easy and can be done with a single line of code, into which you pass the coordinates of the starting point and the end point. 

<pre><code>line(0,0,100,100)
</code></pre>

Drawing a dashed line in P5JS is also relatively easy, however requires you to invoke the 'drawingContext' as pointed out in this Github <a href='https://github.com/processing/p5.js/issues/3336'>issue</a>:
<pre><code>drawingContext.setLineDash([5, 15]);
</code></pre>

Drawing custom dashed lines, from scratch that have an animated behaviour, requires a lot of code. What follows is my attempt at making animated dashed lines in P5JS, but is no way the optimal approach to doing so. Again we will heavily rely on Object Oriented programming by modeling the dahsed line as an Object and giving it behaviour through functions and parameters.

<h2>Drawing a line between two points</h2>
First order of business will be drawing a line manually between two sets of coordinates. This involves finding the angle that the line forms with the x axis, which can be done with the following formula:
<pre><code>
// slope of line going from point (x1,y1) to point (x2,y2)
slope = atan2(y2 - y1, x2 - x1)
</code></pre>

We will also require the length of the segment between the two points, the formula for that is:
<pre><code>
// length of line going from point (x1, y1) to point (x2, y2)
slope = this.L = sqrt(pow((x1 - x2), 2) + pow((y1 - y2), 2));
</code></pre>

The result given by atan2 defines the angle of inclination of our line, which we will have to rotate by that angle. Essentially, we will center a circle on the point (x1, y1) with a radius equal to the length of the line (which we just calculated) and use the angle with sin() and cos() to slant the line. If you haven't already done so, go and read my post on different types of rotation in P5JS here. 

So far we would have something that looks like this:
<pre><code>
class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));

    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
  }

  display() {
    line(this.x1, this.y1,
      this.x1 + this.L * cos(this.S),
      this.y1 + this.L * sin(this.S))
  }
}

function setup() {
  createCanvas(400, 400);
  sl = new SlopeLine(200, 200, 300, 200);
}

function draw() {
  background(220);
  sl.display();
}
</code></pre>

Not very impressive yet, because it's essentially just doing the same thing that you could do with the line() function. But I thought that it was cool to see what it takes to simply draw a line.

<h2>Dashed Lines</h2>
Now we'll tackle the main problem, achieving the dashed line style. Similarly to drawingContext hack, we want to be able to have variable dash lengths and variable length dash gaps. We'll need to first figure out the overall length of the line.

Next we'll have to split up the line into segments of a specific length, with spaces in between, also of a specific length.
<pre><code>
this.numSegments = this.dist / (this.segmentLength + this.spacing);
</code></pre>

At this point we encounter our first problem, how do we split up a segment of fixed length into a number of sub-segments, whose combined length might not exactly add up to the total length of the entire segment? In actuality, we have to consider two cases, the segment and space length either add up exactly n times to fill out the segment, or they simply don't. If they do add up, great! Otherwise, we'll have to write some code to handle that case. Let's first have a look at what it looks like without handling that case: 
<pre><code>class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));

    // calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
    
    // calculate number of segments
    this.numS = this.L / (this.segmentLength+this.spaceLength)
    console.log(this.numS)
  }

  display() {
     for(let i = 0; i < this.numS; i++){
       console.log(i)
       line(
         this.x1 + (this.segmentLength + this.spaceLength)*i*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*i*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*(i+1)*cos(this.S)-this.spaceLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*(i+1)*sin(this.S)-this.spaceLength*sin(this.S),
           )
     }
  }
}

function setup() {
  createCanvas(400, 400);
  
  sls = [];
  for(i = 0; i < 20; i++){
    sls.push(new SlopeLine(100, 100+10*i, 300, 100+10*i, random(1,35),random(1,35)));
  }
}

function draw() {
  background(220);

  for(i = 0; i < 20; i++){
    stroke(0)
    strokeWeight(3);
    sls[i].display();
  
    // Additionally  we're gonna draw two red points to see where the line should start and end
    strokeWeight(5);
    stroke(255,0,0)
    point(100,100+i*10);
    point(300,100+i*10);
  }
}
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/dashed dirty2.png" alt="" /></span>

Here we're actually drawing a couple of lines with the dash length and in-between spacing randomized. The red points show where the line start and where it should end. You can see that in some cases the last dash protrudes further than the second red dot. My OCD thinks that this looks very bad. Let's also seee what it looks like if we spread these lines on an arc:


<pre><code>function setup() {
  createCanvas(400, 400);
  
  sls = [];
  radius = 50;
  for(i = 0; i < 20; i++){
    sls.push(new SlopeLine(200+radius*cos(TWO_PI/20*i),
                           200+radius*sin(TWO_PI/20*i),
                           200+radius*cos(TWO_PI/20*i)*2,
                           200+radius*sin(TWO_PI/20*i)*2, 
                           random(1,35),
             random(1,35)));
  }
}

function draw() {
  background(220);

  for(i = 0; i < 20; i++){
    stroke(0)
    strokeWeight(3);
    sls[i].display();
  
    // Additionally  we're gonna draw two red points to see where the line should start and end
    strokeWeight(5);
    stroke(255,0,0)
    point(200+radius*cos(TWO_PI/20*i),
                           200+radius*sin(TWO_PI/20*i));
    point(200+radius*cos(TWO_PI/20*i)*2,
                           200+radius*sin(TWO_PI/20*i)*2);
  }
}
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/dashed dirty.png" alt="" /></span>

<h2>Fixing the trailing dashes</h2>
This was genuinely a headache to fix. A first thought was to simply check if the length of n * (segment length + space length) exceeds the length of the segment, and then discard the last dash. But that turned out to be quite ugly as well:

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/discarded.png" alt="" /></span>

We'll have to make a check just for the last dash, this can be done by finding the total length of all segments and spaces:
<pre><code>
var check = sqrt(
      pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*sin(this.S)-this.spaceLength*sin(this.S), 2 ) 
      + pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*cos(this.S)-this.spaceLength*cos(this.S), 2)
    )
</code></pre>
Notice that we're casting 'this.numS' to an integer, this was maybe the one thing that I was stuck on for a while. We're casting to an integer to be able to draw an exact number of segments and spaces. We're also adding 1 to the number of segments because we're stopping the previous for loop at 'this.numS-1'. Then we also subtract one length of a spacing, since we only care about where the dash ends, not where the dash and it's following space end. If we now draw a green point at this position, we obtain:


<pre><code>
stroke(0,255,0)
      strokeWeight(5)
      point(this.x1 +
        (this.segmentLength+ this.spaceLength)*
        (int(this.numS)+1)*cos(this.S)-
        this.spaceLength*cos(this.S),
        
         this.y1+(this.segmentLength + this.spaceLength)*
        (int(this.numS)+1)*sin(this.S)-
        this.spaceLength*sin(this.S)
      )
</code></pre>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/green dot.png" alt="" /></span>

Next we just need to check if the length of the last dash exceeds the total length of the line or not, and draw the lines accordingly:
<pre><code>stroke(255,255,0)
strokeWeight(3)
if(check<this.L){
  line(
     this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S),
     this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S),
     this.x1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*cos(this.S)-this.spaceLength*cos(this.S),
     this.y1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*sin(this.S)-this.spaceLength*sin(this.S)
       )
}else if(check>this.L){
  line(
     this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S),
     this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S),
     this.x2,
     this.y2
  )
}
</code></pre>
Here you can see in yellow the final dash segments and the green point indicates where the segment would have ended otherwise. Is this a good solution? Probably not, but it works for now.
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Fixed.png" alt="" /></span>

<h2>Putting it all together</h2>

<pre><code>class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));
    console.log(this.L)

    // calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
    
    // calculate number of segments
    this.numS = this.L / (this.segmentLength+this.spaceLength)
    console.log(this.numS)
    
    // calculate length of tail
    this.tailL = this.L % (this.segmentLength+this.spaceLength)
  }

  display() {
     for(let i = 0; i < this.numS-1; i++){
       //console.log(i)
       line(
         this.x1 + (this.segmentLength + this.spaceLength)*i*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*i*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*(i+1)*cos(this.S)-this.spaceLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*(i+1)*sin(this.S)-this.spaceLength*sin(this.S)
           )
     }
    
    var check = sqrt(
      pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*sin(this.S)-this.spaceLength*sin(this.S), 2 ) 
      + pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*cos(this.S)-this.spaceLength*cos(this.S),2)
    )
    
    stroke(0,255,0)
      strokeWeight(5)
      point(this.x1 +
        (this.segmentLength+ this.spaceLength)*
        (int(this.numS)+1)*cos(this.S)-
        this.spaceLength*cos(this.S),
        
         this.y1+(this.segmentLength + this.spaceLength)*
        (int(this.numS)+1)*sin(this.S)-
        this.spaceLength*sin(this.S)
      )
    
    stroke(255,255,0)
      strokeWeight(3)
    if(check<this.L){
      line(
         this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*cos(this.S)-this.spaceLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*sin(this.S)-this.spaceLength*sin(this.S)
           )
    }else if(check>=this.L){
      line(
         this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S),
         this.x2,
         this.y2
           )
     }
  }
}

function setup() {
  createCanvas(400, 400);
  sl = new SlopeLine(100, 200, 300, 100, random(2,35),random(2,35));
}

function draw() {
  background(220);
  stroke(0)
  strokeWeight(3);
  sl.display();
  strokeWeight(5);
  stroke(255,0,0)
  point(100,200);
  point(300,100);
}
</code></pre>

This works, and it even works for any numbers that you give as parameters for segment length and spacing length. Except for negative numbers, which don't make sense to begin with.

<h2>Animating the dashes</h2>

<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Dashed Final.gif" alt="" /></span>
Next we'll want to look into conveying motion with the dashes, which is a little tricky but actually easier than you might think. We want them to flow from the start of the line towards the end, where they seemingly disappear and reappear at the beginning of the line. Since we already wrote solid code, it won't take many modifications to achieve this effect. First, we'll have to create a parameter called 'this.beginning', such that the class now becomes:

<pre><code>class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));
    console.log(this.L)

    // Calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)
    
    // Calculate number of segments
    this.numS = this.L / (this.segmentLength+this.spaceLength)
    console.log(this.numS)
  
    this.beginningLength = 0;
  }
  
  move(rate){
    this.beginningLength += rate
    if(this.beginningLength>this.segmentLength+this.spaceLength){
      this.beginningLength = 0;
    }
  }
  
  display() {
     for(let i = 0; i < this.numS-1; i++){
       line(
         this.x1 + (this.segmentLength + this.spaceLength)*i*cos(this.S) 
            + this.beginningLength*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*i*sin(this.S) 
            + this.beginningLength*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*(i+1)*cos(this.S)
            -this.spaceLength*cos(this.S) + this.beginningLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*(i+1)*sin(this.S)
            -this.spaceLength*sin(this.S) + this.beginningLength*sin(this.S)
           )
     }
    
    var check = sqrt(
      pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*sin(this.S)
          -this.spaceLength*sin(this.S)+ this.beginningLength*sin(this.S), 2) 
      + pow((this.segmentLength + this.spaceLength)*(int(this.numS)+1)*cos(this.S)
      +   -this.spaceLength*cos(this.S)+ this.beginningLength*cos(this.S), 2)
    )
    
    stroke(0,255,0)
      strokeWeight(5)
      point(this.x1 +
        (this.segmentLength+ this.spaceLength)*
        (int(this.numS)+1)*cos(this.S)-
        this.spaceLength*cos(this.S)+ this.beginningLength*cos(this.S),
        
         this.y1+(this.segmentLength + this.spaceLength)*
        (int(this.numS)+1)*sin(this.S)-
        this.spaceLength*sin(this.S)+ this.beginningLength*sin(this.S)
      )
    
    stroke(255,255,0)
      strokeWeight(3)
    if(check<this.L){
      line(
         this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S)+ this.beginningLength*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S)+ this.beginningLength*sin(this.S),
         this.x1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*cos(this.S)-this.spaceLength*cos(this.S)+ this.beginningLength*cos(this.S),
         this.y1 + (this.segmentLength+ this.spaceLength)*((int(this.numS))+1)*sin(this.S)-this.spaceLength*sin(this.S)+ this.beginningLength*sin(this.S)
           )
    }else if(check>this.L){
      line(
         this.x1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*cos(this.S)+ this.beginningLength*cos(this.S),
         this.y1 + (this.segmentLength + this.spaceLength)*(int(this.numS))*sin(this.S)+ this.beginningLength*sin(this.S),
         this.x2,
         this.y2
           )
    }
  }
}

function setup() {
  createCanvas(400, 400);
  
  sls = [];
  for(i = 0; i < 20; i++){
    sls.push(new SlopeLine(100, 100+10*i, 300, 100+10*i, random(1,35),random(1,35)));
  }
}

function draw() {
  background(220);

  for(i = 0; i < 20; i++){
    stroke(0)
    strokeWeight(3);
    sls[i].display();
    sls[i].move(0.2)
  
    // Additionally  we're gonna draw two red points to see where the line should start and end
    strokeWeight(5);
    stroke(255,0,0)
    point(100,100+i*10);
    point(300,100+i*10);
  }
}
</code></pre>

And we end up with something similar to this:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Animation Attempt 1.gif" alt="" /></span>
Watching this go on for a little while, you cans ee how unsatisfying it to see the first dash of each line pop up out of seemingly nowhere. We need to draw the line that goes from the first red point to the beginning of the space preceding the first dash. This simulates as if the dashed line is continuously appearing from the left side, and already makes the animation look much less jumpy:
<pre><code>// Add this to beginning of display function
if(this.beginningLength > this.spaceLength){
       stroke(255,0,255)
       line(this.x1, this.y1,
       this.x1 + (this.beginningLength-this.spaceLength)*cos(this.S),
       this.y1+ (this.beginningLength-this.spaceLength)*sin(this.S)
           )
     }
</code></pre>
Now we have something that looks like this:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Animation Attempt 2.gif" alt="" /></span>
And the last thing that we need to do is make it such that the newly protruding last dash gets truncated. Since the protruding last dash will be 

<pre><code>
class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));
    console.log(this.L)

    // calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)

    // calculate number of segments
    this.numS = this.L / (this.segmentLength + this.spaceLength)
    console.log(this.numS)

    // calculate length of tail
    this.tailL = this.L % (this.segmentLength + this.spaceLength)

    this.beginningLength = 0;
    this.tailLength = this.tailL;
    this.difference = (this.segmentLength + this.spaceLength)

  }

  move(rate) {
    this.beginningLength += rate
    if (this.beginningLength >= this.segmentLength + this.spaceLength) {
      this.beginningLength = 0;
    }
    
    this.difference = (this.segmentLength + this.spaceLength)-this.beginningLength
  }



  display() {
    if (this.beginningLength > this.spaceLength) {
      stroke(255, 0, 255)
      line(this.x1, this.y1, this.x1 + (this.beginningLength - this.spaceLength) * cos(this.S), this.y1 + (this.beginningLength - this.spaceLength) * sin(this.S))
    }

    stroke(0)
    for (let i = 0; i < this.numS; i++) {
      var distCheck = sqrt(pow((this.segmentLength + this.spaceLength) * (i + 1) * cos(this.S) - this.spaceLength * cos(this.S) + this.beginningLength * cos(this.S), 2) + pow((this.segmentLength + this.spaceLength) * (i + 1) * sin(this.S) - this.spaceLength * sin(this.S) + this.beginningLength * sin(this.S), 2))

      //console.log(i)
      if (distCheck <= this.L) {
        //console.log(i)
        line(
          this.x1 + (this.segmentLength + this.spaceLength) * i * cos(this.S) + this.beginningLength * cos(this.S),
          this.y1 + (this.segmentLength + this.spaceLength) * i * sin(this.S) + this.beginningLength * sin(this.S),
          this.x1 + (this.segmentLength + this.spaceLength) * (i + 1) * cos(this.S) - this.spaceLength * cos(this.S) + this.beginningLength * cos(this.S),
          this.y1 + (this.segmentLength + this.spaceLength) * (i + 1) * sin(this.S) - this.spaceLength * sin(this.S) + this.beginningLength * sin(this.S)
        )
      } else {

        var distCheck = sqrt(pow(this.x1 + (this.segmentLength + this.spaceLength) * i * cos(this.S) + this.beginningLength * cos(this.S), 2) + pow(this.y1 + (this.segmentLength + this.spaceLength) * i * sin(this.S) + this.beginningLength * sin(this.S), 2))
        if (distCheck < this.L) {
          line(
            this.x1 + (this.segmentLength + this.spaceLength) * i * cos(this.S) + this.beginningLength * cos(this.S),
            this.y1 + (this.segmentLength + this.spaceLength) * i * sin(this.S) + this.beginningLength * sin(this.S),
            this.x2, this.y2
          )
        } else {
  

        }
      }
    }

    var check = sqrt(
      pow((this.segmentLength + this.spaceLength) * (int(this.numS) + 1) * sin(this.S) - this.spaceLength * sin(this.S) + this.beginningLength * sin(this.S), 2) +
      pow((this.segmentLength + this.spaceLength) * (int(this.numS) + 1) * cos(this.S) - this.spaceLength * cos(this.S) + this.beginningLength * cos(this.S), 2)
    )

    stroke(0, 255, 0)
    strokeWeight(5)


    stroke(0, 0, 0)
    strokeWeight(3)


  }
}

function setup() {
  createCanvas(400, 400);

  sls = [];
  for (i = 0; i < 20; i++) {
    sls.push(new SlopeLine(100, 100 + 10 * i, 300, 100 + 10 * i, random(1, 35), random(1, 35)));
  }
}

function draw() {
  background(220);

  for (i = 0; i < 20; i++) {
    stroke(0)
    strokeWeight(3);
    sls[i].display();
    sls[i].move(0.2)

    // Additionally  we're gonna draw two red points to see where the line should start and end
    strokeWeight(5);
    stroke(255, 0, 0)
    point(100, 100 + i * 10);
    point(300, 100 + i * 10);
  }

  //noLoop()
}
</code></pre>
And we finally end up with this:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Dashed Final.gif" alt="" /></span>
Now the protruding dash doesn't appear anymorer but we have a weird behaviour where the last dash simply disappears. We don't want that.

<h2>Final Result</h2>

The final code goes here:
<pre><code>class SlopeLine {
  constructor(x1, y1, x2, y2, segmentLength, spaceLength) {
    this.x1 = x1;
    this.y1 = y1;
    this.x2 = x2;
    this.y2 = y2;
    this.segmentLength = segmentLength;
    this.spaceLength = spaceLength;

    // Calculate length
    this.L = sqrt(
      pow((this.x1 - this.x2), 2) +
      pow((this.y1 - this.y2), 2));
    console.log(this.L)

    // calculate angle
    this.S = atan2(this.y2 - this.y1, this.x2 - this.x1)

    // calculate number of segments
    this.numS = this.L / (this.segmentLength + this.spaceLength)
    console.log(this.numS)

    // calculate length of tail
    this.tailL = this.L % (this.segmentLength + this.spaceLength)

    this.beginningLength = 0;
    this.tailLength = this.tailL;
    this.difference = (this.segmentLength + this.spaceLength)

  }

  move(rate) {
    this.beginningLength += rate
    if (this.beginningLength >= this.segmentLength + this.spaceLength) {
      this.beginningLength = 0;
    }
    
    this.difference = (this.segmentLength + this.spaceLength)-this.beginningLength
  }


  display() {
    if (this.beginningLength > this.spaceLength) {
      stroke(255, 0, 255)
      line(this.x1, 
           this.y1, 
           this.x1 + (this.beginningLength - this.spaceLength) * cos(this.S), 
           this.y1 + (this.beginningLength - this.spaceLength) * sin(this.S)
          )      
    }
  

    stroke(0)
    for (let i = 0; i < this.numS; i++) {
      stroke(0)
      var distCheck = sqrt(
        pow(
          (this.segmentLength + this.spaceLength) * (i + 1) 
          * cos(this.S) - this.spaceLength * cos(this.S) 
          + this.beginningLength * cos(this.S), 2) 
        + pow((this.segmentLength + this.spaceLength) * (i + 1) 
          * sin(this.S) - this.spaceLength * sin(this.S) 
              + this.beginningLength * sin(this.S), 2))

      //console.log(i)
      if (distCheck <= this.L) {
        //console.log(i)
        line(
          this.x1 + (this.segmentLength + this.spaceLength) * i * cos(this.S) + this.beginningLength * cos(this.S),
          this.y1 + (this.segmentLength + this.spaceLength) * i * sin(this.S) + this.beginningLength * sin(this.S),
          this.x1 + (this.segmentLength + this.spaceLength) * (i + 1) * cos(this.S) - this.spaceLength * cos(this.S) + this.beginningLength * cos(this.S),
          this.y1 + (this.segmentLength + this.spaceLength) * (i + 1) * sin(this.S) - this.spaceLength * sin(this.S) + this.beginningLength * sin(this.S)
        )
      } else {

        stroke(255,255,0)
        var distCheck = 
            sqrt(
              pow((this.segmentLength + this.spaceLength) * i 
                  * cos(this.S) + this.beginningLength * cos(this.S), 2) 
              + pow((this.segmentLength + this.spaceLength) * i 
                    * sin(this.S) + this.beginningLength * sin(this.S), 2))
       
        if (distCheck  < this.L) {
          line(
            this.x1 + (this.segmentLength + this.spaceLength) * i * cos(this.S) + this.beginningLength * cos(this.S),
            this.y1 + (this.segmentLength + this.spaceLength) * i * sin(this.S) + this.beginningLength * sin(this.S),
            this.x2, this.y2
          )
        }
      }
    }
  }
}

function setup() {
  createCanvas(400, 400);

  sls = [];
  for (i = 0; i < 20; i++) {
    sls.push(new SlopeLine(100, 100 + 10 * i, 300, 100 + 10 * i, random(1, 35), random(1, 35)));
  }
}

function draw() {
  background(220);

  for (i = 0; i < 20; i++) {
    stroke(0)
    strokeWeight(3);
    sls[i].display();
    sls[i].move(0.2)

    // Additionally  we're gonna draw two red points to see where the line should start and end
    strokeWeight(5);
    stroke(255, 0, 0)
    point(100, 100 + i * 10);
    point(300, 100 + i * 10);
  }

  //noLoop()
}
</code></pre>

As sanity check, it works fine with any kind of slanted line as well:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-30-Animated-Dashed-Lines-in-P5JS/Circular Final.gif" alt="" /></span>

<h2>Reversing the movement</h2>
Ok, one absolutely last thing will be reversing the movement to go in the other direction. We need to modify the move function one last time

<pre><code>
move(rate) {
    if(rate>0){
        this.beginningLength += rate
        if (this.beginningLength >= this.segmentLength + this.spaceLength) {
          this.beginningLength = 0;
        }

        this.difference = (this.segmentLength + this.spaceLength)-this.beginningLength
    }else if(rate<0 ){
        this.beginningLength += rate
        if (this.beginningLength <= -this.segmentLength - this.spaceLength) {
          this.beginningLength = 0;
        }
        this.difference = (this.segmentLength + this.spaceLength)-this.beginningLength
    }
  }
</code></pre>

This works, but again we will have to make checks for the lines that protrude outside the boundaries of the red dots.
