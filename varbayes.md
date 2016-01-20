
Variational Inference via the GMM
=================================

Applying variational inference can be kind of complicated, especially with
new problems. This tutorial has the aim of explaining the different options
in variational inference and where to apply them. Specifically, we're going
to focus on different ways to deal with the case of the mixture of
multivariate Gaussians. Hopefully, just working with the same model in
different ways illustrates the differences between the methods in a useful
way.

Specifically, we're going to cover:

* Variational Bayes with closed form updates.
* Stochastic Variational Inference
* Black-box Variational Inference
* What the heck is amortised inference?

What we're not going to cover:

* What is the variational free energy?
* Why do we want to minimize the KL divergence?
* Why should I use the forward versus the reverse KL divergence?

Prerequisites
-------------

These are described better than I could ever do [on Metacademy][pre].

Variational Bayes
-----------------

The best resource for a complete example of variational Bayes applied to
the Gaussian mixture model is [Chris Bishop's book: Pattern Recognition and
Machine Learning][bishop]. In section 10.2 he covers a Bayesian Gaussian
mixture model, complete with priors on all the parameters, and shows how an
iterative solution is derived with variational methods. There are various
exercises with solutions available.

This is a programming exercise. Given the equations for updates in Bishop's
example, how do we actually code up the mixture model (and get it to work)?
There are some subtleties which you might not expect. Luckily, we have a
notebook ready for you to start working on in the browser [right
here][vbinder] (although, it's a little flaky).

Stochastic Variational Inference
--------------------------------

Unsure what to do with this at the moment.

Black-Box Variational Inference
-------------------------------

If you've already studied all the stuff above, this is where I'm going to
bring together some things you've hopefully not looked into yet.

(Follow with exercises covering black box with parameterised distributions
and amortised inference).

(Also include links to Bayesian neural network in less than 100 lines).

[pre]: https://www.metacademy.org/graphs/concepts/variational_inference
[bishop]: http://research.microsoft.com/users/cmbishop/PRML/index.htm
[vbinder]: http://mybinder.org/repo/gngdb/vbgmm
