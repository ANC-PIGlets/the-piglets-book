

Autoencoders
============

Autoencoders are a neural networks where the output _is the input_. This is
a tutorial covering how to write an autoencoder from scratch and train it
on a small dataset. This involves writing a simple [feedforward neural
net][feedforward], and using [automatic differentiation][auto] to run
[stochastic gradient descent][sgd] for training. There will also be some
questions for understanding.

Reading
=======

The best resource for introductory reading on autoencoders is the [relevant
chapter][aechapter] from the [Deep Learning book by Goodfellow, Bengio and
Courville][book]. You don't have to read the whole thing, but it's a good
resource for reference.

There is also [a talk from the 2015 Deep Learning summer school about
autoencoders][talk] by Pascal Vincent that may be useful to you, but is
not necessary for the tutorial.

Tutorial
========

The tutorial is hosted on GitHub in [this repository][repo], using Jupyter
notebooks and autograd for the automatic differentiation.

[feedforward]: https://www.metacademy.org/graphs/concepts/feed_forward_neural_nets
[auto]: https://www.metacademy.org/graphs/concepts/automatic_differentiation
[sgd]: https://www.metacademy.org/graphs/concepts/stochastic_gradient_descent
[book]: http://www.deeplearningbook.org/
[aechapter]: http://www.deeplearningbook.org/contents/autoencoders.html
[talk]: http://videolectures.net/deeplearning2015_vincent_autoencoders/
[repo]: https://github.com/ANC-PIGlets/autoencoders
