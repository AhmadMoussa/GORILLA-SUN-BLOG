---
layout: default
title: 9 Tips for Tsubuyaki Processing
author: Ahmad Moussa
categories:
  - p5js
description: Tsubuyaki processing has become a popular and fun little challenge on Twitter. In this post I'll be going over some character saving tricks that can come in clutch when you're out of ideas for reducing the size of your code!
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/compressed/tsubuyaki.jpg
published: true
exclude_rss: true
legacy: false
listed: true
---

<div class="image fit" style="display: block; margin: 0 0 0 0; padding: 0 0 0 0;">
  <video autoplay="" loop="" muted="" playsinline="" style="width:100%; border-radius: 0.375em; margin: 0 0 0 0;" draggable="true">
    <source src="https://gorillasun.de/assets/images/tsubuyaki/banner.mp4" type="video/mp4">
  </video>
</div>

<p></p>

Initially this was supposed to be a quick post on a bunch of useful tricks to shorten the javascript code of your #つぶやきProcessing sketches. However, I really needed to dig into and research some points on my list, because I wasn't 100% sure how certain things actually worked! This made the post end up being a list of 10 #つぶやきProcessing tips, each of which is a tiny javascript lesson.

<h2><a name='return'></a>What's #つぶやきProcessing?</h2>

#つぶやきProcessing, pronounced Tsu-bu-ya-ki processing, literally translated  meaning 'Tweet Processing', is a Twitter specific code golfing challenge, where you try to fit a compilable and functioning piece of P5 or processing code into a single tweet. Generally these tweets are also posted alongside the graphics that they generate.

This 280 character sized challenge first emerged with Japanese twitter user <a href='https://twitter.com/Hau_kun'>Hau-kun</a> in 2019:

<blockquote class="twitter-tweet tw-align-center"><p lang="ja" dir="ltr">唐突に <a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8DProcessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきProcessing</a> というタグを作ってみる。</p>&mdash; はぅ君 (@Hau_kun) <a href="https://twitter.com/Hau_kun/status/1133006598136139776?ref_src=twsrc%5Etfw">May 27, 2019</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<p></p>
He says as much as:

<blockquote>I'm going to create a tag #TweetProcessing out of the blue!</blockquote>

And in the replies he elaborates that he had been previously trying to make a rainbow sketch within the 140 character limit, and that the character limit increase had given him a lot more flexibility. And it genuinely seems tough to make anything interesting within 140 characters. Hau-kun then started tweeting many #つぶやきProcessing sketches over the course of the next months. Fast forward a bit, and lots of folks started joining in before the hashtag started to take on a life of it's own:

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">i=n=a=0;draw=_=&gt;{i||createCanvas(q=512,q);colorMode(HSB,1);v=createVector;h(i++);m=a.mult(4).add(2);stroke(i/4**7,1,1);line(m.x,m.y,n.x,n.y);n=m};h=i=&gt;{a=v((i&amp;3)&gt;&gt;1,[0,1,1][i&amp;3]);for(j=1;j&lt;8;){i/=4;k=i&amp;3;l=2**j++;!k?a=v(a.y,a.x):k&lt;2?a.y+=l:k==2?a.add(l,l):a=v(l-1-a.y+l,l-1-a.x)}} <a href="https://t.co/23S3gHsm4J">pic.twitter.com/23S3gHsm4J</a></p>&mdash; Daniel Shiffman (@shiffman) <a href="https://twitter.com/shiffman/status/1333117934827343872?ref_src=twsrc%5Etfw">November 29, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<p></p>

Hence, let's have a closer look at some tips to get you started with #つぶやきProcessing! Enjoy, it's a fun one! Here's the index:


<h3>The Tips:</h3>
1. <a href='#return'>Expression return values</a>
2. <a href='#omit'>Omitting the draw and setup functions</a>
3. <a href='#reference'>Storing functions in variables</a>
4. <a href='#arrow'>Arrow functions</a>
5. <a href='#ternary'>The conditional ternary operator</a>
6. <a href='#skip'>Skipping last condition in a for loop</a>
7. <a href='#forcode'>Code inside the for loop</a>
8. <a href='#multcond'>Multiplying a condition</a>
9. <a href='#multiple'>Multiple assignment with an array</a>
10. <a href='#final'>Putting it all together</a>


<h2><a name='return'></a>Expression return values</h2>

Let's start off with a tip that will come in handy in each and every one of your #つぶやきProcessing sketches! Drawing anything to the canvas requires creating it first. Since we're short for characters, we most likely don't have the luxury to make a sketch that adjusts to the window dimensions. For this reason most #つぶやきProcessing are created with a fixed canvas size:

<pre><code>function setup(){
  w = 600;
  createCanvas(w, w);
}
</code></pre>

This first trick involves placing the declaration of the canvas size within the createCanvas() function, like so:

<pre><code>createCanvas(w=600,w);
</code></pre>

Essentially we're assigning a value to the variable 'w' AND immediately reusing it as a second parameter for the createCanvas() function... now why does this work? It has to do with what is considered an expression and what is considered a statement in javascript:

<pre><code>// this works because w = 600 is an expression
createCanvas(w=600,w);

// this doesn't work because var w = 600 is a statement
createCanvas(var w=600,w);
</code></pre>

In general, expressions in javascript are <a href='https://medium.com/launch-school/javascript-expressions-and-statements-4d32ac9c0e74'>units of code that can be evaluated to a value</a>. Here the assignment is in fact an expression, which will return the value that was assigned. And this is by design. It also enables things like multiple assignment, for example:

<pre><code>a = x = y = 5;</code></pre>

Which could also be a useful trick to save you some characters. More on this <a href='https://stackoverflow.com/q/16027247/6688750'>here</a>.






<h2><a name='omit'></a>Omitting the draw and setup functions</h2>

We generally structure our p5 sketches to have a setup function, where we do some preliminary things, and then draw our graphics in the draw loop which runs after the setup function has finished. However it is not always necessary to have both the setup and draw function. When we're simply dealing with a static image we don't require the draw function at all, and could simply write our code at the end of the setup function. This would also lets us make do without the noLoop() function call.

On the other hand we could also omit the setup function, which is a little tricky because we still need to create the canvas, and the createCanvas() function should generally only be called once. A nifty trick for this is:

<pre><code>f=0;
function draw(){
  f++||createCanvas(w=600,w);
}
</code></pre>

Neat right?! On the very first draw call the expression 'f++' returns 0, which forces the sketch to check the second part of the condition, which in turn will actually create the canvas. On subsequent loops of the draw function the variable f will have been incremented, which means that it doesn't actually have to check the second part of the condition anymore. This is because values other than 0 are considered to be truthy.




<h2><a name='reference'></a>Storing functions in variables</h2>

Another nifty thing we can do to save on characters is replacing a function's name with one that is shorter. This is useful when you're using some inbuilt p5 functions that have a somewhat lengthy name, for example:

<pre><code>// let's shorten the random() function
// and store a reference to the random function in a variable
R = random

// we can now use the shortened version
func(R(), R())
</code></pre>

This is another trick that had me scratching my head! I get what's happening, but why is it possible?! For this we have to have a closer look at how javascript works internally. I asked the folks in the birbsnest discord and <a href=''>Julien Pradet</a> explained it very elegantly:

<blockquote>A function in javascript (and many other languages) is another type of value. Just like you have string or numbers, you have functions. So just like string and numbers, you can store functions in variables, move them around, etc.

So when you do function draw() { }, what you actually do is storing the draw function in a variable called draw. It's done automatically. But you could also write: let draw = function draw() { }. draw() would still behave the same way.</blockquote>

And also provided a nice example:

<pre><code>// This exists inside p5 even if it's not written this way
let random = function random() { /* ... */ }

// in your code
let R  = random; // in a sense, you're just copying the function into another variable
R(); // outputs the result of the `random`
</code></pre>

In conclusion he also adds:

<blockquote>[...] but beware, you're dangerously close from entering the world of functional programming. In no time, you'll be creating functions that return functions, want to curry all your functions and so on. (I'm personally in love with this way of writing code.)</blockquote>

So there's that, go show <a href='https://twitter.com/JulienPradet'>Julien</a> some love, he makes some cool gen-art and <a href='https://www.twitch.tv/superroipatate'>streams live coding on twitch</a> every monday and saturday. Also join the birbsnest, it's a lovely community.











<h2><a name='arrow'></a>Arrow functions</h2>

On the topic of functions, that 'function' keyword takes up an awfully large number of characters. We can do better here as well and save some characters by using arrow functions! Arrow functions are a javascript feature that has been introduced with ES6, and for our purposes enable a much more compact way to write function signatures:

<pre><code>// instead of doing this
function someFunc(a, b){
  return a + b
}

// we can omit the 'function' keyword and the name of the function
// and rewrite it in this manner by using the arrow function expression
(a, b) => {
  return a + b
}

// we can further reduce this by removing the curly braces
// and the return keyword
(a, b) => a + b

// to use this we can assign this arrow function to a variable
let someFunc = (a, b) => a + b

// which can then be called in this manner
someFunc(a, b)
</code></pre>


Ok a lot is happening here. Basically, arrow functions let us do without the 'function' keyword as well as the actual name of the function, instead we just specify what kind of arguments it should take, followed by the arrow symbol (equal and larger than symbols) pointing towards the body of the function. We can now also omit the curly braces, which tells js that the value following the arrow should be returned. In that way we can also omit the 'return' keyword. Now we can use this to shorten the signature lines of our setup and draw functions, ending up with something like this:

<pre><code>setup=()=>{createCanvas(w=600,w)}</code></pre>

Since the setup function doesn't take any paramteres, we can make it a parameterless arrow function by writing it with empty parentheses. However, we can make it even shorter, by declaring it such that it actually takes in a parameter, so you'll often see something like this:

<pre><code>setup=_=>{createCanvas(w=600,w)}</code></pre>

Now we could technically pass in an argument to the setup function, but the setup function is usually never called a second time from within the code, so it doesn't matter, and it saves us a character.








<h2><a name='ternary'></a>Ternary conditional operator</h2>

Conditional statements are also very useful and often needed to make interesting sketches. if/else statements however quickly add up to a significant amount of characters that we could save by employing the ternary operator:

<pre><code>// regular if else statement
if(condition) {
    func1()
}else{
    func2
}

// the ternary operator
condition ? func1() : func2();
</code></pre>

It's pretty straightforward actually, the condition goes before the question mark symbol, the first option after, followed by the second condition separated by a colon. We can also obtain more complicated logic by chaining them:

<pre><code>// multiple ternary operators chained together
condition ? func1() : condition2 ? func2() : func3();
</code></pre>


Notice how we can also skip the parentheses around the condition! Another thing I want to add here is that the curly braces are not always necessary, hence if for some reason you need to use an if statement, you can do it like this:

<pre><code>if(condition) doSomething();
</code></pre>

I personally don't like it, but it's a possibility.









<h2><a name='skip'></a>Skipping last condition in the for loop</h2>

A for loop formally consists of three parts: an initializer, a termination condition and an iterator. In most cases we can actually make do without the third part:

<pre><code>// regular for loop
for(let i = 0; i < 10; i++){}

// rewritten and shortened
for(let i = 10; i--;){}
</code></pre>

In this case we reversed the iteration order, counting down from ten rather than starting from 0. Why does this loop actually terminate? After completing ten iterations the variable will reach a point where it is equal to zero, in that case the for loop will check the condition and determine that it is false. Thus it will stop looping. I'm sure there are other creative ways to make use of this as well!








<h2><a name='forcode'></a>Code inside the for loop</h2>

Moreover, we can make use of the fact the initialization clause in the for loop only gets executed once, and the iteration clause several times. As a matter of fact, the for loop doesn't really care what you write inside of it. As long as you still fulfill the termination condition you should be fine. Let's have a look at an example:

<pre><code>f=0;
draw =_=> {
  for(
    f++ || createCanvas(w = 600, w),
    background(220),
    maxNum = 10,
    spacing = w/maxNum,
    n = 0;

    n < maxNum;

    rect(n++*spacing,
          (n-1)*spacing,
          spacing,
          spacing
        )
    );
}
</code></pre>

This would simply draw a couple of rectangles along the diagonal. All of our setup stuff went inside the first clause of the for loop! You just need to make sure that these statements are comma separated, the last of which terminated with a semin-colon. Then we have the termination condition, as well as the iteration clause. We are actually drawing the rectangles inside this clause, and incrementing the loop variable 'n' inside of it. Remember that an expression returns it's value? Throwing all of this together we can achieve some pretty interesting stuff, here's a sketch where I use this technique and a bunch of the previous ones:

<script src="//toolness.github.io/p5.js-widget/p5-widget.js"></script>
<script type="text/p5" data-p5-version="1.2.0" data-autoplay data-preview-width="350" data-height="400">
f=0,draw=(a=>{for(f++||createCanvas(w=762,w),background(0,50),noStroke(),p=w/9,N=21,n=N**2,s=(w-2*p)/N;n>0;k=millis()/w+n/3,l=w/6,c=(n=>l+l*n),o=cos(k),fill(c(sin(k)),c(o),c(-o)),ellipse(int(n--/N)*s+p,map(fract(n/N),0,.9,0,20)*s+p,s-3+s*sin(k)));});
</script>
<p></p>

Due to having a fixed canvas size you might have to copy it into the p5js editor to see it as intended. And credit where redit's due, putting code in the for loop I stumbled across through <a href='https://twitter.com/MaximGifmaker'>Maxim's</a> awesome video on Tsubuyaki Processing tricks:

<iframe width="100%" height="500" src="https://www.youtube.com/embed/PDj0NQLEMtQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<p></p>
Seriously go check it out, it's a really good video where goes over a number of sketches. All of which have some interesting tips and tricks!


<h2><a name='multcond'></a>Multiplying conditions</h2>

Another thing that comes in handy very often, is the ability to multiply conditional expressions with numbers. This is often useful when doing combinatoions and permutations of things. Let's have a look at how this works:

<pre><code>for(let i = 0; i < 10; i++){
  console.log( 5 * ( i % 2 == 0 ) )
}
</code></pre>

In this example the loop will print 5 when i is odd and 0 when i is even. This is because the boolean expression (i%2==0) when truthy evaluates to 1 and when falsy evaluates to 0. This came in incredibly useful when I did this recursive grid sketch:

<blockquote class="twitter-tweet tw-align-center"><p lang="en" dir="ltr">Even shorter:<br><br>setup=_=&gt;{createCanvas(w=600,w);s=w/9;q=w-s*2;c(s,s,q,q,7)};function c(x,y,i,h,d){if(d&gt;0){var[o,k]=[i/2,h/2];for(let n=4;n--;){c(x+o*(n==3||!n),y+k*(n==2||!n),o,k,d-random([1,2]))}}else{rect(x,y,i,h)}}<a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8DProcessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきProcessing</a> look Ma, so many leftover characters💪😎💪 <a href="https://t.co/KrY4mUWwso">https://t.co/KrY4mUWwso</a></p>&mdash; Ahmad Moussa || Gorilla Sun (@gorillasu) <a href="https://twitter.com/gorillasu/status/1517502897524719616?ref_src=twsrc%5Etfw">April 22, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<p></p>





<h2><a name='multiple'></a>Multiple assignment with an array</h2>

Last but not least, here's another trick to assign multiple different values to several variables. This is sometimes useful when we create variables with the 'var' or 'let' keywords and don't want to declare them with a global scope. We've already seen that we can assign the same value to multiple variables like so:

<pre><code>// assign a value to several variables
var a = b = c = 5
</code></pre>

However what if we want to assign different values to different values, can it be done in a single line? Yes, it can:

<pre><code>// we're going to use an array for this
var [a, b, c] = [1,2,3]

// and now we can simply use these variables by themselves
var d = a + b + c
</code></pre>

This way of assigning values is also a feature of ES6 and is called array destructuring.

<h2><a name='final'></a>Putting it all together</h2>

And finally here's a sketch where I combine a lot of these techniques that we've discussed today:

<blockquote class="twitter-tweet tw-align-center"><p lang="ht" dir="ltr"><a href="https://twitter.com/hashtag/%E3%81%A4%E3%81%B6%E3%82%84%E3%81%8DProcessing?src=hash&amp;ref_src=twsrc%5Etfw">#つぶやきProcessing</a> 😬😵‍💫😁<br>f=0,draw=(a=&gt;{for(f++||createCanvas(w=762,w),background(0,50),noStroke(),p=w/9,N=21,n=N**2,s=(w-2*p)/N;n&gt;0;k=millis()/w+n/3,l=w/6,c=(n=&gt;l+l*n),o=cos(k),fill(c(sin(k)),c(o),c(-o)),ellipse(int(n--/N)*s+p,map(fract(n/N),0,.9,0,20)*s+p,s-3+s*sin(k)));}); <a href="https://t.co/iqQ7W5WQw9">pic.twitter.com/iqQ7W5WQw9</a></p>&mdash; Ahmad Moussa || Gorilla Sun (@gorillasu) <a href="https://twitter.com/gorillasu/status/1523973819924455427?ref_src=twsrc%5Etfw">May 10, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
<p></p>

And here we find ourselves at the bottom of the blog post again! If you've enjoyed the post, share it with your friend, it helps out a lot! Otherwise come say hello on Twitter! Thanks for reading and happy sketching!
