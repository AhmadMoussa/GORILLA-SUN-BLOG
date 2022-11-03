---
layout: default
title: "Getting started with Mastodon"
author: Ahmad Moussa
categories:
  - blog
description: Mastodon is where all the cool kids are right now. If you've been meaning to join the Twitter alternative and don't know where to start, then this post got you covered. We'll go over how to sign up to Mastodon and how everything on the platform works.
thumbtype: img
thumbnail_path: https://gorillasun.de/assets/images/Mastodon/thumb.png
published: true
exclude_rss: false
legacy: false
listed: true
---

If you’re a frequent Twitter user and are aware of recent events then you might have also had thoughts about starting an account on an alternative social media outlet. One popular alternative that you've might seen trending on Twitter is Mastodon! I've tried it for a couple of days now, and it's been a great way to escape all of the Twitter drama for a little bit. In this post I'll summarize everything that I've learned so far and help you get started with Mastodon.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/banner.png" alt="">
</span>
<p></p>

Mastodon is inherently quite a bit different from Twitter, as it's built on top of a completely different mechanism. So there's a few things that you need to be aware of to use Mastodon effectively. Social media platforms have always felt like min-max games to me; it takes a while to get good at them, but once you've got a feel for how they work it's smooth sailing. Mastodon has a slightly steeper learning curve than Twitter, but is at the same time much more flexible, in my experience at least. Here's a quick index:

<h3>The Basics:</h3>

1. <a href='#1'>What is Mastodon</a>
2. <a href='#2'>Signing up to Mastodon</a>
3. <a href='#3'>Tooting (Tweeting) on Mastodon</a>
4. <a href='#4'>The different Timelines</a>
5. <a href='#5'>The advanced web Interface</a>
6. <a href='#6'>Concluding Thoughts: Mastodon vs. Twitter</a>
7. <a href='#7'>More Reading Material</a>


<h2><a name='1'></a>What is Mastodon</h2>

Mastodon describes itself as a decentralised open source microblogging platform. Moreover it isn't really a social media networking website, but rather a software that allows you to host your very own. Oof, that is already a lot to digest in two sentences... let's break that down. Superficially, Mastodon is very similar to Twitter, you create an account, post short texts and images to a timeline, follow other users and interact with their posts. Conceptually however, there are quite a few differences that we're gonna tackle throughout the sections of this post.

The emphasis here is on the words 'decentralised' and 'open source'. Mastodon isn't run by a single entity, people can spin up and host their own individual Mastodon ‘instances’ with their own set of rules. When you create an account on Twitter, you're logging into one shared network with all other Twitter users, on Mastodon your account exists on a specific server, which in turn exists within a larger network of servers that make up the entirety of the Mastodon platform. Any one of these servers could be an entry point for you, in the next section we'll talk about how to choose a specific Mastodon instance that suits your needs. So in other words, your account lives in a specific independently run Mastodon instance, which in turn exist in a larger cluster of many other such servers.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/federation animation.gif" alt="">
</span>
<p></p>

This galaxy of different instances is also called the 'Fediverse' or the 'Federation', and not only encompasses Mastodon servers, but also other identities that support a communication protocol that follows the open standard. Now these different instances aren't isolated from each other, the great thing is that you can still interact with any other person on any other server. This has it's own pros and cons which we'll go into at a later point in this post.

One important thing to add here is that different instances aren't automatically connected when they're created. Two instances become 'federated' after there is some form of interaction between the two for the first time, for example your instance (instance A) becomes federated with another instance (instance B) if you start following a user on that instance. Furthermore, instance federation is a propagative process, let's say that the user, that you're following on instance B, starts following another user on a separate third instance (instance C). Instance C would now also become federated with instance A, and so on. This might be the most complicated aspect of Mastodon to wrap your head around, but now that i've set the scene with a very rough general overview of what Mastodon is and how it works, let's dive into some of the details.

<h3>Key Points:</h3>

1. Mastodon is a decentralised conglomerate of many independently hosted instances.
2. This conglomerate is also known as the Fediverse, and encompasses all services that are based on communication protocols that use the open standard.
3. Instances are interconnected: users on one instance can communicate with users in other instances.
4. Instances become 'federated' (connected) after an initial first interaction. This process in propagative.

<h2><a name='2'></a>Signing up to Mastodon</h2>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/welcome.png" alt="">
</span>
<p></p>

Joining Mastodon means joining one of the instances that exist in the Fediverse, and choosing which one to join can be a little bit tricky. As we've already discussed, in the larger scheme of things it probably doesn't matter that much, because ultimately you will still be able to interact with the rest of the Mastodon users in the Fediverse. However some things that you should consider before choosing a server:

1. The server you sign up to determines your overall Mastodon handle
2. Different Mastodon instances are run by different individuals and/or organisations, in this manner each instance has its own set of guidelines and rules.
3. The server you choose partially determines which content you'll see on your timeline

<h3>The server you sign up to determines your overall Mastodon handle</h3>
We'll talk about handles a bit more later on when we talk about how to mention people on Mastodon, but for now just know that handles on Mastodon are composed from two parts; your chosen username in addition to the name of the instance that you signed up to, both prefixed with the @ symbol. It would look something like this <b>@username@instancename</b>. In many ways that's very similar to email addresses. For example if you're an artist you probably would preferably join an instance that overall has something to do with art like the mastodon.art instance, then your handle would be <b>@artistname@mastodon.art</b>. You probably would want a handle that reflects your social media presence and the type of content that you're intending to share. One last thing here, is that if your username of choice is already taken on a specific server, then you might be able to get it on a different server.

<h3>Different Mastodon servers have different guidelines and content policies</h3>
Now the next thing is checking out the servers rules and guidelines which can generally be found by typing in <b>instancename/about</b> into your browsers' search bar. Let's go with the same example again, let's say you're an artist and mainly use social media to promote physical products like prints of your art, some servers might not allow that or might require you to put a content warning on posts that promote merch. For example one of mastodon.art's server rules forbids any content related to NFTs, if that's something you're active in then mastodon.art might not be the right fit for you:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/rules.png" alt="">
</span>
<p></p>

This means that you're free to choose a server with a content policy that you agree with and an admin that you think is capable.


<h3>Your server partially determines what content you get to see</h3>

Lastly, the instance you join also partially determines what content you'll see. Choosing a server that is relevant to the niche you're active in might make it easier to discover content that you find interesting as well as people that you'll potentially want to interact with (the local server timeline). Additionally, I think it's very rare, but you'd also want to check that there is no federation conflict between your server and a server with your friends or people you would want to interact with. Some admins might decide that they don't want to be federated with another instance that has different content policies and/or values.

<h3>Finding Mastodon servers</h3>
Overall there's a lot of things to consider before joining a specific Mastodon instance, a good place to start is the <a href='https://joinmastodon.org/servers'>server list</a> on the official website, which is NOT an exhaustive list of servers though. Another place to find an instance that might be a good fit for you is <a href='https://instances.social/'>this server search engine</a>. One popular server is the <a href='https://mastodon.social/home'>flagship and oldest running mastodon instance</a> hosted by it’s lead developer <a href='https://mastodon.social/@Gargron'>Eugen</a>. It's kind of like a general purpose hub.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/servers.png" alt="">
</span>
<p></p>

Joining multiple servers is also an option. In my case I have an account on mastodon.social and genart.social (which requires an invite). I don’t know if it’s the best way to do it or not, but in that manner I’ve reserved that handle on both of those servers. If you start an account on one server and then decide that you'd like to switch over to another instance, migrating from one server to another is also a possibility which we'll talk about in more detail later in the article.

<iframe src="https://mastodon.social/@gorillasun/109263122704312935/embed" class="mastodon-embed" style="max-width: 100%; border: 0; border-radius: 5px;" width="100%" allowfullscreen="allowfullscreen"></iframe><script src="https://static-cdn.mastodon.social/embed.js" async="async"></script>
<p></p>

After you’ve chosen your server, you just need to enter your email, a password and verify the link they send to your email. And you’re good to go really. Next step is setting up your profile which should be a no-brainer.


<h3>Key Points:</h3>
<ol>
<li>Choose a server that has a content policy you agree with</li>
<li>Pick a server with trustworthy admins</li>
<li>Select a server that is relevant to your niche and the type of content you intend to post</li>
<li>Check that there's no federation conflict with servers your friends are on</li>
<li>Your server determines your overall Mastodon handle</li>
</ol>

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/2.png" alt="">
</span>
<p></p>


<h2><a name='3'></a>Tooting (Tweeting) on Mastodon</h2>

Posting and interacting with content on Mastodon is fairly similar to Twitter, there are however a couple of things that are different. First we'll go over everything noteworthy about posting content on Mastodon followed by interacting.

<h3>Creating toots</h3>

1. Tweets on Mastodon are called Toots, some instances might also have a different lingo.
2. Toots have a limit of 500 characters instead of the 280 characters on Twitter for more nuanced conversations.
3. Instead of setting who can reply to your toots, like on Twitter, you can set the visibility of your toots.
4. You can place content warnings on your toots (think of a spoiler tag on discord).

The larger character limit of toots is a breath of fresh air, sometimes it's really useful to not have to cram sentences into the 280 character limit. Another thing is that you can choose who gets to see your toots by choosing the timeline that they'll get posted to (we'll talk in more detail about the different timelines in the next section). On Twitter, if your profile is public, then your tweets are by default also public. In that manner you can only control the set of accounts that can reply to your tweets, not their visibility though. On Mastodon it's the other way around, which honestly makes a lot more sense: you can't control who gets to reply to your toots, but you can control who gets to see your toots.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/privacy-comparison.jpg" alt="">
</span>
<p></p>

It's still an open discussion if this might be changed for Mastodon in the future, an extensive discussion about the pros and cons of this can be found in <a href='https://github.com/mastodon/mastodon/issues/14762'>this github issue</a>. It might also change on Twitter now that Musk is in charge. Who knows, I'll write an update then. As simple as these mechanics might seem, a lot of thought needs to go into designing them such that ultimately healthy interactions are incentivised.

Placing a content warning on one of your toots is akin to marking your discord messages as spoilers, such that the text of the discord message is hidden and needs to be manually clicked to reveal its content. Likewise on Mastodon the person viewing your toot needs to click "show more" to be able to see the toot's content. The only difference here is that on Mastodon you can add a short text that informs the viewer what kind of content they should expect:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/Content Warning Collage.jpg" alt="">
</span>
<p></p>

One last thing to mention here is @howtomentionpeople (pun intended). Mentioning people on Mastodon is only slightly more complex than on Twitter, because handles will change depending on what server they are on. For folks that are on the same server as you, metioning them is as simple as tagging them by their handle @username, mentioning users on other servers can be done by tagging them by their handle alongside the server name extension @username@servername.

Hashtags are also a thing on Mastodon, simply prefix a word with the octothorp symbol. When you click on a hashtag it'll take you to a timeline that show you all of the tweets that use this hashtag. The important thing about hashtags is that they're the only searchable part of toots which is important if you want your toots to be discovered under a certain category.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/Hashtags.png" alt="">
</span>
<p></p>

<h3>Interacting with other toots</h3>
Interacting with toots from other users is also a little bit different than on Twitter. On Mastodon you can reply to, favourite (like/heart) and bookmark a toot. Similarly to re-tweeting you can also <b>boost</b> a tweet, with the slight difference that there is no quote-boosting. Why? It was an intentional choice by Eugen while developing Mastodon:

<iframe src="https://mastodon.social/@Gargron/99662106175542726/embed" class="mastodon-embed" style="max-width: 100%; border: 0; border-radius: 5px;" width="100%" allowfullscreen="allowfullscreen"></iframe><script src="https://static-cdn.mastodon.social/embed.js" async="async"></script>
<p></p>

Additionally favouriting a toot on Mastodon is not something that will be publicly announced to other Mastodon users, nor can people who check out your profile see what you've favourited unlike on Twitter where there's an entire tab where you can stalk what someone has liked. You can still see a list of users that liked a toot by clicking on the number favourites underneath a toot.

<h3>Key Points:</h3>
<ol>
<li>Mastodon tweets are called toots and have an extended limit of 500 characters</li>
<li>Mastodon has a content warning feature, and different toot visibility options</li>
<li>You can mention people from other servers with their full handle</li>
<li>Favouriting toots on Mastodon is more private than on Twitter, no one can see what you favourited</li>
<li>Toots can't be quoted by design, as a measure against toxic behaviour</li>
</ol>


<h2><a name='4'></a>The different Timelines</h2>

We've already talked a little bit about how Mastodon servers make up a larger conglomerate known as the 'Fediverse'. The different servers are federated (connected) in a way that allows users on one server to interact with users on other servers. The fact that you can interact with users in different groups gives rise to different timelines, which will show you different types of content. These timelines are:

1. The <b>Home Timeline</b> is your main timeline and shows you toots from all of the folks that you follow, whatever server they might be on.
2. The <b>Local Timeline</b> shows you everything that is posted to your home server, a great way to see content that is relevant to your niche.
3. The <b>Federated Timeline</b> shows you toots from all the servers that your instance is connected to, and is constantly updating itself with new toots at an incredibly rapid pace.

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/the timelines.png" alt="">
</span>
<p></p>

If you've been on Twitter you're probably familiar with your home timeline, where you can <b>sometimes</b> see things that people you follow have posted, intermingled with random cat videos and advertisements that the Twitter algorithm thinks you might enjoy, in an arbitrary non-chronological order aimed at keeping you engaged for much longer than necessary. The different timelines on Mastodon are strictly chronological, there is no algorithm that shuffles around what you see. This is genuinely a huge thing for me, it made me realise how much time I was actually wasting on Twitter, looking at things that I didn't actually care for. On Mastodon spam and clutter is minimal, you can choose what you want to see.

The local timeline shows you all of the toots from all accounts that exist on your server. The Federated timeline is special in a way that it will show you toots from all of the instances that your server is federated with, for larger servers that are federated with many others this timeline is often very busy, you'll probably seeing toots coming in faster than you can actually read them. Both of these are however also in chronological order, which has a very retro Twitter feel to it.


<h2><a name='5'></a>The advanced web Interface</h2>

Mastodon's advanced UI interface essentially turns it into a tweet deck experience, where you can create and customise a column layout each of which containing different types of content. Accessing this view is done from though the preferences tab:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/advanced web interface toggle.png" alt="">
</span>
<p></p>

And you'll be met with a view similar to this:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/advanced web interface example.png" alt="">
</span>
<p></p>

This is very reminiscent of what tweetdeck looks like:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/tweetdeck.png" alt="">
</span>
<p></p>

You can customise the layout by pressing the little settings button in the top right corner of each one of the different columns. In this manner you can pin, unpin and move the columns to your preference. To create and pin a new column, you can simply click on one of the timelines or a hashtag and a new column will pop up, then simply click the pin button (button might be in a slightly different location, because different instances sometimes have different styles).

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/pinning.png" alt="">
</span>
<p></p>

A cool thing is that you can also pin a specific hashtag! To follow specific users you can create a list, similar to Twitter:

<span class="image fit" style="margin: 0 0 1em 0; padding: 0 0 0 0;">
  <img class="viewable" src="https://gorillasun.de/assets/images/Mastodon/annotely_image.jpeg" alt="">
</span>
<p></p>


<h2><a name='6'></a>Concluding Thoughts: Mastodon vs. Twitter</h2>

In the past 1.5 years of my creative coding endeavours I’ve used Twitter extensively, having posted over 5k tweets and gained a following of almost 6k people (incredibly grateful for each one of you! 🙏). It would not have been possible for me to get this far with creative coding if I didn’t share my work on Twitter. I sometimes see Twitter getting a bad rep for being ‘Toxic’, and some spaces probably are, but I think it just depends on how you use it. Personally I’ve used it almost exclusively to share my art and my writing, and generally remained within my own twitter bubble. Moreover, Twitter has allowed me to make countless internet friends and have invaluable interactions with so many lovely people, and for that I am infinitely grateful.

For the most part, the moderation team at Twitter does a good job at suppressing heinous content, which is something that can't be optimally solved with decentralised and instance specific moderation like on Mastodon. For example, nothing stands in the way of a group of people spinning up a Mastodon instance and using it as an outlet for bigotry. This in fact happened in 2019 when the far-right social network GAB migrated to it's own Mastodon server. The way Mastodon is designed does not allow for an external force to moderate that instance. Server admins can still decouple their servers from that specific instance, essentially banning any and all content propagated from the instance in question. In that manner one bad actor requires a collectively large effort to be dealt with. At this point most servers have no connection to the GAB instance, which has also been banned from being accessed through many of the apps that support Mastodon. It's a tricky problem, but if people want to post heinous content they will find a way, ultimately it's not a Mastodon problem, it's a people problem.

All in all, Mastodon overall feels like a viable alternative outlet for sharing my work and thoughts, as well as interacting with others. It has a distinct indie feel to it that is really refreshing, it also makes me feel like I'm in control over what content I get to see without the mind numbing flood of superfluous information. On Mastodon you can choose what type of experience you get, whereas Twitter's algorithm is designed to keep you scrolling for as long as possible. I'm quite honestly very tired of being shown random videos, and I refuse to spend one more second of my time on content that I didn't choose to see. Lastly, on Mastodon you can rest assured that your data and activity isn't being harvested in some manner, because simply put there is no obfuscated algorithm working in the background. Furthermore, because the code is open source and accessible to everyone, even if there was something mischievous going on, it wouldn't take long for someone to uncover it.

That said, I’m not planning on stopping my Twitter activity. However, relying on a single service for your entire social media outreach also feels like you're limiting yourself. I don’t think Twitter will implode overnight, and believe that people will keep using it for at least some time to come, but from now on I'll also be using Mastodon just because it feels right.

<h2><a name='7'></a>More Reading Material</h2>

Here's some more Mastodon related reading material:

<h3>Guides</h3>
1. <a href='https://blog.joinmastodon.org/2018/08/mastodon-quick-start-guide/'>Official Mastodon quick start guide</a>
2. <a href='https://kevquirk.com/how-does-mastodon-work/'>Guide to Mastodon by Kev Quirk</a>
3. <a href='https://www.theverge.com/2017/4/7/15183128/mastodon-open-source-twitter-clone-how-to-use'>The Verge Guide to Mastodon</a>
4. <a href='https://fedi.tips/'>Guide to everything Fediverse related</a>


<h3>Tools</h3>
1. <a href='https://crossposter.masto.donte.com.br/'>Mastodon-Twitter cross posting tool</a>
2. <a href='https://fedifinder.glitch.me/'>Find the Mastodon handles of your Twitter followers and following</a>
3. <a href='https://communitywiki.org/trunk'>Trunk: a Mastodon mass following tool</a>

<h3>Miscellaneous</h3>
1. <a href='https://hackernoon.com/welcome-to-mastodon-111d9227e56a'>A welcome note from Eugen Rochko</a>
2. <a href='https://blog.joinmastodon.org/2018/07/cage-the-mastodon/'>Some of Mastodon's design decisions explained</a>
3. <a href='https://www.theverge.com/2019/7/12/20691957/mastodon-decentralized-social-network-gab-migration-fediverse-app-blocking'>The Verge article about the Gab problem on Mastodon</a>

<h3>Tips</h3>
1. <a href='https://axbom.com/mastodon-tips/'>Ten Mastodon tips by Per Axbom</a>
2. <a href='https://mastodon.social/@Gargron/103393780267601137'>How to migrate a Mastodon account from one server to another:</a>
<iframe src="https://mastodon.social/@Gargron/103393780267601137/embed" class="mastodon-embed" style="max-width: 100%; border: 0; border-radius: 5px;" width="100%" allowfullscreen="allowfullscreen"></iframe><script src="https://static-cdn.mastodon.social/embed.js" async="async"></script>
<p></p>

If this post helped you, consider sharing it with a friend or other folks that would like to get started with Mastodon. Otherwise, thanks for reading and happy tooting! Or come say hi over on Mastodon and/or Twitter!
