---
title: GWT Confusion
author: David Ackerman
layout: post
date: 2011-03-02T07:49:36+00:00
url: /2011/03/gwt-confusion/
categories:
  - Software Development
  - Software Testing

---
Recently, I&#8217;ve been working on a <a title="GWT project home" href="http://code.google.com/webtoolkit/" target="_blank">GWT</a> project at work. Well, starting it, that is. It&#8217;s interesting how many different ways there are to develop in this framework. All the simple examples are in the style of&#8230; well, let&#8217;s call the technique &#8220;_spaghetti code&#8221;_. They&#8217;re toy examples that don&#8217;t really have any sort of pattern, so they would quickly get unmanageable if you were to start building a real application on top of them.

The other methods are both MVP, but now there are two different versions you can read from the GWT documentation. There is the &#8220;_old stinky way&#8221;_, and the &#8220;_new shiny way&#8221;_. I might decide to go with the old stinky way in the name of testability and simplicity, but that&#8217;s just me.

## <img title="Stinky MVP" src="http://greenbabyguide.com/wp-content/uploads/2010/07/Stinky-diaper-pail.bmp" alt="Stinky MVP" width="306" height="239" /><img title="Shiny GWT" src="http://bwog.com/uploads/diamond.jpg" alt="Shiny GWT" width="180" height="239" />

## MVP

<a href="http://en.wikipedia.org/wiki/Model-view-presenter" target="_blank">Model-View-Presenter</a> is the main software pattern encouraged by _good_ GWT code. The main idea is to make the view as dumb as possible. Since the view will be full of hard-to-test widgets and DOM manipulation, let&#8217;s make it dumb so we don&#8217;t have to test it. The model is pretty obvious &#8211; it&#8217;s purpose is to contain the business logic involved in manipulating the underlying data and should be highly testable. The presenter is another POJO who&#8217;s job is to respond to events from the view code (it will ultimately be executed as javascript, so it&#8217;s going to be fast), and respond by asking the server for various information in the form of GWT RPC calls.

## The Old Stinky Way

So what is the &#8220;_old stinky way&#8221;_, you might ask?  It&#8217;s the way they have been describing MVP since before GWT 2.1 (which, as far as I know, was only released sometime in October 2010).  The basic premise goes like this:

  * The Presenter is a POJO with an inner interface that the View must implement 
      * The Presenter also registers a bunch of listeners on the view so it knows when, say, a button gets clicked.
      * The Presenter will also make RPC calls to services on the server side if necessary.
  * The View implements the presenter&#8217;s interface and displays untestable widgets
  * The AppController is in charge of figuring out which Presenter and View to show, so it listens to the onHistoryChange event, which gets fired each time the application wants to &#8220;change pages&#8221; (although it&#8217;s not a full page reload, it&#8217;s just javascript redrawing the view appropriately).

This way is old and stinky, supposedly because you don&#8217;t have any sort of framework deciding what classes to instantiate. The Presenter, AppController, and Service are all POJOs, easily testable. However, there is nothing, other than an onHistoryChange event, that tells us that a new &#8220;page&#8221; is wanted, and there is no guarantee that we will actually _go_ to a different page when that happens. It is fully up to the AppController to do that.

The _&#8220;new shiny way&#8221;_ is predictably more complex. I think the main goal was to build a framework around places, such that there is existing GWT code that monitors what pages should be rendered and automatically creates and shows them. Then all you need to do is define your Views and Presenters, well, and the mapping from url &#8211; > view, and GWT does the rest! The problem is that Java doesn&#8217;t seem to allow a really simple and <a href="http://en.wikipedia.org/wiki/Don't_repeat_yourself" target="_blank">DRY</a> way of doing this.

## The New Shiny Way (or the Overengineered way)

Let me try to explain how the new shiny works without losing you (or getting lost myself).

  * The Presenter still is a POJO, but there is no inner interface.  In fact, the Presenter implements an interface defined in the _View._
  * The View has an interface for the Presenter, and a method called setPresenter so there is bi-directional communication between the View and Presenter.

While I like the fact that the Presenter no longer is fully bound to the View interface, I don&#8217;t see how that makes things a lot more flexible.  If the Presenter is changing it&#8217;s behavior such that it needs to change it&#8217;s interface, the View classes will need to change as well.  This way gives you more flexibility, but also makes it harder to tell just how the Presenter is going to _use_ the view.  Since there is no longer an obvious interface to the View from the Presenter, anything is possible.  Multiple views could be called upon from the ClientFactory and nobody would be the wiser.  This doesn&#8217;t seem like a step forward to me.

  * ClientFactory gets created in the beginning of the app with the EventBus, PlaceController, and Views.  The idea is that the Presenters will get the Views they want out of this object.

Sounds like a violation of the <a href="http://en.wikipedia.org/wiki/Law_of_Demeter" target="_blank">law of demeter</a> to me. &#8220;X.getY().callZ()&#8221; is really hard to mock (and therefore test), because you need to create a mock X, mock out getY to return a mock Y, then mock out callZ to do what you want.  This ClientFactory sounds a little bit like <a href="http://misko.hevery.com/code-reviewers-guide/flaw-brittle-global-state-singletons/" target="_blank">a ServiceLocator antipattern</a>.

  * Activity is a presenter of some view, but there is no interface that defines this. It just recieves a Place object (to give it state about it&#8217;s current location), and a ClientFactory where it can get the View it wants.

This is annoying, because it is probably not getting and rendering multiple views at once &#8211; so why not specify what it wants statically in an interface?  This added flexibility isn&#8217;t exactly wanted here.

  * Activity has a few methods on it like mayStop() which get called if the user is about to leave that particular page.  Cool, but could pretty easily be done manually.
  * If we want to go somewhere else, we call clientFactory.getPlaceController().goTo(place).  Once again, this is a violation of the law of demeter, and hard to test.

Then we get into the Place functionality.  This is the _really_ confusing stuff.

  * Place object is in charge of holding state for what kind of information is needed by Presenters in this location.  It also usually has an inner class called Tokenizer that can convert a URL -> place and visa versa.
  * Your own PlaceHistoryMapper is an interface which must be created where you extend PlaceHistoryMapper and then create a weird annotation called @WithTokenizers and list off all the possible Tokenizers in your application.  Didn&#8217;t we already define this by having classes called Tokenizer in our Place classes?  Weird that we have to declare this again..
  * Activity Mapper is in charge of determining which Place object should be mapped to which Activity object.  They recommend a long list of (place instanceof SomePlaceObject) type code.
  * EntryPoint code is wiring everyting together.  It tends to need ClientFactory, EventBus, PlaceController, ActivityMapper, ActivityManager (not to be confused with the mapper!), AppPlaceHistoryMapper, PlaceHistoryHandler, and RootPanel.  Most of those classes I don&#8217;t understand in the slightest.  The descriptors Controller, Manager, and Handler don&#8217;t mean a whole lot to me, and even after reading about them 30 minutes ago, I think I have forgotten.

## WTF?

So the main lesson here is <a href="http://en.wikipedia.org/wiki/You_ain't_gonna_need_it" target="_blank">YAGNI</a>.  I really think that there are decent implementations of &#8220;Places&#8221; in GWT, but this code is trying to be too generic, and in the process is making things more complicated than they need to be.

If you&#8217;d like to see the document showing all of this craziness, <a href="http://code.google.com/webtoolkit/doc/latest/DevGuideMvpActivitiesAndPlaces.html" target="_blank">be my guest</a>.

I don&#8217;t want to say that MVP is wrong, or even that this method won&#8217;t work, but just that it seems way too complicated given the value it provides.  At least for my feeble mind.

Let me know if you disagree in the comments!