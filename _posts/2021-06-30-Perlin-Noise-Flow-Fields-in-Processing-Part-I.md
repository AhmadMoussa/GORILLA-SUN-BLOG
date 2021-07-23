---
title: Perlin Noise Flow Fields in Processing - Part I
author: Ahmad Moussa
categories:
  - p5js
description: This blog post will explain how to code up a Flow Field in Processing.
thumbnail_path: 2021-04-16-Generative-Art-and-Creative-Coding-Showcase.png
published: true
---
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/fieldColorDashedShort58.png" alt="" /></span>

1. <a href='#intro'>Introduction</a>
2. <a href='#what'>What are Flow Fields?</a>
3. <a href='#make'>Components of a Flow Field</a>
4. <a href='#ga'>The GridAngle Object</a>
5. <a href='#al'>Array Lists in Java</a>
6. <a href='#populate'>Populating the Grid</a>
7. <a href='#draw'>Drawing the Grid</a>


<h2><a name='intro'>Introduction</a></h2>
This is the 5-th and final part of the explorative series on Perlin Noise, where I explain how to code up a Flow Field, and this time we'll take a step away from P5JS and do it in Processing. Technically, all of this is possible and maybe easier to program in P5 but for efficiency reasons Processing is the framework of choice. This post might end up being a little bit lengthy and tough, but is definitely worth the trouble.


<div class="row gtr-200">
			<div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/f1.png" alt="" /></span>
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/f3.png" alt="" /></span>
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/f2.png" alt="" /></span>
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/f4.png" alt="" /></span>
      </div>
</div>

The code outlined in this post is loosely based on <a href='https://tylerxhobbs.com'>Tyler Hobbs</a> pseudocode on flow fields in his excellent <a href='https://tylerxhobbs.com/essays/2020/flow-fields'>essay</a> on them, which I recommend reading and maybe even taking at stab at implementing the pseudocode outlined in it, before following this tutorial. Naturally, my code is probably different, and merely shows a rough idea of what you can do with flow fields. Also note that the code shown here is far from optimal and equivalently is not the only way to create flow fields, but gives a basic idea about the steps involved in making one.

<h2><a name='what'>What are Flow Fields?</a></h2>
<a href='https://www.quora.com/What-is-a-flow-field-in-fluid-mechanics'>Flow Fields</a> are a concept borrowed from fluid mechanics, and essentially simulate the behaviour of some substance (that has certain properties) over a surface or within a volume. Flow Fields have a special place in creative coding, and allow you to create mesmerizing generative artworks that wouldn't otherwise be possible. Tyler Hobbs, for instance, is a true master at them, and to quote him:

<blockquote>It's entirely possible that I've used them in more programs than any other person alive.</blockquote>

<blockquote class="twitter-tweet tw-align-center"><p lang="es" dir="ltr">ECTOGENESIS, 2019 <a href="https://t.co/zFFqtaO570">pic.twitter.com/zFFqtaO570</a></p>&mdash; Tyler Hobbs (@tylerxhobbs) <a href="https://twitter.com/tylerxhobbs/status/1401555981402771456?ref_src=twsrc%5Etfw">June 6, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<h2><a name='make'>Components of a Flow Field</a></h2>
Making a flow field essentially consists of two parts, firstly creating a grid of angles, and secondly drawing flow lines whose curvature is guided by this grid. This will make more sense in a second. Since this is a little bit loaded we'll split these two parts up into separate blog posts. The grid in this one, and the flow lines in the next one.

<div class="row gtr-200">
			<div class="col-6 col-12-medium">
              <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/grid.png" alt="" /></span>
      </div>
      <div class="col-6 col-12-medium">
        <span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/flow.png" alt="" /></span>
      </div>
</div>

The flow lines here look a bit weird, but we'll make them look much nicer along the way.

<h2><a name='ga'>Creating a Grid of Angles</a></h2>
The very first thing we'll want to do is create a custom class for the angles in our grid. This will make things more convenient for us later on.

If you're not used to Java syntax this might be a little confusing at first, especially if you're used to javascript (and/or python) which is very permissive with it's variable types. Generally, in java you need to specify the type of a variable when you first create it, and mixing between different variable types can lead to odd errors and unexpected behaviour that can take some time to debug. Let's start with a simple class in java:

<pre><code>class GridAngle {
  int x, y;
  GridAngle(int x_, int y_){
    x = x_;
    y = y_;
  }
}
</code></pre>

This class doesn't do anything yet, but simply serves as an example of the syntax involved in writing a java class. The member variables are declared within the space directly after declaring the class with the class argument and class name. The constructor is designated by the class name and accepts input parameters. When creating a function, be it a constructor or any other function that accepts input parameters, you're required to specify the type of the variable by preceding it with the correct keyword, similarly to any other time you create a new variable in Java. The actual class that we're going to use has some more stuff going on, as follows:

<pre><code>class GridAngle {
  int x, y, r;
  float angle;

  PVector v;

  GridAngle(int x_, int y_, int r_, float angle_) {
    x = x_;
    y = y_;

    angle = angle_;
    r = r_;
    v = new PVector(x + r * cos(angle),
                    y + r * sin(angle));
  }

  void display() {
    strokeWeight(2);
    line(x, y, v.x, v.y);
  }
}
</code></pre>

Take a second and read through the code. Here the class simply consists of a position and an angle. Processing's vector class 'PVector' will help us when drawing the the flowlines through the grid, and also allows us to visualize the grid.

Now that we have created this GridAngle class, we'll actually want to create some instances of it and store them somehow in a gridlike manner. This will require us to store our instances in an array, which is a little bit tricky in Java. Generally when creating an array in Java, you need to know it's size beforehand, to allocate the appropriate amount of space in memory. This won't work for us, since we want to have a grid that can have a different number of items depending on the parameters.

<h2><a name='al'>Array Lists in Java</a></h2>
However there is a way, using something called an ArrayList! It's essentially an array like data-structure in java, that allows easy addition and manipulation of items within it. Maybe the scariest thing about it is the way it's instantiated:

<pre><code>final ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt; vectors 
			= new ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt;();
</code></pre>

Let's go through this step by step! The final keyword here is similar to the 'const' keyword in javascript that declares a constant. Don't worry, this won't mean that we can't modify our ArrayList anymore, simply that we can't reassign its reference. A more elaborate discussion can be found <a href='https://stackoverflow.com/questions/10750791/what-is-the-sense-of-final-arraylist'>here</a>.

Next up, ArrayLists can only hold one data type, which needs to be specified in the space between the arrow brackets, so for example, if we were to create an ArrayList of integers, we would do it as follows:

<pre><code>ArrayList&lt;int&gt; numbers = new ArrayList&lt;int&gt;();
</code></pre>

This means we can create an arraylist of arbitrary data types, even ones we create ourselves, such as the GridAngle class we created earlier:

<pre><code>ArrayList&lt;GridAngle&gt; gridangles = new ArrayList&lt;GridAngle&gt;();
</code></pre>

To make this a nested ArrayList we simply create an Array List that can hold other Array Lists:

<pre><code>ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt; gridangles 
			= new ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt;();
</code></pre>

Makes sense right?

<h2><a name='populate'>Populating the Grid</a></h2>
In addition to that we'll also need a couple of other parameters and a function that actually fills this ArrayList:

<pre><code>final int xOff = 50;
final int yOff = 50;
final int spacing = 10;
final float rez = 0.0005;
</code></pre>

The function that fills our ArrayList will look as follows, and this is actually where we'll make use of Perlin Noise:
<pre><code>void createGrid() {
  for (int x = xOff; x&lt;width-xOff; x+=spacing) {
    ArrayList&lt;GridAngle&gt; row  = new ArrayList&lt;GridAngle&gt;();
    for (int y = yOff; y&lt;width-yOff; y+=spacing) {
      float angle = map(noise(x*rez, y*rez), 0.0, 1.0, 0.0, TAU);

      row.add(new GridAngle(x, y, 5, angle));
    }
    grid.add(row);
  }
}
</code></pre>

Nothing special here: a nested loop to populate the nested ArrayList, the noise function to which we feed the x and y coordinates of each grid angle, multiplied by the resolution factor, and we map it to the [0,TAU] range to get an angle that is somewhere between 0 and 360 degrees.

<h2><a name='draw'>Drawing the Grid</a></h2>

Later on we won't need to visualize this grid anymore, but for now it's interesting to see what it looks like. This boils down to looping over our nested array list and invoking the display function that we have created earlier as a part of the GridAngle class:

<pre><code>void setup(){
  size(600,600);
  createGrid();
}

void draw() {
  background(220);
  for (int x = 0; x&lt;vectors.size(); x++) {
    for (int y = 0; y&lt;vectors.get(0).size(); y++) {
      vectors.get(x).get(y).display();

      ga.display();
    }
  }
}
</code></pre>

And you should see something like this:
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-06-30-Perlin-Noise-Flow-Fields-in-Processing-Part-I/gridx.png" alt="" /></span>

Which almost looks like an optical illusion, the dashes seem to shift whenever you let your eyes wander over it. Great, this concludes the first part of the tutorial, and in the next part we'll cover how to draw the flowlines using this grid!

The entire code for reference:
<pre><code>final ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt; grid = new ArrayList&lt;ArrayList&lt;GridAngle&gt;&gt;();

final int xOff = 50;
final int yOff = 50;
final int spacing = 10;
final float rez = 0.005;

class GridAngle {
  int x, y, r;
  float angle;

  PVector v;

  GridAngle(int x_, int y_, int r_, float angle_) {
    x = x_;
    y = y_;

    angle = angle_;
    r = r_;
    v = new PVector(x + r * cos(angle),
                    y + r * sin(angle));
  }

  void display() {
    strokeWeight(2);
    line(x, y, v.x, v.y);
  }
}

void createGrid() {
  for (int x = xOff; x<width-xOff; x+=spacing) {
    ArrayList&lt;GridAngle&gt; row  = new ArrayList&lt;GridAngle&gt;();
    for (int y = yOff; y<width-yOff; y+=spacing) {
      float angle = map(noise(x*rez, y*rez), 0.0, 1.0, 0.0, TAU);

      row.add(new GridAngle(x, y, spacing/2, angle));
    }
    grid.add(row);
  }
}

void setup(){
  size(600,600);
  createGrid();
}

void draw() {
  background(220);
  for (int x = 0; x&lt;grid.size(); x++) {
    for (int y = 0; y&lt;grid.get(0).size(); y++) {
      grid.get(x).get(y).display();
    }
  }
  save("grid.png");
  noLoop();
}
</code></pre>
