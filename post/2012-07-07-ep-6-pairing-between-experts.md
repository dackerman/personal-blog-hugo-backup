---
title: Ep.6 Pairing between Experts
author: David Ackerman
layout: post
date: 2012-07-07T22:40:03+00:00
url: /2012/07/ep-6-pairing-between-experts/
categories:
  - Productivity
  - Software Development
tags:
  - What I Learned Today

---
I rediscovered the [James Shore: Let&#8217;s Play TDD][1] series yesterday, and had a lot of fun watching him pair with [Roy Osherove][2] in episodes <a href="http://jamesshore.com/Blog/Lets-Play/Episode-48.html" target="_blank">48</a> through <a href="http://jamesshore.com/Blog/Lets-Play/Episode-52.html" target="_blank">52</a>.

Roy and James paired during the <a href="http://oredev.org/" target="_blank">Øredev</a> conference, so presumably they hadn&#8217;t paired before, and it _showed_.  I cringed several times toward the beginning as they worked out a lot of their differences, and there were plenty. James Shore prefers multiple asserts in a given test if it helps to describe the functionality, and Roy prefers one assert per test, and keeping them in order of how they were developed.  James Shore likes to work in super-small batches, and Roy prefers to write out the whole test first (even if the classes don&#8217;t yet exist) to get a feel for what needs to be done.

<img class="alignleft" src="http://www.olympic-games-2012-london.com/wp-content/uploads/2011/11/Boxing.jpg" alt="" width="300" height="251" />

This is interesting because pairing can really bring out the differences in people. It&#8217;s especially true when both of those people are experienced and opinionated. Each has their own ways of doing things, and they are both likely effective at doing TDD, but they have to come up with a compromise while together. This could go either way &#8211; the two experts can work together and learn alternatives to the way they do things, or they could beat each other up 😉

Pairing with a more junior developer, however, doesn&#8217;t usually have as much back-and-forth, since they are likely more willing to listen to the senior developer on style and implementation issues. The downside here is that the junior developer will check out and just do what the senior one says, which might keep them from challenging and questioning as they go.

I think toward the end James and Roy really bonded and got their feature done (making a table that shows alternating rows of colors), and I was impressed by the way that they tried each other&#8217;s methods even though sometimes they vehemently disagreed with how the tests should play out and what functionality to write.  Although they seemed agitated at times, I think this is the way to learn. It&#8217;s okay to have strong opinions, but respect for your fellow developer is crucial to success.

 [1]: http://jamesshore.com/Blog/Lets-Play/
 [2]: http://artofunittesting.com/