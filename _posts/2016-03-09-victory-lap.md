---
title: Victory Lap
categories:
- Machine Learning
- Neural Net
- Convolutional Net
- Artificial Intelligence
- MNIST
- Kaggle
---

![Kaggle Leaderboard](/images/kaggle_leader.png)

This took longer than it should have.  And though this is an already "won" race, I'm going to take a moment to enjoy this and celebrate by ... moving on.  While I did find the result I was looking for, it came in about around a mystery that I never solved.


All of my early attempts at [MNIST](http://yann.lecun.com/exdb/mnist/) recognition went about as expected, with feed-forward fully connected nets yielding accuracies in the low 90's, and with excessive (perhaps [obsessive](https://imgur.com/bSiNEq6)) hyperparameter tuning, never exceeding 95% accuracy.  They do say that last 5% of MNIST are the most difficult.

But others were obviously getting better results, so I kept at it.  Convolutional Networks were the obvious next step.  Modeled after the retina and designed originally for image recognition convnets are uniquely suited, and as MNIST is the "Hello World" for everything else, let's give it a shot.

While [Finnegan](http://uglyboxer.github.io/capstone/) was built from the ground up in raw Python, I was not quite ready to dive into a raw Convolutional Net, but Python is your friend, and its community by extension brings you a lot of very helpful friends.  [Theano](http://uglyboxer.github.io/capstone/) is the goto package in Neural Networks in Python for its robust handling of all of the linear algebra required for a neural net.  It also the key to distributing the computations to the GPU given its support for the [CUDA](https://www.nvidia.com/object/cuda_home_new.html) api.  While basic convnets can run on a cpu, even a modest convnet will quickly scale past the capabilities of your laptop and most-likely your patience (my cpu fan makes funny noises now).

Theano can be a bit cumbersome though, so again, community to the rescue, I cracked open [Keras](http://keras.io/).  A wrapper for Theano to make building a model as simple as stacking a sandwich. 

```
model = Sequential()

model.add(Convolution2D(20, nb_conv, nb_conv,
                        border_mode='valid',
                        input_shape=(1, img_rows, img_cols)))
model.add(Activation('relu'))
model.add(Convolution2D(60, nb_conv, nb_conv))
model.add(Activation('relu'))
model.add(Convolution2D(120, nb_conv, nb_conv))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(nb_pool, nb_pool)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(150))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(nb_classes))
model.add(Activation('softmax'))
EarlyStopping(monitor='val_loss')

model.compile(loss='categorical_crossentropy', optimizer='adadelta')
```

A couple of convolutional pieces of ham, a topping of fully-connected forward piece of cheese and out came a model.  Straight to 97% accurate on Kaggle.  Better, but still nothing to write home about.  So where to look next?  The data itself!  Convolutional Nets by design are robust to perturbations in the data, so let's perturb it!

Conveniently, this has already been done and posted: [AffNIST](http://www.cs.toronto.edu/~tijmen/affNIST/).  32 random affine transformations of the original 60,000 MNIST examples.  Each made by padding the 28x28 pixel image with 0's up to 40x40 and then sliding, shrinking, rotating the image around that space. And you end up with:
![Affnist](/images/affnist.png)

But now we have almost 2 million training samples and each is over twice the size of the original!  Handling that much data is the lower edge of troublesome (on a laptop anyway).  But luckly we have AWS and they have GPU's!  The Affnist set is stored in Matlab file format but (Stackoverflow)[https://stackoverflow.com/questions/7008608/scipy-io-loadmat-nested-structures-i-e-dictionaries] of course has the answer.

Here is where the sad mystery starts.  I started by just using part of the training set. I only used the first half of the dataset, and the model converged as expected.  Now the test set was still the smaller 28x28 image so I had to pad those with 0's around the edges to get a 40x40 test sample.  But on showing them to the model I never got better than 60% accuracy.  Something must be wrong with the data, either the test set, the train set, I said to myself.  Some variation introduced between the test and train sets that the model was picking up on.  Something.  I tore my hair out picking at each set trying to find something.  But 60% was just too weird.  Way better than random chance, so it was seeing something.  But it was still clearly off the base I was hoping for.  Soon life and work intruded on my pursuit, I was forced to shelve the project.

A few weeks go by and my friend [Hannes](http://hanneshapke.github.io/) and I are planning a lecture on Neural Nets for [Hack Oregon University](http://www.hackoregon.org/) and he has been using [nolearn](https://pythonhosted.org/nolearn/) for his projects.  I pushed and kicked for a day or so, but it made sense to present one set of tools to the students, so I caved.  And am I glad I did!  nolearn is really slick.  Actually a wrapper for [Lasagne](https://lasagne.readthedocs.org/en/latest/index.html), which itself is another wrapper for Theano.  A similar sandwich style of stacking, and voilà, your network.

After the class was over and had this handy dandy tool, I decided to dust off the AffNIST and give it another go.  

```
def CNN(n_epochs):
    net1 = NeuralNet(
        layers=[
        ('input', layers.InputLayer),
        ('conv1', layers.Conv2DLayer),
        ('pool1', layers.MaxPool2DLayer), 
        ('conv2', layers.Conv2DLayer),
        ('hidden3', layers.DenseLayer),
        ('dropout3', layers.DropoutLayer),
        ('output', layers.DenseLayer),
        ],

    input_shape=(None, 1, 40, 40),
    conv1_num_filters=20,                     
    conv1_filter_size=(5, 5), 
    conv1_nonlinearity=lasagne.nonlinearities.rectify,
        
    pool1_pool_size=(2, 2),
        
    conv2_num_filters=60, 
    conv2_filter_size=(5, 5),    
    conv2_nonlinearity=lasagne.nonlinearities.rectify,
        
    hidden3_num_units=500,
    output_num_units=10,
    dropout3_p=0.5,
    output_nonlinearity=lasagne.nonlinearities.softmax,

    update=nesterov_momentum,
    update_learning_rate=0.01,
    update_momentum=0.9,

    max_epochs=n_epochs,
    verbose=1,
    )
    return net1
print(test.shape)
cnn = CNN(10).fit(train,target) 
```

Two important things.  *NORMALIZE* the data.  And don't forget the set Theano flags to use the gpu:

```
$ THEANO_FLAGS='floatX=float32,device=gpu0,lib.cnmem=1'  python <myscript>.py
```

I won't pretend that it worked straight out of the box, but it worked on the second try (once I padded the test set in the right order).  It worked.  And way better than I hoped.  99.22%.  Not state of the art, but I'm gonna call it a win.

Now, I never figured out what the difference was.  Some innate difference between Keras and nolearn, perhaps?  Most likely operator error, but I came out the other side no worse for the wear, so I'm good with that.

"Hello World" I bid you adieu.  On to the next project!

Until next time, Happy Learning!
