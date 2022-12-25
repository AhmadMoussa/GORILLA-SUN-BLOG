---
layout: default
title: "An Introduction to Javascript Generators"
author: Ahmad Moussa
categories:
  - blog
description: Generators are a versatile feature of ES6 and allow for interesting programming strategies, that otherwise wouldn't be possible. In this post we'll go over the basics and some use cases of JS generators.
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/generators/banner.png
published: true
exclude_rss: false
legacy: false
listed: true
---

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/generators/banner.png" alt="">
</span>
<p></p>

Generators are a bit of an obscure feature of javascript, and their usefulness might not be immediately evident. This article aims at explaining how they work and what they can be used for! We’ll first have a look at the syntax and how generators are typically implemented, followed by some use cases where generators come in really handy! We'll also briefly dip our toes into the concept of iteration, iterators and iterables. Lastly, we'll also have a look at the lazy evaluation programming paradigm and how generators are one way to implement it!

<h3>Quick Index:</h3>

1. <a href='#1'>A Tale of Iterators and Iterables</a>
2. <a href='#2'>Generators: Iterators in Disguise!</a>
3. <a href='#3'>Generators as Iterables</a>
4. <a href='#4'>Creating custom Iterables with Generators</a>
5. <a href='#5'>Generators and Lazy Evaluation</a>
6. <a href='#6'>Infinite Sequences</a>
7. <a href='#7'>Closing Notes</a>



<h2><a name='1'></a>A Tale of Iterators and Iterables</h2>

Generators are essentially iterators in disguise. Let me explain what that means! An iterator can be best explained as a "protocol that defines how to produce a sequence of values from an object". That's a bit of a mouthful, and it's probably best to directly have a look at an example. You're probably familiar with the <b>for...of</b> loop statement that was introduced in ES6 (not to be confused with the <b>for...in</b>):

<pre><code>let nums = [1, 2, 3, 4, 5]

for(let n of nums){
  console.log(n)
}
// 1, 2, 3, 4, 5
</code></pre>

It's essentially an alternative to the regular old school imperative for loop that we're used to, where we manually have to access the elements of the array we're looping over and requires us to keep track of the index. The <b>for...of</b> loop takes care of all of that for us, we just need to pass it the array taht we want it to loop over and it'll directly serve us the elements of that array without having to access them by index (as opposed to the <b>for...in</b> loop statement for example). Fun fact, the <b>for...of</b> loop can also be used for other objects such as sets and strings (and more)! Neat, right?.

The object that is passed to a <b>for...of</b> loop usually needs to be an <b>iterable</b>. The important thing to understand here, is that the <b>for...of</b> loop doesn't actually loop over the object by itself, but makes use of an <b>iterator</b> function that is attached to the iterable. The <b>for...of</b> loop simply invokes that iterator function and makes use of it to generate the correct sequence of items. This means that arrays, sets, strings, etc. already have such an iterator function built into them that predicate in what sequence the <b>for...of</b> loop will go over the elements. In <a href='https://exploringjs.com/es6.html'>exploring ES6</a>, Dr. Axel Rauschmayer gives a good summary of what iterables and iterators are:

<blockquote>
An iterable is a data structure that wants to make its elements accessible to the public. It does so by implementing a method whose key is <b>Symbol.iterator</b>. That method is a factory for iterators.
</blockquote>

In essence, iterables are sequences of data that are intended to be traversed and consumed, and at the same time hold their own iterator function stored inside an object property with the identifier <b>Symbol.iterator</b>. It describes what the sequence of items should look like when it's being looped over. What javascript Symbols are is widely outside the range of this article (you can learn more about Symbols <a href='https://www.javascripttutorial.net/es6/symbol/'>here</a>), and isn't really of much concern for the purposes of this article, we simply need to implement this function to make an object iterable. More on that later.

<blockquote>An iterator is a pointer for traversing the elements of a data structure (think cursors in databases).</blockquote>

Basically, iterators are procedures that describe how the iterable sequence is being traversed. A more formal definition for an iterator can be found on MDN, it essentially is an object that satisfies two conditions:

<blockquote>In JavaScript an iterator is an object which defines a sequence and potentially a return value upon its termination. Specifically, an iterator is any object which implements the Iterator protocol by having a next() method […] — MDN</blockquote>

We're gonna see what all of this means in a hands on manner throughout the next two sections! Additionally, as a matter of fact, all iterators are actually also iterables, because technically, they can be iterated over by another iterator. However, not all iterables are iterators. This is probably very confusing at this point, hang on though, it'll get a little clearer in the coming sections. I hope I have piqued your interest at this point, and you're curious to know how generators fit into the overall picture of iteration!

<h2><a name='2'></a>Generators: Iterators in Disguise!</h2>

A javascript generator is a special type of synchronous function where execution can be manually halted and resumed at any given point, all the while remembering where we halted execution. Regular functions don’t have this ability, once invoked they will run until fully executed or a return statement is encountered.

To best illustrate this, let’s start by having a look at the syntax! To tell javascript that a function should be a generator, we simply follow the <b>function</b> keyword by an asterisk symbol (<b>*</b>). And that's that, how this syntax came to be from a historical point of view is a bit hazy. Here's an initial minimal example:

<pre><code>function* generator(){
  yield;
}
</code></pre>

Our next point of interest is the <b>yield</b> keyword that you can see in the body of the generator's function. Think of it as the return keyword’s distant cousin: it is also used to get a value out of the generator function and returns it when the generator is invoked, however, unlike the return keyword, it doesn’t terminate the function instantaneously but rather just pauses it’s execution. The next time the generator is invoked again we will resume execution starting from the last <b>yield</b> keyword. This essentially means that the <b>yield</b> keyword can occur more than once throughout the generator’s body:

<pre><code>function* generator(){
  yield 1;
  yield 2;
  yield 3;
}

console.log(generator().next().value) // 1
console.log(generator().next().value) // 2
console.log(generator().next().value) // 3
console.log(generator().next().value) // undefined
</code></pre>

You probably also noticed that we can’t simply invoke the generator like a regular function. Calling the generator function like a regular one would actually not do anything. To actually get a value out of it we need to use the <b>next()</b> method. Invoking this method on the generator function we will obtain a little object with two properties <b>value</b> and <b>done</b>, where the former is simply the value that we are returning with the <b>yield</b> statement, and the latter is a boolean indicating if the next .next() call will return a value or undefined. You will get a generator object with a ‘value: undefined’ when there are no more yield statements to consume.

<pre><code>console.log(generator().next()) // {done: false, value: val}</code></pre>

If your generator accepts input parameters, these have to be passed through the <b>next()</b> method as well. In this manner, generator functions are iterators as the satisfy the two conditions that we mentioned earlier:

1. Generators are objects that implement a <b>next()</b> function. Printing out the generator object itself with <b>console.log(generator)</b> you'll see that it has a property called <b>next</b> that holds a function.
2. A generator has a return value on when it terminates. This happens when all yield statements are exhausted, and even holds true when there are no yield statements in the body of the generator function.

In the coming section we'll have a crack at creating our own custom iterator functions for our objects! Generators will come in very handy for that!

<h2><a name='3'></a>Generators as Iterables!</h2>

At the same time, generators also act as iterables! For example, we can simply pass our generator to a <b>for...of</b> loop to extract values from it:

<pre><code>function* generator(){
  yield 1;
  yield 2;
  yield 3;
}

for(let val of generator()){
  console.log(val)
} // 1, 2, 3
</code></pre>

We can also do all the other stuff that we would normally be able to do with iterables, like extracting the entire sequence with a spread operator:

<pre><code>let seq = […generator()]
</code></pre>

This implies that we can pass one generator (as an iterable) to another generator (as an iterator):

<pre><code>function* iterable(){
  yield 1;
  yield 2;
  yield 3;
}

function* iterator(iterable){
  for(let val of iterable){
    yield val
  }
}

let a = [...iterator(iterable())]
console.log(a) // [1, 2, 3]
</code></pre>

This can get a bit confusing and it's probably a bit difficult to see how this is useful, but later we'll get into an interesting use case, where we can plug these generators together in a sort of modular manner and achieve lazy evaluation of values. However, let's first have a look at how to create custom iterator functions for our objects!


<h2><a name='4'></a>Creating custom Iterables with Generators</h2>

Some objects in javascript already have iterators built-in, simply allowing us to pass them to a <b>for...of</b> loop. But what if we create a custom object, how can we turn it into an iterable that we can pass to a <b>for...of</b> loop? In this case we would have to define our own iterator and attach it to the object under the <b>Symbol.iterator</b> property that we've already discussed earlier. To demonstrate this, I'll borrow an example from an amazing talk by <a href='https://twitter.com/AnjanaVakil'>Anjana Vakil</a>: <a href='https://www.youtube.com/watch?v=gu3FfmgkwUc'>The Power of JS Generators</a>. Let's assume we have the following object:

<pre><code>let cardDeck = {
  suits: ['♥️', '♦️', '♠️', '♣️'],
  court: ['J', 'Q', 'K', 'A']
}
</code></pre>

The expected behaviour here, when passing it to a <b>for...of<b> loop, would be to loop over the all of the court + suit combinations in addition to the pip cards (the cards with numbers on them). Hence 52 distinct pairs in total, and we want to do this without manually having to type all of them out. Building our iterator object from scratch, we need to follow the two conditions:

<ul>
<li>We need to implement a <b>next()</b> function</li>
<li>We need to return an object of the form <b>{done: true|false, value: val}<b></li>
</ul>

Typically this is implemented in the following manner:

<pre><code>[Symbol.iterator] = function(){
  return {
    next: () => {
      done: true,
      value: 'hello'
    }
  }
}
</code></pre>

A little bit difficult to digest on first glance. The function returns an object that holds as property the <b>next()</b> function. In turn, the <b>next()</b> function is what returns the iterator object which holds the <b>done</b> and <b>value</b> properties. This would already work if we were to pass it to a <b>for...of</b> loop, but is still a bit primitive in its current state. Here we defaulted the done value to true, because having it set to false would be problematic if we fed it to a <b>for...of</b> loop (it would loop indefinitely). We've got to add some more stuff to make this work as intended:

<pre><code>let cardDeck = {
  suits: ['♥️', '♦️', '♠️', '♣️'],
  court: ['J', 'Q', 'K', 'A'],

  [Symbol.iterator]: function(){

    // indices that keep track of our position in the two arrays
    let suitsIdx = -1;
    let courtIdx = 0;

    return {
      next: () => {
        // simulating a nested loop here: courtIdx is only incremented
        // whenever suitsIdx has made a complete pass over the suits array
        if (suitsIdx >= this.suits.length - 1) {
          suitsIdx = 0;
          courtIdx++
        } else {
          suitsIdx++;
        }

        // when courtIdx has made a complete path, we are done
        if (courtIdx > this.court.length - 1) {
          return {
            value: undefined,
            done: true,
          };
        }

        // If the previous condition didn't trigger, we return the iterator object with the current suit/court combinations
        return {
          done: false,
          value: this.suits[suitsIdx] + this.court[courtIdx]
        }
      }
    }
  }
}
</code></pre>

Now passing this to a <b>for...of</b> loop we would have successfully generated all of the possible suit/court combinations, we would still need to work on it to generate all of the 52 combinations. Try changing the code to make it also produce the numerical cards, it's a bit of a hassle... Imagine if you had more complicated objects for which you would want to generate certain sets of combinations! The problem of adding your own iterator function to your custom objects can be made much more tractable when using generators! Let's do the same thing as we just did, with the simple difference that this time around we'll use a generator as a middle man:

<pre><code>let cardDeck = {
  suits: ['♥️', '♦️', '♠️', '♣️'],
  court: ['J', 'Q', 'K', 'A'],

  [Symbol.iterator]: function* (){
    for(let suit of this.suits){
      for(let i = 2; i <= 10; i++) yield suit + i;
      for(let c of this.court) yield suit + c;
    }
  }
}
</code></pre>

See how much more compact this has become?! We didn't have to deal with keeping track of any index, the syntax is a lot cleaner, much more readable, and it implicitly took care of returning the iterator object. Essentially, we mediated between iterability and our custom object via means of a generator! By adding the <b>Symbol.iterator</b> function we've provided a means for iterators to consume our iterable.

Moral of the story is: the next time that you want to turn one of your objects into a custom iterable, try to use a generator to make things easier!


<h2><a name='5'></a>Generators and Lazy Evaluation</h2>

All of what precedes already makes generators incredibly versatile tools, however Javascript generators really shine when it comes to lazy evaluation (also sometimes referred to as call-by-need). Here it might be useful to spend a moment and talk about what that actually means. In essence it’s a programming paradigm where the computation of values and/or evaluation of statements is delayed until is absolutely necessary.

During my master studies I incidentally used generators on a daily basis, where I was working on machine learning models and training neural networks. It is often the case that machine learning datasets are quite large (ranging in the hundreds of Gigabytes), especially when you're dealing with vision models that need to be trained on image files for example. From a memory perspective, training these models can be quite challenging, even when you're working with a super high end computer. It is impossible to fit such datasets into memory. It would be much more convenient to be able to fetch just a couple of training samples at a time, feed them to the neural network, do a training pass, then discard those and fetch a new batch of samples and repeat the previous steps. And well, I think you know what I'm trying to imply.

Generators allow us to do this with ease, and are much less clunky to work with than for loops in this scenario, often it the case that we want to fetch a small batch of data samples, apply some pre-processing steps to it, pass it to the neural network, do a training pass, and then every so many iterations we'd also want to evaluate how well our model is doing, and ultimately start over again and repeat these steps. Generators just make this interleaved and sequenced way of writing code super easy, as they will always wait for us when we suspend them and remember where we'e left off as well.

This is however just one example of this lazy, on demand evaluation type of programming. A great resource on this lazy iteration, is James Sinclair's article <a href='https://jrsinclair.com/articles/2022/why-would-anyone-need-javascript-generator-functions/'>Why would anyone need javascript generator functions?</a> where you will incidentally also learn a lot about Australian culture and what a Tim Tam Slam is. Let's have a look at a concrete example, say we have an array of numbers:

<pre><code>let nums = [0, 1, 2, 3, 4]
</code></pre>

And say, we'd like to apply two operations on each element in this array, firstly add the previous value (when it exists) to the current value, and then square the result. We can attempt this with the inbuilt javascript array methods, like for example .map():

<pre><code>let offsetAndSquared = nums.map((curr, n, arr) => curr+=arr[n-1] ? arr[n-1] : 0).map(curr => curr**2)

console.log(offsetAndSquared) // Result: [0, 1, 9, 25, 49] / Expected: [0, 1, 5, 13, 25]
</code></pre>

However this is not the intended effect, we would actually like that both actions happen in sequence on each element before they are applied to the next element. Although we could do this with an old school imperative for loop... Generators to the rescue! For this we'll create a little number generator that can give us a sequence of N numbers as an iterable:

<pre><code>function* number(){
  let i = 0
  while(true){
    yield i++
  }
}
</code></pre>


Next we'll create our own version of the map array method, but with the twist that it's a generator:

<pre><code>function* mapGen(iterable, mapFn){
  for(let item of iterable){
    yield mapFn(item)
  }
}
</code></pre>

And we'll also create a generator which remembers and adds the previous value if there is one:

<pre><code>function* addPrev(iterable){
  let prevItem = undefined;
  for(let item of iterable){
    yield (prevItem) ? item + prevItem : item;
    prevItem = item
  }
}
</code></pre>


Last but not least, we also need a function that can extract a N values from our starting iterable (the number generator), we'll call that function <b>take</b>, as in taking from the iterable, and which will act as the main iterator that starts our iterable domino chain:

<pre><code>function* take(n, iterable){
  for(let item of iterable){
    if(n <= 0) return;
    n--;
    yield item;
  }
}
</code></pre>

Now we would put together our little iterable daisy chain in this manner:

<pre><code>let offsetAndSquaredGen = [...take(5, addPrev(mapGen(numberGenerator, n => n**2)))] // [0, 1, 5, 13, 25]
</code></pre>

This is really cool, because technically there is no limit to how many generators we can chain together, and essentially obtain new and interesting sequences in this modular approach! Could be cool for generative art purposes?


<h2><a name='6'></a>Infinite Sequence Generators</h2>

If you've noticed, we've had a <b>while(true)</b> in our number generator, which is in any other scenario quite a scary statement, but thanks to the yield keyword we can rest assured that it won't run infinitely (except if we pass it to a for...of loop, but at that point it really is our fault). In essence, we've created an iterable that defines an infinite range, which could be potentially useful in certain situations, for instance when we don't know how many items/values we need in advance, we can just write a generator that defines the sequence and generate these values on demand. An example of an infinite sequence would be the fibonacci numbers:

<pre><code>function* () {
  let prev = 0;
  let curr = 1;
  while (true) {
    yield curr;
    const tmp = prev;
    prev = curr;
    curr = curr + tmp;
  }
}
</code></pre>

Now we could also recreate the usual array methods as generator equivalents, like we've already done earlier and act upon this infinite sequence in a lazy manner! This might come in handy one day.

<h2><a name='7'></a>Closing Notes</h2>

I think we've covered all of the basics of generators in this article! They are a very powerful feature of Javascript which allows for a completely different approach for certain types of problems and otherwise might just be fun to play around with. One thing that needs to be pointed out here is that generators are <a href='https://caniuse.com/es6-generators'>not supported on some older browsers</a>, so make sure you account for that if you use them in your projects.

If there's any more things that you think should be included, let me know in the comments down below, and if you have any questions or need any clarifications also leave me a comment. Otherwise, thanks for reading! If this was useful to you, share it with a friend! Or check out some of my other articles! It helps out a lot! Cheers, happy holidays, merry christmas, a happy new year and happy coding!

Here's some further reading material:

1. <a href='https://www.youtube.com/watch?v=gu3FfmgkwUc'>The Power of JS Generators</a> by Anjana Vakil
2. <a href='https://jrsinclair.com/articles/2022/why-would-anyone-need-javascript-generator-functions/'>Why would anyone need javascript generator functions</a> by James Sinclair
3. <a href='https://medium.com/hackernoon/infinite-data-structures-in-javascript-eb67ecbccdb'>Infinite Data Structures in Javascript</a> by Francis Stokes
4. <a href='https://www.javascripttutorial.net/es6/symbol/'>The Ultimate Guide to JavaScript Symbols</a>
