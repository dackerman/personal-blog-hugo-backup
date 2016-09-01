---
title: Your Tests Are Making Your Code Inflexible
author: David Ackerman
layout: post
date: 2010-01-14T02:59:00+00:00
url: /2010/01/your-tests-are-making-your-code-inflexible/
blogger_blog:
  - csmajorsunite.blogspot.com
blogger_author:
  - Dave Ackermanhttp://www.blogger.com/profile/15439243804403661750noreply@blogger.com
blogger_permalink:
  - /2010/01/your-tests-are-making-your-code.html
categories:
  - Software Development
  - Software Testing

---
<!--.java{
font-family:consolas, "Courier New", courier, monospace;
font-weight:bold;
} -->

### A _Very_ Naive Approach

Most of us will agree that making code _flexible_ is a primary motivation in writing Object Oriented code. Otherwise, we would write our app as one giant class, with one giant method, and call it &#8220;efficient&#8217;. We would copy-and-paste everywhere, because we don&#8217;t want to introduce any &#8220;complexity&#8221; by adding _methods._ We certainly wouldn&#8217;t want to create whole new Classes&#8230; then we would have to look in more than one file to figure out how the system works! That is _way_ too prone to error!

### Coming back to Reality

No, of course we don&#8217;t have that mindset. Our code should be split up into as many classes is necessary for each of them to be responsible for _one thing._ Each class should, in turn, have the appropriate number of private methods to make each of them trivial to understand in isolation. And the public API of the class should be small and cohesive, so as to keep it from having to _change_ for more than one reason.

And tests! We need lots of tests! We need to make sure that each of our classes is tested at a sufficiently low level to be able to test every feature and expectation. This is where we run into a problem.

What is wrong with this code? (Borrowed from JUnit and <a href="http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882" target="_blank">Clean Code by Robert Martin</a>):

<img style="border: 0pt none; display: block; margin-left: auto; margin-right: auto;" title="assertEquals(&quot;expected:<b[a]> but was:<b[c]>&quot;, failure);" src="http://lh6.ggpht.com/_LVy1p_agL64/S06IeFiz7-I/AAAAAAAABEo/JyYh7qfLr_A/beforecode6.png?imgmax=800" border="0" alt="assertEquals(&quot;expected:<b[a]> but was:<b[c]>&quot;, failure);" width="522" height="362" />

I have only shown a few tests for brevity, but there are many more like it in the book (pg. 283 of Clean Code). Uncle Bob comments on the code, &#8220;I could explain it further, but the test cases do a better job. So take a look at Listing 15-1 and you will understand the requirements of this module in depth. While you are at it, critique the structure of the tests. Could they be simpler or more obvious?&#8221;

#### Simple and/or more obvious, does not a good test make.

I agree that the tests are very simple and obvious. They convey what they are trying to test very clearly. However, I have a problem with these tests. They are not very DRY. In fact, on the scale of DRYness, they are unbelievably WET with duplication.

Those who know what <a href="http://en.wikipedia.org/wiki/Don't_repeat_yourself" target="_blank">DRY</a> means, know what I am talking about already. Basically, there is a ton of duplication in this code. I am about 87.3% sure that Kent Beck (or whoever wrote these tests) used CTRL+C, CTRL+V more than a few times when writing 50 unit tests that look almost identical. It seems that most people think this is okay, because they’re tests. That you should be writing similar code for similar tests in the name of readability. However, I think if you find yourself writing the same code over and over again, you have a problem with focus. Each test should _focus_ on a feature.  You are probably testing the same thing in every one of those tests you copy/pasted.

#### Just like you don’t want one giant test that tests everything under the sun, you don’t want tons of tests that all test the same thing when they shouldn’t care about that thing!

In this example, that something is the format of the return string.

### Why Won’t the Maintenance Team Invite you to Lunch? Because you Copy/Paste Test Data

So how does this make it inflexible?  What if we wanted to change the &#8220;<&#8221; and &#8220;>&#8221; surrounding the discrepancy into &#8220;[&#8221; and &#8220;]&#8221;. Well, we would do a simple search and replace in our IDE, you say. What if the substitution isn&#8217;t so easy? What if we wanted to change the first &#8220;&#8230;&#8221; into &#8220;&#8211;&#8220;, but not the second set? We would have to manually go into each test and modify the relevant portions.  What about adding an extra parameter to the constructor that these tests don’t care about?

You have to make a change to every one of your tests.  You have to muck around with the code until it passes again, and now you have more nulls, or even worse, more “magic” numbers, in your tests.

### One Solution

You don’t have to sacrifice readability to soak up all that wetness in your code.  Lets take one step forward here, shown in the next example (# of tests cases reduced for clarity):

<img style="border: 0pt none; display: block; margin-left: auto; margin-right: auto;" title="assertFailureHasProperMessage(&quot;[b]&quot;,&quot;[c]&quot;,failure);" src="http://lh3.ggpht.com/_LVy1p_agL64/S06Iex8dR0I/AAAAAAAABEs/MeU2yY08Dq8/aftercode6.png?imgmax=800" border="0" alt="assertFailureHasProperMessage(&quot;[b]&quot;,&quot;[c]&quot;,failure);" width="597" height="271" />

So we’ve split out each assert into a method. One big win here, is that the formatting of the message, which is clearly _not_ what we are trying to test, is tucked away in the method.  The string still _gets tested_ just as before, but now we don’t see it in each and every test – which now more clearly shows the bits we are really trying to assert.  Unfortunately, passing in multiple parameters gets hard to read really fast, and we want to be able to use our tests as documentation.  Also, wtf is the first number in the constructor supposed to do?  You have to look at about a dozen test cases to figure out its purpose.

### Domain Specific Languages

A really good way to make your code self-documenting is to have lots of small, descriptive function and class names.  Well, since our tests _ARE_ our documentation (at least to other developers on the team, and your future self), we should follow this pattern even more.  instead of:

<img style="display: inline; margin-left: 0px; margin-right: 0px; border-width: 0px;" title="image" src="http://lh3.ggpht.com/_LVy1p_agL64/S06IfX7LHtI/AAAAAAAABEw/scV-9wyAbXo/image%5B10%5D.png?imgmax=800" border="0" alt="image" width="535" height="20" />

We could have something like:

<img style="display: inline; margin-left: 0px; margin-right: 0px; border-width: 0px;" title="image" src="http://lh5.ggpht.com/_LVy1p_agL64/S06Ifjk4WJI/AAAAAAAABE0/IDJiS8cGK60/image%5B14%5D.png?imgmax=800" border="0" alt="image" width="403" height="22" />

This allows us to concisely state, in _almost_ regular language, what we want the test scenario to be.  It is a mini-language, called a DSL, or <a href="http://en.wikipedia.org/wiki/Domain-specific_language" target="_blank">Domain Specific Language</a>.  Notice, also, how the “<span class="java">.compact(null);</span>” section is missing.  That is because we don’t care about it.  We aren’t using the message passed in, so the builder object inserts the <span class="java">null</span> for us.  If we don’t care about it, we don’t want it showing up in our test.

Another benefit we get from this approach, is that we aren’t _directly_ manipulating any of the code; we are calling functions on an object, which eventually manipulates the code.  In my opinion, this separates two concerns that rarely get separated: the semantics of the test, and the syntax of the test.  If we change the constructor to take more data, all we need to do is add another function to the interface, and all the tests will again pass (unless there is a bug, of course).

You see, we shouldn’t _care_ how many parameters the constructor needs in the tests, we just want to input some data, and make sure the output was correct.  We want to work at a higher level.  We are _manipulating_ at a low level, but _describing_ at a high level.  We do this in our production code as programmers, why not in tests?

Now, we can make our test harness handle <span class="java">null</span> information for us, and handle the passing of intermediate data like the “<span class="java">String failure</span>” in the first example.  Below is the entire Test class with the harness.  Obviously, it looks large as there are only two tests shown here, but the harness is mostly a one-time cost, so its effort in proportion to the benefit gets smaller over time.

<img style="border: 0pt none; display: block; margin-left: auto; margin-right: auto;" title="image" src="http://lh4.ggpht.com/_LVy1p_agL64/S06IgAOpNlI/AAAAAAAABE4/l5sQyibmCd0/image%5B5%5D.png?imgmax=800" border="0" alt="image" width="634" height="618" />

### What Have We Done Here?

Essentially, we have created our own domain “language” for testing.  It isn’t flexible enough to describe _all_ code; just this class or set of classes.  We can also reuse frameworks like this in other tests.  Generally this pattern is called the “Builder” because of its property of returning itself from a setter method.  This allows us to chain methods together and “build” an object with very little boilerplate code.  It is generally used with <a href="http://en.wikipedia.org/wiki/Fluent_interface" target="_blank">fluent interfaces</a> and Domain Specific Languages because it allows for a more _language-like_ syntax directly in the code.

This type of testing may not work for every case, but I would seriously consider it on your next project, and try it out for yourself.  DSLs are somewhat hard to write well, so practice makes perfect (and I am new at this as well, so I have a lot to learn).  You may find out interesting properties of how your code is structured by writing a DSL for it.

Disagree? Leave a comment.  A colleague I respect “whole-heartedly disagreed” with me on this, so I am open to a little discussion :).