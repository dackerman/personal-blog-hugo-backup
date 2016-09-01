---
title: Ep.4 TDD Effective Test Splitting
author: David Ackerman
layout: post
date: 2012-07-06T03:00:42+00:00
url: /2012/07/ep-4-tdd-effective-test-splitting/
categories:
  - Productivity
  - Software Development
  - Software Testing
tags:
  - What I Learned Today

---
I came across some great videos on TDD recently, and think a lot of people can benefit from watching them (they cost $7.50 for each 20 minute video, but it&#8217;s worth it).  Kent Beck explains TDD with an example: <http://pragprog.com/screencasts/v-kbtdd/test-driven-development>

I am already reading Kent Beck&#8217;s writings recently since I am reading through his XP book, so this was a nice tangential supplement to that. I already am familiar with TDD, but like many others, I sometimes get tripped up on decisions that seem to contradict the red/green/refactor cycle, or make it harder to accomplish. One such thing is:

### What to do when you write a simple test, but to make it pass you need to write a bunch of code?

Kent Beck explains this very well in his video (it&#8217;s right in the beginning of video 1 if you&#8217;re curious and want to purchase it). He is talking about making an interface to the &#8220;Tyrant&#8221; api, which is a basic byte store on a server which communicates through a socket. He mentions how he wants to use the system, much like a Map<T>, and writes the first test like so:

<pre>@Test public void getRetrievesWhatWasPut() {
        Tyrant t = new Tyrant();
        t.put(key, value);
        assertEquals(value, t.get(key));
    }</pre>

Please note, he hasn&#8217;t written a single line of production code yet. It may seem simple, but you have to write a lot of code to get this working. You have to do both the operations for get and put in full just to see this first test pass. Kent Beck likes to get constant feedback about how his code and design are doing, and this just isn&#8217;t frequently enough for him. Therefore, he doesn&#8217;t write this test at all.

### Just write some code to verify your first assumptions, then refactor

He starts by just writing the implementation for `put` in a test. This means no Tyrant class, no nothing. He opens up a Socket object and starts writing bytes to it like `0xCB` to start a transmission etc right in the test case! This ends up working very well for him because he gets the test green (which validates his assumptions about how the Tyrant API even works), and from there, he starts refactoring a little at a time.

The first refactorings are so small that they seem trivial. He wants to add an `open` method to start the connection because he wants to make it explicit to the user that although the interface to Tyrant looks like a map, it is really making more expensive socket calls. After doing this, it makes sense to make a `Tyrant` class. However, he makes the class an inner class of the TestCase itself. Why? Because it&#8217;s the simplest thing to get things working. After he had some substantial tests and verified that the API code was going to be around for a while, he then moved it into it&#8217;s own class and package.

I thought it was really interesting how small his steps were when building code. I have heard that this is the right way to go, but am always tempted to take big steps while developing. I frequently think I know what to do to get to a good design, but I almost always end up taking enough untested steps, that I end up having to rewind some of it when it doesn&#8217;t turn out just right. TDD can really help to validate a design as early as possible so we can avoid rework. That is not to say that you can avoid all rework (and it&#8217;s perfectly healthy to do refactorings at all stages of code development), but that TDD helps keep your code as &#8220;complete&#8221; as it can be for the features written.

Overall, I think these videos were really helpful (I watched all 4). The concept of TDD is really simple &#8211; the hard part is making the right decisions in ambiguous situations like these, and not getting discouraged if you find yourself being inefficient at first. With experience, TDD can really be a great tool in the toolbox.