---
title: Nice, Neat Rows
tags:
- Neural Net
- Vectorization
- Linear Algebra
- Python
- Machine Learning
---

So I failed to take notes.  Lesson learned.  It has been a hectic two weeks plying through a mess that I admittedly should not have made.  I should have continued iterating through the design only with a test firmly in hand at each step.  I didn't and I paid the price.  Here is where I should pile up some epic story of failure and perseverance, littered with trials and tribulations, successes and pains.  But there is nothing so entertaining to be had in this venture, just a mess of spaghetti and a commit path just as wandering and even more useless.  So I'll just say it:

Test.

Document.

Repeat.

(head slap)

But, to give this post a point, I did come away with something more than the oh-so-obvious (but oh-so-important) advice above.  Where did it begin?  My original code was full of nested for loops in the brute force pursuit of updating each weight on the atomic level.  Backpropagation is a serious business and there isn't much time for messing around, let's update each weight individually.

{% highlight python %}
for i, neuron in enumerate(self.neurons):
                temp_err = 0
                for j, la_neuron in enumerate(layer_ahead.neurons):
                    temp_err += layer_ahead.neurons[j]["neuron"].weights[i] *\
                                layer_ahead.error_matrix[j]
                self.error_matrix.append(self.mr_output[i] *
                                         (1 - self.mr_output[i]) * temp_err)
{% endhighlight %}

Okay, the word 'inelegant' comes to mind, and quite possibly incorrect, but it was a valuable exploration for my own personally learning process.  A step that needed to be taken.  Something however was horribly broken in my code during this phase, it could well be this monstrosity.  So in my maddening spins and deep frustration I took a break and to reset my mind. I took a look at [this article](http://iamtrask.github.io/2015/07/12/basic-python-network/) by I Am Trask.  (pause) Okay, now that I'm over the fact my ego has been dashed by some stranger and 11 lines of code.  Let's ponder that for a moment ... 11 freakin' lines of code!  Okay, I'm totally over my ego.  That is amazing.  And completely beautiful.  

My reverie aside, in reading those 11 lines(!!!!) I had an epiphany.  My internal visualization of the weight space was wrong.  Or at least overly limited and not useful.  Let's take a momentary step back in time.  When I wrote my first Perceptron and instilled it with what I thought was backpropagation at the time (I was wrong about that too, but that's not what we're here for right?), I had a single weight space that was taking input and passing it out (no real hidden layer).  The mental model I built around this was a weight space that precisely matched the input vector, an 8 x 8 matrix of greyscale values.  Now this was ridiculous of course for many reasons, not the least of which was the fact I as flattening the input vectors to begin with, but again ...

So my mental model was misguided, what else could I use?  Trask's implementation was the key.  

{% highlight python %}
syn0 = 2*np.random.random((3,4)) - 1
{% endhighlight %}

He is treating all of the weights across all the neurons in a layer as a single matrix! 

"cool visualization of that here"

Each column represents a neuron, and each entry in that column a weight corresponding to an entry in the input vector.  Now, transpose that and matrix multiply against a 'vertical' input vector and you get ... 

"cool visualization of that here" (cut me some slack, this is my homework project right now!)

a vertical vector, each of whose entries are the scalar dot product of the columns of the weight matrix with the input vector.  But that is just equivalent to the output of that neuron!  So the resulting 'vertical' vector is the output of that layer.  Simple (hah!) linear algebra. 

Aside from allowing my brain to finally wrap around what was happening in a useful way, it provided an enormous speed boost.  Funny how going from nested to for loops to a single matrix multiplication, which numpy handles under the hood with our good friend C can be useful.  What originally took 6 hours (on the MNIST dataset) took 5 minutes!  Now if all refactorings can be that productive, I'll be set.

Notes on next steps for finnegan:

  * Obviously need a visualizations for all of this
  * A way to optimize the hyper-parameters, hopefully the viz will help guide the artist's hand here
  * A blog post about Hinton's Dropout method that I did add to the latest version
  * Regularization function for the weights to prevent over-fitting the training data (this has been another debacle of my personal design, I will re-approach from scratch with my head screwed on straight)

Cheers and Happy Learning.