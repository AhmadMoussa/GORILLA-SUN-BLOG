---
title: How to set up a Breadfond
author: Ahmad Moussa
categories:
  - p5js
description: In this tutorial I will guide you through setting up a breadfond on the Tezos blockchain using the TEIA and/or Versum smart contracts.
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: true
exclude_rss: true
---



This time around I've got a very special topic! One that I'm incredibly excited about and have discussed at length with some lovely fellow Artists and Tezos people.


<h2>A bit of history</h2>

I think, to begin it's probably best to give a little context. Everything started with this tweet from <a href='https://twitter.com/micalnoelson'>Mical Noelson</a>:

https://twitter.com/micalnoelson/status/1502562539699376131

Afterwards I retweeted his tweet because I thought that it was a formidable idea! <a href='https://twitter.com/aebrer'>Andrew</a> quickly hopped onto the bandwagon in the replies and pointed out that this would be possible via the TEIA collab contracts.

https://twitter.com/gorillasu/status/1502632630742884353

We discussed it a bit more, and Andrew quickly did a little test if it was possible:

https://twitter.com/aebrer/status/1502725134184071173

And it worked out perfectly:

https://twitter.com/aebrer/status/1502728290146136068



<h2>What's a Breadfond?</h2>

The idea is derived from a concept originating in the Netherlands, namely: Broodfonds. In eglish 'broodfond' translates to breadfund. The term basically defines a group of people that band together to provide and enable each other with temporary sick leave: https://en.wikipedia.org/wiki/Broodfonds

In general, this means that when one member falls sick, they will receive donations (or support in other forms) from the other members of this support system, until they get back on their feet. In general, this means that there is no single "correct" way this collective has to behave, it all depends on the participating members and the dynamic that is agreed upon.

The term “Breadfond” is a mash up of the original dutch term and the english term, and what we rolled with to indicate our version of the concept.




<h2>Translating this concept to Tezos</h2>


The Breadfond collective is a Tezos version of this concept! Essentially, we will make use of the tools available on the Tezos blockchain to set up a similar support system. More specifically, this will be done by using  the collab and split contracts that currently exist on the TEIA and Versum NFT marketplaces. These contracts have been around for a while, but have not lived up to their full potential so far since they allow for some interesting collaborative setups.

The core idea: each member of a Breadfond voluntarily relinquishes a small portion of the revenue that they generated from the sale of NFTs, and has it automatically sent to a split/collab contract. This contract in turn will automatically and evenly distribute that amount among all members that belong to this particular Breadfond. We'll discuss how to set this up in the next section.

This relinquished percentage can be as minuscule as 1% ~ 3%. This is still to be discussed and agreed upon, among others. This is basically what this Discord is for.

Each user then will have a unique personal Breadfond, essentially a list of people they choose to support for whatever reason. The Breadfond collective is simply the group of people made up of all the people using a Breadfond. We've made a <a href='https://discord.gg/nbpfU8P6XW'>discord</a> for everyone that would like to find a breadfond to be part of, create a new breadfond or simply further the discussion.


<h2>Setting up a Breadfond on TEIA</h2>

In this section we'll go over how a Breadfond can be set up via the TEIA collab contract. First off, you'll have to make sure that you're synced on teia with one of your wallets.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/1.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/2.png" alt="">
</span>

A quick note here, while creating the breadfond, it doesn't really matter from which one of your wallets you create it, this collab will only act as an intermediary that will split whatever it receives in tez with all the beneficiaries that you added to it. You'll have to decide if you want to have your breadfond collabs linked to your main wallet, or linked to a separate one that holds all your collab contracts (or a mix of both).

Now, go and click on the hamburger menu in the top right corner and you should be met with a menu as such:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/3.png" alt="">
</span>

Now click on collaborate and you'll be taken to a new page that might have some entries already if you've collaborated with someone before, or alternatively is empty if you haven't done so yet:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/4.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/6.png" alt="">
</span>

For now, we'll shift our attention towards the 'create' tab and click on it. You should see something like this:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/7.png" alt="">
</span>

We can skip this for now, and proceed to the next screen that allows you to add beneficiaries:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/8.png" alt="">
</span>

Here we will click on "add address manually" and add the wallet addresses that we would like to belong to our Breadfond. You'll see something that says "[object Object]" which you can safely clear and replace with the address you'd like to add:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/9.png" alt="">
</span>

In the shares field ideally you would put the number '1', which means that this beneficiary will receive exactly one share of the tez inbound to this collab address.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/10.png" alt="">
</span>

We can add arbitrarily many beneficiary addresses to the collab, each of which will be set to receive one share.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/11.png" alt="">
</span>

To exemplify, I'll add in some of my own wallet addresses so that we can proceed:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond_tutorial/11.png" alt="">
</span>

<h2>Setting up the top level Collab</h2>


<h2>Things to keep in mind</h2>




<div class="row gtr-50 gtr-uniform">
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/breadfond/50.png" alt="">
		</span>
	</div>
	<div class="col-6">
		<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
			<img class="viewable" src="https://gorillasun.de/assets/images/breadfond/27.png" alt="">
		</span>
	</div>
