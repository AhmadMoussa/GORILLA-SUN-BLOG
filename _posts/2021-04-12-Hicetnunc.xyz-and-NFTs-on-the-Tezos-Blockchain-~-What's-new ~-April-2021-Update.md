---
title: Hicetnunc.xyz and NFTs on the Tezos Blockchain ~ What's new ~ April 2021 Mega Update
author: Ahmad Moussa
categories:
  - p5js
description: Only a month has passed, however a lot has changed in the Tezos NFT world. Hicetnunc.xyz has become a lot slicker since it's launch roughly 2 months ago.
thumbnail_path: 2021-04-12-hicetnunc.png
published: false
---

This will be a loaded article, so hold on to your NFTs as we dive into all the recent developments of the past month! Here's a quick overview, if you're interested in reading up on something in particular:

1. <a href='#UI'>UI/UX Changes</a>
2. <a href='#Secondary'>The Secondary Market Launches</a>
3. <a href='#Templates'>HTML/P5JS/and other upload Templates</a>
4. <a href='#hDAO'>The hDAO feed</a>
5. <a href='#OBJKT4OBJKT'>OBJKT4OBJKT Event</a>
6. <a href='#Copyminters'>Copyminters and Hoarders</a>
7. <a href='#Kalamint'>A new challenger arrives: Kalamint ~ Punc vs. Boutique</a>
8. <a href='#Tezos'>Tezos to the moon</a>
9. How to price things adequately ~ Quasimondo insights
10. <a href='Presstube'>The presstube method</a>
11. <a href='#Livestream'>Weekly Livestreams</a>
12. Tezos Developer portal
13. Companion sites and art reviews ~ 
14. Other interesting Articles, Resources and Stuff(s)


<h2><a name='UI'>UI/UX Changes</a></h2>
Since it's launch, Hicetnunc.xyz has undergone a number of major UI changes. The minting procedure is still pretty much the same as described in my tutorial <a href='https://gorillasun.de/blog/Getting-started-with-Tezos-and-minting-on-hicetnunc.xyz'>here</a>, however there is no need to sync before every action you do on the website. Once you connect your wallet and grant permission it'll stay synced with your wallet address, and it'll show it to in the top right corner next to the drop down 'hamburger' menu. The sync button still exists however, you simply click the drop down  menu in the top right corner, and where it previously showed your wallet address it'll show an 'unsync' button. Click it and it'll disconnect your current wallet address. 'unsync' becomes 'sync' and clicking the latter will ask you again to grant permission, now you can choose a different wallet address.


<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-12-Hicetnunc.xyz-and-NFTs-on-the-Tezos-Blockchain-~-What's-new ~-April-2021-Update/sync.png" alt="" /></span>

<p>Another major change is that you now get to preview your NFT before minting it. This conveniently allows you to check that everything is right before minting.</p>
<span class="image fit"><img src="https://gorillasun.de/assets/images/2021-04-12-Hicetnunc.xyz-and-NFTs-on-the-Tezos-Blockchain-~-What's-new ~-April-2021-Update/minting_updated.png" alt="" /></span>

Additionally, setting the royalties on your mints is now back. Royalties in this context basically mean that you'll get a percentage share of every subsequent sale of your NFTs. You can set this percentage between 0% and 25%.

<h2><a name='Secondary'>The Secondary Market</a></h2>
<p>On the topic of royalties, you can now not only collect, but also re-sell previously acquired OBJKTs in your collection! This feature was rolled out quietly throughout the month an had some issues at first. The UI worked in such a manner that if someone were to offer up an NFT for sale that they've collected, while the original creator still had remaining editions of said NFT up for sale, they would essentially supersede the original creator with their offer, since the 'click to collect' button would have to be shared. This was problematic, because if someone were to re-sell 1 edition of an NFT for 200tez, whereas the original creator still had dozens of the same NFT up for sale at a much lower price, these would then essentially be blocked until someone bought the NFT offered for 200tez.
  
This button introduced another problem, that caused a number of users quite the frustration. If an NFT were to sell all of it's remaining editions, the button would show 'not for sale'. This, however, didn't mean that the button was not able to be clicked 
  
This has now been fixed by showing all open offers for an NFT in it's collectors tab, with the respective price and the wallet address of the seller.  </p>

<h2><a href='Templates'>HTML/P5JS/and other upload Templates</a></h2>
Another big change has been the support of various data formats including html, svg, p5js, pdf. This was pioneered by a number of Twitter users in parallel, some super insightiful articles by Twitter user @tarwin can be found here. What first started as html encapsulated in an svg file. Now hicetnunc outright supports these formats, and you can find the respective templates for them <a href='https://github.com/hicetnunc2000/hicetnunc/tree/main/templates'>here</a>. You'll have to replace certain parts of the templates with your own code, replace the default thumbnail.png / thumbnail.gif with your own and zip all files. This zip file will be what you upload at mint time. Amazing Right? 

Here's an example of what a minted p5js sketch looks like. Refreshing the page randomizes the sketch
<iframe src="https://www.hicetnunc.xyz/objkt/25767" width="100%" height="1000"></iframe>

Maybe the next cool thing could be a convenient iframe embed code? That would allow creators to display and sell their creations easily from their own websites.

<h2><a name='OBJKT4OBJKT'>OBJKT4OBJKT</a></h2>
On the weekend of the 24th of March, in celebration of 10k minted NFTs on hicetnunc, we've seen an event started by Twitter duo @ameliemaiaa and @tataismo1 that go under the handle @DiverseNftArt, that encouraged users to share NFTs for free, such that users on hicetnunc could build up their collections without a cost. Even though the event is officially over, searching the hashtag #OBJKT4OBJKT on Twitter, you'll probably still be able to find some free NFT goodies. It is also very likely that this event will make a return in the future.

<div style='text-align:center;'>
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">To celebrate 10k <a href="https://twitter.com/hashtag/OBJKT?src=hash&amp;ref_src=twsrc%5Etfw">#OBJKT</a> mints, <a href="https://twitter.com/hicetnunc2000?ref_src=twsrc%5Etfw">@hicetnunc2000</a> and <a href="https://twitter.com/DiverseNftArt?ref_src=twsrc%5Etfw">@DiverseNftArt</a> are promoting a weekend of fun and celebration for all artists and collectors!<br> <a href="https://twitter.com/hashtag/cleanNFT?src=hash&amp;ref_src=twsrc%5Etfw">#cleanNFT</a> <a href="https://twitter.com/hashtag/OBJKT4OBJKT?src=hash&amp;ref_src=twsrc%5Etfw">#OBJKT4OBJKT</a> <a href="https://twitter.com/hashtag/NFTCollector?src=hash&amp;ref_src=twsrc%5Etfw">#NFTCollector</a> <a href="https://twitter.com/hashtag/NFTArt?src=hash&amp;ref_src=twsrc%5Etfw">#NFTArt</a> <a href="https://twitter.com/hashtag/NFTArtists?src=hash&amp;ref_src=twsrc%5Etfw">#NFTArtists</a> <a href="https://twitter.com/hashtag/cryptoart?src=hash&amp;ref_src=twsrc%5Etfw">#cryptoart</a> <a href="https://twitter.com/hashtag/lgbtqia?src=hash&amp;ref_src=twsrc%5Etfw">#lgbtqia</a> <a href="https://twitter.com/hashtag/bipoc?src=hash&amp;ref_src=twsrc%5Etfw">#bipoc</a> <a href="https://twitter.com/hashtag/diverse?src=hash&amp;ref_src=twsrc%5Etfw">#diverse</a> <a href="https://twitter.com/hashtag/artists?src=hash&amp;ref_src=twsrc%5Etfw">#artists</a> <a href="https://twitter.com/hashtag/WomenInTech?src=hash&amp;ref_src=twsrc%5Etfw">#WomenInTech</a> <a href="https://twitter.com/hashtag/Tezos?src=hash&amp;ref_src=twsrc%5Etfw">#Tezos</a> <a href="https://twitter.com/hashtag/XTZ?src=hash&amp;ref_src=twsrc%5Etfw">#XTZ</a> <a href="https://t.co/1iBeju6Ltw">pic.twitter.com/1iBeju6Ltw</a></p>&mdash; Diverse NFT Artists 🦄 (@DiverseNftArt) <a href="https://twitter.com/DiverseNftArt/status/1374751025219899393?ref_src=twsrc%5Etfw">March 24, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
  </div>

<h2><a name='Copyminters'>Copyminters and Hoarders</a></h2>
A problem that arose with the OBJKT4OBJKT event was that some individuals would collect multiples of certain free OBJKTs, while not being illegal, it is clearly a dick move. Clearly the main purpose is the later resale of the claimed NFTs. 

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Just realized that one of the guys shamelessly snatching dozens of copies of the free <a href="https://twitter.com/hashtag/OBJKT4OBJKT?src=hash&amp;ref_src=twsrc%5Etfw">#OBJKT4OBJKT</a> pieces was @sirboomcat aka tz1MHVtpYFsW7VDH1rhbhK3Krad14n6Ww8RD.<br><br>The hint is his own NFT, <a href="https://t.co/pZCSOvGdPg">https://t.co/pZCSOvGdPg</a>, which he minted for this reply here: <a href="https://t.co/UQOmiyxl5o">https://t.co/UQOmiyxl5o</a><br><br>1/2 <a href="https://t.co/DsEBrBLSA2">pic.twitter.com/DsEBrBLSA2</a></p>&mdash; Django BRRRRiꜩ 🌮 (@djangobits) <a href="https://twitter.com/djangobits/status/1376278372712595464?ref_src=twsrc%5Etfw">March 28, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Copyminting is another huge issue, where someone mints an NFT/piece/artwork that is not originally theirs. Some solutions to this problem are being discussed <a href='https://github.com/hicetnunc2000/hicetnunc/issues/433'>here</a>.

<h2><a href='hDAO'>hDAO and the hDAO feed</a></h2>
You might've noticed already that every NFT on hicetnunc has a little circle underneath it, in the lower right corner. The same circle can be found on the NFT page itself, underneath the OBJKT number, and also in the drop down menu. This circle isn't just some aesthetical UI fluff but it can actually be clicked. Clicking it underneath an NFT will trigger your Tezos wallet, clicking it in the hamburger menu takes you to the hDAO feed.


<h2><a name='Presstube'>The 'Presstube' method</a></h2>
The 'Presstube' method is essentially a specific way of pricing NFTs on hicetnunc, which rewards the early birds that swoop in and cop the first couple of editions of a freshly minted NFT. When you mint an NFT on hicetnunc, it appears in the store, however it is not immediately up for sale.

<h2><a name='Kalamint'>A new challenger arrives: Kalamint ~ Punc vs. Boutique</a></h2>

<h2><a name='Tezos'>Tezos to the Moon</a></h2>
The NFT activity on the Tezos blockchain has catapulted the number of contract calls, surpassing previous all-time highs. Twitter user @Esclaponr shows some stats he has aggregated:
<div style='width: 100%'>
<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Largest spike in new users on <a href="https://twitter.com/hicetnunc2000?ref_src=twsrc%5Etfw">@hicetnunc2000</a> since I started collecting some data about the smart contract usage two days ago <a href="https://t.co/jQVBEmqh8h">pic.twitter.com/jQVBEmqh8h</a></p>&mdash; Ricky Esclapon (@Esclaponr) <a href="https://twitter.com/Esclaponr/status/1374910647956344834?ref_src=twsrc%5Etfw">March 25, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
  </div>

<h2><a href='Livestream'>Weekly Livestreams</a></h2>
1. https://twitter.com/verticalcrypto
2. https://twitter.com/TezosCommons

<h2><a href='Companion'>Companion sites and art reviews</a></h2>
1. Poptwig https://medium.com/poptwig
2. Discovery Tool https://projects.stroep.nl/hicetnunc/#discover
3. https://here-or-there.glitch.me/


<h2><a href='Other'>Other Interesting Articles, Resources and Stuff(s)</a></h2>
1. https://www.plummerfernandez.com/works/not-another-jpeg/
2. https://www.salon.com/2021/04/08/why-would-anyone-buy-crypto-art--let-alone-spend-millions-on-whats-essentially-a-link-to-a-jpeg_partner/
3. Hosting your own IPFS node https://twitter.com/antic/status/1374417104489697283


<h2>Adding your info to tezos blockchain</h2>
https://twitter.com/quasimondo/status/1375788135171428356
