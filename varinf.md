
Variational Bayes
=================

This tutorial basically just comes from me seeing some really short code for 
doing a kind of variational inference on arbitrary latent variable models called
"Black Box Variational Inference". I thought it would be interesting to contrast
directly with the analytic method of deriving iterative updates.

Specifically, we're going to cover:

* Variational Bayes with closed form updates.
* Black-box Variational Inference

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

Black-Box Variational Inference
-------------------------------

[Black-Box Variational Inference][bbox] is what it sounds like: [given any
probabilistic model, we'd like to perform inference in it without having to do any
algebra][bboxvid]. Which seems useful for a lot of problems, but how does it
work? Well, recently a paper came out called [Black Box Stochastic Variational
Inference in Five Lines of Python][fivelines], and this paper is a lot of fun.
Black-box stochastic variational inference depends on having stochastic
gradients of the ELBO, and this paper really exploits automatic differentiation
with [Autograd][] (their own library).

The exercise is to take apart the [Black Box SVI example][autoex] in a
notebook and explain what's happening. Then, build on this to fit a Gaussian
Mixture model with a differentiable parameterised model, using the classic Iris
dataset. Worked example is [in this repository][bbsvirepo], please submit pull 
requests if you come up with something interesting!

[pre]: https://www.metacademy.org/graphs/concepts/variational_inference
[bishop]: http://research.microsoft.com/users/cmbishop/PRML/index.htm
[vbinder]: http://mybinder.org/repo/gngdb/vbgmm
[bbox]: http://jmlr.org/proceedings/papers/v33/ranganath14.pdf
[bboxvid]: https://www.youtube.com/watch?v=QugI-7QacEY
[fivelines]: http://people.seas.harvard.edu/~dduvenaud/papers/blackbox.pdf
[autograd]: https://github.com/HIPS/autograd
[autoex]: https://github.com/HIPS/autograd/blob/master/examples/black_box_svi.py
[bbsvirepo]: https://github.com/ANC-PIGlets/bbsvi
