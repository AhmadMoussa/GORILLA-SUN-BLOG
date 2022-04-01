---
title: How to set up a Breadfond
author: Ahmad Moussa
categories:
  - p5js
description: In this tutorial I will guide you through setting up a breadfond on the Tezos blockchain using the TEIA and/or Versum smart contracts.
thumbnail_path: https://gorillasun.de/assets/images/hexagons/spiralgrid.mp4
published: false
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
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/1.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/2.png" alt="">
</span>

A quick note here, while creating the breadfond, it doesn't really matter from which one of your wallets you create it, this collab will only act as an intermediary that will split whatever it receives in tez with all the beneficiaries that you added to it. You'll have to decide if you want to have your breadfond collabs linked to your main wallet, or linked to a separate one that holds all your collab contracts (or a mix of both).

Now, go and click on the hamburger menu in the top right corner and you should be met with a menu as such:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/3.png" alt="">
</span>

Now click on collaborate and you'll be taken to a new page that might have some entries already if you've collaborated with someone before, or alternatively is empty if you haven't done so yet:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/4.png" alt="">
</span>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/6.png" alt="">
</span>

For now, we'll shift our attention towards the 'create' tab and click on it. You should see something like this:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/7.png" alt="">
</span>

We can skip this for now, and proceed to the next screen that allows you to add beneficiaries:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/8.png" alt="">
</span>

Here we will click on "add address manually" and add the wallet addresses that we would like to belong to our Breadfond. You'll see something that says "[object Object]" which you can safely clear and replace with the address you'd like to add:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/9.png" alt="">
</span>

In the shares field ideally you would put the number '1', which means that this beneficiary will receive exactly one share of the tez inbound to this collab address.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/10.png" alt="">
</span>

We can add arbitrarily many beneficiary addresses to the collab, each of which will be set to receive one share.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/11.png" alt="">
</span>

To exemplify, I'll add in some of my own wallet addresses so that we can proceed:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/12.png" alt="">
</span>

After we've added all of our beneficiary addresses we'll be taken to a review screen where it will show us how the percentages will be split between beneficiaries:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/13.png" alt="">
</span>

Now we can hit "create collaborative contract" and verify with our wallet. It's important to note that the gas fees for creating this contract are pretty high at this point (~5$) which is not too bad since we only have to do this step two times in total (once now, and a second time to set up the top level contract):

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/14.png" alt="">
</span>

After confirming the transaction we'll be taken back to our overview of collab contracts and we should be seeing a new entry that has a rather lengthy name. In this view you can choose into which collab you want to sign in. Once you're signed into one of your collabs you can click again on the hamburger menu and click on profile. If you're signed in to a collab you will be able to change the profile information of that collab.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/15.png" alt="">
</span>

If you want you can update this information now. And this essentially concludes this part of the tutorial. One last thing is that you should grab the address of this contract and save it somewhere, we're gonna need it in a second!

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/16.png" alt="">
</span>

You can find the address by clicking "more detail" in the manage view.


<h2>Setting up the top level Collab on TEIA</h2>

In this part we're largely going to repeat the same steps that we've taken before, with some slight, but important differences. First, let's make sure that we are synced with the correct wallet that we want to create this collab contract from. This is important because we are going to mint our NFT from this wallet address. We'll repeat the same steps as before, but after arriving at the create tab of the collab contract, we're gonna want to add a core collaborator this time, instead of just skipping. And this core collaborator will be none other than ourselves, we will add our own wallet address (the one with which we are synced right now) into one of the core collaborator fields:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/17.png" alt="">
</span>

Then in the beneficiary field we'll input the address of the Breadfond contract we created just a second ago:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/18.png" alt="">
</span>

And then we proceed to the review screen again. Note that you can add a different split of shares, depending on how large a chunk of your sales you want to go towards the breadfond!

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/breadfond/19.png" alt="">
</span>

Now you can return to your "Manage" view, sign in and edit your profile information. And that's about it regarding setup, now we're ready to mint an NFT!

<h2>Minting an NFT with the collab contract</h2>

Minting an NFT from the collab contract is relatively straightforward. We proceed as usual, the only difference here would be that we need to sign the NFT before we can list it.


<h2>Things to keep in mind</h2>

This might not be the optimal way to do things


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
