---
title: Ep.2 Python Packaging Perusal
author: David Ackerman
layout: post
date: 2012-07-03T16:02:32+00:00
url: /2012/07/ep-2-python-packaging-perusal/
categories:
  - Uncategorized
tags:
  - What I Learned Today

---
So I was hunting down (what I thought was) a weird bug in my python program today, which caused me to discover some interesting information about how python packages work.

In my small app, I only have a handful of python files. The project only goes one level deeper than top-level, for things like talking to the GitHub APIand MongoDB connetions. I&#8217;ve been successfully running the code in production on my webserver, but now it was time to build an offline loader class that could periodically update my database with fresh api data.  I thought I had it working, but found out that my tests were failing because my import to &#8220;model&#8221; was breaking. All my imports seemed to be correct, so I was confused.

I googled around and found some sites talking about the rules of how python packages work:

<http://stackoverflow.com/questions/72852/how-to-do-relative-imports-in-python>

Where someone was trying to import a &#8220;relative&#8221; path (something like &#8220;from .. import mymodule&#8221;), and someone mentioned this snippet from <a href="http://www.python.org/dev/peps/pep-0328/" target="_blank">PEP-328</a>:

> &#8220;Relative imports use a module&#8217;s \_\_name\_\_ attribute to determine that module&#8217;s position in the package hierarchy. If the module&#8217;s name does not contain any package information (e.g. it is set to &#8216;\_\_main\_\_&#8217;) then relative imports are resolved as if the module were a top level module, regardless of where the module is actually located on the file system.&#8221;

I didn&#8217;t know this was the case&#8230; I guess I hadn&#8217;t run into it before! When someone mentioned how there must be a non-hacky way to get around this issue, another user replied &#8220;Guido views running scripts within a package as an anti-pattern&#8221; via rejected <a href="http://www.python.org/dev/peps/pep-3122/" target="_blank">PEP-3122</a>. So much for that!

In the end, it turned out that my problem actually had nothing to do with this &#8211; and I was just missing an import in a file I hadn&#8217;t looked at &#8211; but this was still an interesting find!