---
title: Machine Learning on $10 a Day
categories:
- Machine Learning
- Perceptron
- Python
---

Initial foray into machine learning was an implementation of a basic Perceptron (image) on the Handwritten Digits sets from [scikit-learn](http://scikit-learn.org).  The 8x8 pixel images in grayscale number 1797, and have been adjusted for skewing.  
  
[![Handwritten digits](/images/digits.png)](http://scikit-learn.org)*from scikit-learn*


 The first completed test resulted in a 75% success rate on guessing the correct value of image from the set of data the trained algorithm had not seen.  Adjusting the hyperparameters, it turned out that with this model, it was over-fitting the data and adjusting the number of training cases down, was inversely proportional to the success rate.  The sweet spot seemed to be around 950 training cases and leaving the 1200 for the guessing. 
   The next hyperparmeter tackled was the number of epochs to run during the learning phase.  Again this turned out to be an inverse relationship with the success ratio after a certain point.  And the sweet spot settled around 250 epochs.  This, of course, introduced my laptop to demands it had never faced and is painting the next adventure in this journey, analysing the performance of the script to find efficiencies (but that is for tomorrow).
     Now with the success rate well into the low 80's, I moved into a dimension on the perceptron and rewrote the decision function, to a logistic function.

![Step Function](/images/step.png)![Sigmoid Function](/images/sigmoid.jpg)

   By having a more specific error in each neuron of the net, we are able to generate the error across the entire net at each epoch and further refine the weight matrix on each neuron by a "universal" error adjustment attained from a modified gradient descent function.  

![Gradient Descent](/images/gradient.png)

This brought the success rate up to 92%.  But this is when [Thunder](https://thundershiviah.github.io) (my mentor in this entire process) asked about validation of the data.  ACK! What's that, okay, okay, I'll do it.  A quick hack and slash on the dataset and voilá a second bit of data (really just peeled of the end of the original set), and away the run goes again.  But wait!  This is weird.  The main test set comes back with a 94% success rate.  And the validation set? ... 88%  Wait, what?  Okay, so something is radically different in that final chunk of data.  A quick spin through scikit-learn at Thunder's advice, and I come up with utils.resample() a method that reshuffles the data, but allows for one to maintain the relationships of the internal data.  Voilá, part deux, new data set.  And away we go again.  95%+ !! on both the test set and the validation set.  I'm going to call that a win.  Thanks and good night.  No really, I'll be here all week.  Now to make my chicken scratch legible.