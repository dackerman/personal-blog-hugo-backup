---
title: Ep.7 Automated Go testing with Eclipse
author: David Ackerman
layout: post
date: 2012-07-08T22:22:59+00:00
url: /2012/07/ep-7-automated-go-testing-with-eclipse/
categories:
  - Go lang
  - Productivity
  - Software Development
  - Software Testing
tags:
  - What I Learned Today

---
I&#8217;ve been messing around with <a href="http://golang.org/" target="_blank">Go</a> and <a href="http://en.wikipedia.org/wiki/Test-driven_development" target="_blank">TDD</a> today, and it seems that although there is a nice Eclipse plugin for Go called <a href="http://code.google.com/p/goclipse/" target="_blank">Goclipse</a>, there is no testing support (like Junit)! I was shocked, as I have always just taken JUnit for granted in Java. There is support for running tests from the command line, so I tried that at first.

I ran &#8220;go test&#8221; from the command line a few times before starting to get agitated by having to switch windows all the time, so I decided to automate it a bit.

Surprisingly, I didn&#8217;t find any ready-made solutions by a quick Google search, so I decided to write down how I did it to help others who ran into this issue.

## How to set up an auto test-runner for Go in Eclipse

  1. Right click on your Go project, click &#8220;_Properties_&#8220;
  2. Click on &#8220;_Builders_&#8221; on the left hand side
  3. Click &#8220;_New&#8230;_&#8221; to make a new builder
  4. Choose &#8220;_Program_&#8221; from the popup. This lets you execute an arbitrary command.

&nbsp;

Now it&#8217;s time to configure the tool. I&#8217;ve got a screenshot below as well.

  1. I cleverly named mine &#8220;_go test_&#8220;
  2. For the location on windows, the location of the go command is &#8220;`C:\Go\bin\go.exe`&#8220;. Adjust accordingly for your platform (<a href="http://golang.org/doc/install" target="_blank">instructions on the go page</a>)
  3. For working directory, click &#8220;_Browse Workspace&#8230;_&#8221; and choose your `src` folder.
  4. For arguments, you probably want to run the test command for all your sub packages, so I set the arguments to `test ./...`.
  5. Go over to the &#8220;_Build Options_&#8221; tab, and set up when this command should be run. I prefer to have it run as often as possible, so I selected &#8220;_During manual builds_&#8221; and &#8220;_During auto builds_&#8220;. This basically means every time I save a file it runs the tests. Perfect for TDD!

Go ahead and click &#8220;Ok&#8221; a couple times, and then modify some code. If you have the console open, you should see the output of your tests! Awesome!

Here are screenshots of what I did.  Happy coding!

<p style="text-align: center;">
  <a href="http://www.david-ackerman.com/blog/wp-content/uploads/2012/07/gocommand1.png"><img class="size-full wp-image-180 aligncenter" title="Go test" src="http://www.david-ackerman.com/blog/wp-content/uploads/2012/07/gocommand1.png" alt="Go test command options" width="525" height="593" /></a>
</p>

<p style="text-align: center;">
  <a href="http://www.david-ackerman.com/blog/wp-content/uploads/2012/07/gocommand_buildoptions.png"><br /> <img class="aligncenter" title="Go test build options" src="http://www.david-ackerman.com/blog/wp-content/uploads/2012/07/gocommand_buildoptions.png" alt="Go test command build options tab" width="525" height="593" /></a>
</p>