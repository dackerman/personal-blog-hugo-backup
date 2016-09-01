---
title: Inheritance vs. Composition
author: David Ackerman
layout: post
date: 2010-02-02T05:21:52+00:00
url: /2010/02/inheritance-vs-composition/
categories:
  - Software Development

---
### Purpose

I came across this question while thinking about a recent framework that I had written for parsing my company’s proprietary binary format for message sending.  The solution I had come up with had quite a bit of inheritance, and as I was catching myself trying to be “clever” with some of the implementation, I started to <a href="http://en.wikipedia.org/wiki/Code_smell" target="_blank">smell something….</a>

Basically, I had read a previous <a href="http://www.berniecode.com/writing/inheritance/" target="_blank">article</a> about a year ago about why inheritance is bad in almost all cases.  This person suggested composition instead.  With the examples he had given, I found it hard to refute his evidence.  I now realize that his examples of inheritance that would work better with composition, while correct, were all from the same class of problem.

### Definitions

Many people think of inheritance as when you have objects in an “is-a” relationship.  For example:

<p style="text-align: center;">
  <a href="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/inheritance.jpg"><img class="size-full wp-image-50 aligncenter" title="inheritance" src="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/inheritance.jpg" alt="" width="560" height="420" srcset="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/inheritance-300x225.jpg 300w, http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/inheritance.jpg 800w" sizes="(max-width: 560px) 100vw, 560px" /></a>
</p>

<p style="text-align: center;">
  <p>
    A Maple <em>is-a </em>Tree, as is Evergreen and Spruce.  This is a good way of separating common functionality &#8211; you have a base class, Tree, and then a few subclasses which can be swapped in place of the Tree.  Sometimes you have an abstract class &#8211; if you didn&#8217;t want to be able to instantiate just a &#8220;Tree&#8221;, but had to specify a specific type. You might do this if the base class didn&#8217;t make any sense on it&#8217;s own, or it doesn&#8217;t specify a critical attribute or behavior that the subclasses must implement.
  </p>
  
  <p>
    And then we have composition.  Most people were taught to think of this as a <em>has-a </em>relationship.  This is a very intuitive way to think about it, but unfortunately, severely limits our use if this is the only metric we use to mentally design classes.  Here is a classic example from the textbooks:
  </p>
  
  <p style="text-align: center;">
    <a href="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/car-composition.jpg"><img class="size-full wp-image-51  aligncenter" title="car-composition" src="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/car-composition.jpg" alt="" width="560" height="293" /></a>
  </p>
  
  <p>
    A &#8220;Car&#8221; is literally made up of a seat, windsheild, optionally airbags, a chassis, steering wheel, and 4 tires.  This makes intuitive sense that a Car &#8220;has&#8221; these things.  It wouldn&#8217;t make sense for us to use inheritance here.  This is a fine example of composition, but it is not the type of composition that is really interesting.  The kind that is interesting involves behavior.
  </p>
  
  <h3>
    When &#8220;Is-A&#8221; fails: Square vs. Rectangle
  </h3>
  
  <p>
    A Square &#8220;is&#8221; a rectangle, right? We learned this mathematically in elementary school, that a square is a more <em>specific </em>type of rectangle.  You should always be able to use a square if you require a rectangle somewhere.  This sounds like a perfect place for inheritance!  We have one object that is more specific than the other, and it can be used in place of the other one! Awesome!  Unfortunately, this is a completely wrong design, and it was led astray by the &#8220;is-a&#8221; mentality.
  </p>
  
  <p>
    If you were to take a class &#8220;Rectangle&#8221;, stick a width and height on it, and then subclass it with Square, you would think you have a nice clean hierarchy.  Unfortunately, if you look closer, you see that a square can do <em>less </em>things than a rectangle.  Your rectangle class probably has a &#8220;setWidth()&#8221; and a &#8220;setHeight()&#8221; on it.  Well, for a square, these functions wouldn&#8217;t quite work right.  Sure, you can set the width, but you would have the set the height to the same value if you wanted to keep it a square.  You would have to do some manipulation behind the scenes to get those functions to keep up the constrains that Square guarantees. The client of this code expects a &#8220;rectangle&#8221;, and therefore if you substitute in a Square into their function, they will be shocked to find out that the setWidth() and setHeight() functions do the same thing!  When they designed their code, they expected the object they were dealing with to be able to change horizontally and vertically independently.  By subclassing rectangle in this way, you have broken that mental mapping, and therefore caused the client to distrust your code (now they basically have to look in the source code, or scour your documentation every time they want to make a small change for fear it will blow up in their face again).
  </p>
  
  <p>
    If you tried the same exercise where Rectangle inherits from Square, you would run into similar issues.  Really, a Square and a Rectangle, while mathematically related, have different behavior (one doesn&#8217;t <em>extend </em>the other), so they shouldn&#8217;t be directly related in the code.
  </p>
  
  <h3>
    When &#8220;Has-A&#8221; fails: Bouncy Balls
  </h3>
  
  <p style="text-align: center;">
    <a href="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/ball.jpg"><img class="size-full wp-image-52  aligncenter" title="ball" src="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/ball.jpg" alt="" width="560" height="563" srcset="http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/ball-298x300.jpg 298w, http://www.david-ackerman.com/blog/wp-content/uploads/2010/02/ball.jpg 800w" sizes="(max-width: 560px) 100vw, 560px" /></a>
  </p>
  
  <p>
    You have a Ball class.  It can set its color, and various other ball-like things.  You then decide that it makes sense to have a &#8220;Bouncing Ball&#8221; class.  This ball inherits all the functionality from Ball, and also does some extra stuff.  You wouldn&#8217;t think that there is any &#8220;has-a&#8221; relationship in here.  A Bouncing ball doesn&#8217;t &#8220;have&#8221; a ball, and a Ball doesn&#8217;t &#8220;have&#8221; a bouncing ball.  It sounds more like an &#8220;is-a&#8221; relationship.
  </p>
  
  <p>
    However, how would you implement a &#8220;BouncingBlinkyBall&#8221;?  You really couldn&#8217;t with most languages (i&#8217;m going to assume you can only inherit from one class here, like in Java).  If you have that functionality all locked up in an inheritance hierarchy, you have made your code hard to change.  You certainly can&#8217;t make a bouncing ball turn into a blinky ball at runtime, and you couldn&#8217;t have any objects that do both.  So what do you do?
  </p>
  
  <p>
    You use composition.
  </p>
  
  <p>
    A similar example is used by my earlier link to the <a href="http://www.berniecode.com/writing/inheritance/">berniecode</a> blog.  He is correct in suggesting composition to solve this problem.  If you want maximum flexibility, you could have a BouncingBehavior and a BlinkingBehavior, and contain those objects within the Ball class.  You can then control your Y coordinate with your Bouncing Behavior, and your alpha channel with your BlinkingBehavior.  Really, the two behaviors are very similar in that they turn on and off gradually, so you could just have two &#8220;AttributeChanger&#8221; objects and then swap the implementations whenever at runtime.  The pseudocode might look like this:
  </p>
  
  <div class="wp_syntax">
    <table>
      <tr>
        <td class="code">
          <pre class="java" style="font-family:monospace;"><span style="color: #000000; font-weight: bold;">package</span> <span style="color: #006699;">com.davidackerman.composition</span><span style="color: #339933;">;</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000000; font-weight: bold;">class</span> Ball <span style="color: #009900;">&#123;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000066; font-weight: bold;">int</span> x <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000066; font-weight: bold;">int</span> y <span style="color: #339933;">=</span> <span style="color: #cc66cc;"></span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">private</span> <span style="color: #000066; font-weight: bold;">double</span> alpha <span style="color: #339933;">=</span> <span style="color: #cc66cc;">1</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> AttributeChanger yBehavior <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> AbsSineWave<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
	<span style="color: #000000; font-weight: bold;">public</span> AttributeChanger alphaBehavior <span style="color: #339933;">=</span> <span style="color: #000000; font-weight: bold;">new</span> SquareWave<span style="color: #009900;">&#40;</span><span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
&nbsp;
	<span style="color: #000000; font-weight: bold;">public</span> <span style="color: #000066; font-weight: bold;">void</span> updateFrame<span style="color: #009900;">&#40;</span>Clock c<span style="color: #009900;">&#41;</span><span style="color: #009900;">&#123;</span>
		y <span style="color: #339933;">=</span> yBehavior.<span style="color: #006633;">valueAt</span><span style="color: #009900;">&#40;</span>c<span style="color: #009900;">&#41;</span><span style="color: #339933;">;</span>
		alpha <span style="color: #339933;">=</span> alphaBehavior.<span style="color: #006633;">valueAt</span><span style="color: #009900;">&#40;</span>c<span style="color: #009900;">&#41;</span> <span style="color: #339933;">/</span> <span style="color: #cc66cc;">100.0</span><span style="color: #339933;">;</span>
	<span style="color: #009900;">&#125;</span>
<span style="color: #009900;">&#125;</span></pre>
        </td>
      </tr>
    </table>
  </div>
  
  <p>
    Here, containment is working much better than inheritance ever would.  However, thinking in terms of &#8220;has-a&#8221; will never get you here. You need to think about the <em>behaviors </em>of your objects.  You need to think about how they will be used and how they will be changed &#8211; avoid trying to shoehorn the &#8220;real world&#8221; into code &#8211; it will never be a 1:1 mapping!
  </p>
  
  <p>
    So, we have a much more flexible class. This is great!  Really, we could always just contain our base class instead of inherit from it when we ant to do something.  So if composition is so much more flexible, then why would we ever use inheritance?  Bernie would say never, but I disagree. Inheritance is great when you <em>want </em>to keep something unchanging.  Obviously, many things change that we didn&#8217;t originally intend to change with software, so its very important to be very careful with this.  Are you <em><span style="text-decoration: underline;">sure </span><span style="font-style: normal;">it isn&#8217;t ever going to change? Not ever?  If so, then it might make sense to have inheritance.  Sometimes you really want to be able to swap out one object for another &#8211; in this case, you would need to have the containing class implement the same interfaces the contained objects have, and delegate to all of them  &#8211; this can get tedious, and noticing that your coding gets tedious is a huge red flag that you are designing something wrong.  You probably want inheritance here &#8211; if the APIs are so similar that you find yourself delegating </span>everything</em>, go with inheritance (unless it might change).
  </p>
  
  <p>
    Here is a handy-dandy matrix of features for you as a recap:
  </p>
  
  <table style="vertical-align: top;" border="1">
    <tr>
      <th scope="col">
      </th>
      
      <th scope="col">
        GOOD
      </th>
      
      <th scope="col">
        BAD
      </th>
    </tr>
    
    <tr>
      <th scope="row">
        Inheritance
      </th>
      
      <td>
        <ul>
          <li>
            Substitutability
          </li>
        </ul>
      </td>
      
      <td>
        <ul>
          <li>
            Compile Time Coupling
          </li>
          <li>
            Change in Base affects all Details
          </li>
        </ul>
      </td>
    </tr>
    
    <tr>
      <th scope="row">
        Composition
      </th>
      
      <td>
        <ul>
          <li>
            Flexibility
          </li>
          <li>
            Runtime Swap
          </li>
        </ul>
      </td>
      
      <td>
        <ul>
          <li>
            Big Potential public API
          </li>
          <li>
            Duplicate implementation with Delegation
          </li>
          <li>
            Cluttered Interface
          </li>
        </ul>
      </td>
    </tr>
  </table>
  
  <h4>
    Inheritance is better when:
  </h4>
  
  <ol>
    <li>
      The APIs of both objects overlap mostly
    </li>
    <li>
      The objects have the same purpose or meaning (one is just more &#8220;specific&#8221;)
    </li>
    <li>
      You want to use one object to replace the other
    </li>
    <li>
      The relationship is fundamental to how the class works, or you don&#8217;t want to be able to change implementations
    </li>
  </ol>
  
  <h4>
    Composition is better when:
  </h4>
  
  <ol>
    <li>
      The classes represent behavior modifiers (strategy pattern)
    </li>
    <li>
      The objects have different meanings but accomplish similar goals
    </li>
    <li>
      One object should control another object
    </li>
    <li>
      The object might want multiple kinds of that behavior type (i.e. if you were going to use multiple inheritance)
    </li>
    <li>
      You want to be able to change it at runtime
    </li>
  </ol>
  
  <p>
    So there we have it.  Composition and Inheritance both have their place.  Don&#8217;t listen to anybody who only uses one over the other.  Sure, composition is more flexible, but that flexibility comes with a cost &#8211; as do all things.  Consider both in all your designs.
  </p>