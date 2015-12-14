---
title: The Jungle of Documentation
tags:
- Sphinx
- ReadtheDocs
- Documentation 
- Python
---

Project, check.  Splash page for project, check (thank you, gh-pages).  Documentation, in case someone want to use this thing, ...  Well I've got docstrings, that's good enough, right?  Not so, say they, what hold sway.

<!--more-->

But they quickly take pitty on me and reveal the wonder of [Sphinx](http://sphinx-doc.org/).  Just a few quick keystrokes and voila, you have a set of documentation.  Well maybe a more than a few.  But it did arrive pretty, I will give it that.  

So, documentation, hot off the presses, let's land it on GitHub.  No problem a little add, here, a little push there and up it goes.  And ... broke.   No pretty!

Whahhapen?  Let's see let's just rebuild it.  Now that won't work!  Sheesh!  (Operator error of course, but my mistake wasn't clear for some time.)  Finally after divining into the depths of the conf.py and index.rst pages, I was able to recreate the docs.  Not exactly beautiful, but definitely readable, and certainly more than one may expect for an auto-generating script.  Special thanks to [Code and Chaos](https://codeandchaos.wordpress.com/2012/07/30/sphinx-autodoc-tutorial-for-dummies/) for finally breaking my mental ice.  Not as simple as I at first imagined. (I'm sure there is a tool out there to make this better, but that is for another day.) 

Then, back to the task at hand, up to the gh-pages servers.  Whoa, broke.  No pretty.  A little more research and ... it seems Jekyll breaks the Sphinx renders.  No problem, I'll just kill Jekyll on that page (which is a subpage of the gh-pages for the project).  Not so fast buddy, that is not how Jekyll works.   It is for your pages or not all, you don't get to mix and match sadly.

Hmmm. Decision time, do I tank the autogen for the project page in favor of a static website created from scratch?  Or do I host the docs somewhere else?

Not excited about divert back to the front end and this point in my studies, I opted for hosting them elsewhere.  I did come across tools that bridge the Jekyll-Sphinx divide, but what can of worms will that be?  Eager to move on to the next project with a process in hand ... I jumped at [Thunder](https://github.com/ThunderShiviah)'s suggestion of [Read the Docs](http://readthedocs.org).  Sweet, host there, then a quick link from the gh-pages and I'm done.  Not so fast.  

(pause for a face-palm)

Page builds locally just fine.  But, Read the Docs, still says 'no bueno'.  Back to the drawing board.

This is turned out, is a simple fix.  Read the Docs will not properly build docs for a script that imports third party libraries, more details [here](http://blog.rtwilson.com/how-to-make-your-sphinx-documentation-compile-with-readthedocs-when-youre-using-numpy-and-scipy/).  So with one final tweak.  Up in all [their](https://linear-neuron.readthedocs.org/en/latest/) glory (or not, but up at least).

Phew, I'm going to bed.  Maybe, I'll make them pretty tomorrow.

p.s.  Let's not do it that way again, I vote for skipping all the research next time, and just getting it done.  Happy documenting!
