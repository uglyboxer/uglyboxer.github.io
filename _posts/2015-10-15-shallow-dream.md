---
title: Shallow Dream
categories:
- Machine Learning
- Perceptron
- Python
- Deep Dream
---

So far, this experiment has yielded one major take away: **_I need more tools!_**

Based on the very broad concept behind [Google's Deep Dream project](http://deepdreamgenerator.com/), [Thunder](https://github.com/ThunderShiviah) wondered what it was my little pet Perceptron had actually learned. Seemed an innocent enough quesion.  


First step involved backing up a bit and polishing some of the project's rougher edges. I needed a visualization of the input and output.  [Matplotlib](http://matplotlib.org/) was the easy answer, especially since it was all over [SciKit-learn](http://scikit-learn.org/stable/index.html) in the first place.  And voilá.

![Number 3](/images/three.png)

Oops. Rabbit hole.  Back now.  A visualization now firmly in hand, what to work with next.  It was growing tedious to rerun the training set each time I needed to adjust something that would not present itself as afoul until the end of the run.  But as the state of the training vectors and therefor the state of the network object itself (specifically the weight sets for each neuron) was unchanged, I could just save that and re-import it for each run.  Some hacky i/o code later (that I'll choose not to share) and next step down.  It is only now that I found out about "pickling", which is just one more for the list (see paragraph 1).

Okay, let's feed it something other than a number and have it guess what it is.  An 8 x 8 grid of "random normalized values between 0 and 255".

{% highlight python %}
x = np.random.normal(128, 1, (8, 8))
y = x.flatten()
{% endhighlight %}

The network thinks it sees a four.  I pat it on the head and give it a cookie.  So let's help it out a bit and tweak the image "toward" what it thinks it sees.

{% highlight python %}
def feedback(vector, weights, direction):
    new_vector = []
    for idx, x in enumerate(vector):
        new_vector.append(x + (weights[idx] * direction))
    return new_vector
{% endhighlight %}

Basically undoing the error correction from the learning run, but instead of *correcting* the weights, we adjust the pixel intensities of the input vector.  And then feed that back in.  And again.  Until it seems to settle down.

![Gaussian Noise](/images/gauss_noise.png)
![Dreamt 4](/images/dream4.png)

And it did always settle.

Picking how to adjust the image was completely arbitrary.  In the end I settled on adding to the intensity of the pixels heavily represented in the neuron that first fired, and subtracting (a much smaller portion) of the pixels that were heavily correlated with the neurons that didn't fire.

Now playing with the hyper-parameters changed the outcomes in various directions, but most settled around this output.

It turns out that the final outputs of this process look very similar to visual representations of the weight sets as an input vector.  Now this is hardly surprising given the linear nature of this single layer network.  So the next step is going to require a more complicated neural network.  So, over here on the drawing board ... oh wait, that is for another post.

Until then, sweet dreams little perceptron.

Cheers.