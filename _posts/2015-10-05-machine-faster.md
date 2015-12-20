---
title: Speed at a Cost
tags:
- Machine Learning
- Efficiency
- Python
- Numpy
- Vectorization
---

Waiting.  Waiting ... Waiting ...

Okay, that is getting lame.  

Running 1000 input vectors through ten neurons hundreds of time with hundreds of calculations each time, to verify the code is functional, turns out to take some bit of time.


So where is all the time going?  I imported cProfiler to find the cycle hog.  And lo and behold the dot product function, multiplying the input vector by the weight vector, over and over.  Since we are living under the GIL and only one thing can happen at a time, which makes the script a huge long queue.  Since other languages aren't restricted by this, let's look toward them for a little help.

{% highlight python %}
import numpy as np
{% endhighlight %}

No, numpy isn't another language, of course, but it can make use of our good friend C.   

{% highlight python %}
answer = np.dot(vector, weights)
{% endhighlight %}

And voilá, we have almost a 20% speed increase!

Once the partying died down, it was time to look where else we could find opportunities for speed.

The next crime against the Art of Code I found, nested for loops.  (A triple nested for loop when you count the epoch time.)

How to get rid of those.  Vectorization.  This should be fun to explore.  Back to the drawing board for now.
