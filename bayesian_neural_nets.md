# Bayesian neural networks

Bayesian neural networks differ from their non-Bayesian counterparts in that they take into account uncertainty in their parameters when making predictions. There are two major issues when applying Bayesian neural networks in practice:

  - how to do inference
  - how to construct priors

In this session, we're going to discuss the above, together with some motivation for (and against) using Bayesian neural networks in practice.

## Prerequisites and suggested reading

I will assume that everybody is familiar with the following:

  - traditional (non-Bayesian) neural networks
  - Hamiltonian Monte Carlo --- see Matt's talk
  - Stochastic Variational Inference --- see Gavin's talk
    
In preparation, please read [chapter 41 of MacKay's ITILA](http://www.inference.phy.cam.ac.uk/mackay/itprnn/ps/492.503.pdf). It discusses Bayesian learning for a single neuron, which is qualitatively the same as for a full neural network. It is short and fairly straightforward to read.

## Plan for the meeting

### Introduction & motivation

We'll see what a Bayesian neural network is and how it differs from a non-Bayesian one. We'll also discuss some motivation for being Bayesian with neural networks, and some reasons why it might not be a good idea after all.

### How to do inference?

We will look at two ways of solving the inference problem in Bayesian neural networks, and discuss their relative advantages and disadvantages. These are *Hamiltonian Monte Carlo* and *Stochastic Variational Inference*, exactly as presented by Matt and Gavin. We will see demos of both techniques on a toy problem, with practical tips on how to get them to work.

### How to construct priors?

We will look at priors from the point of view of the functions they induce. We'll review (with proof) the classic result that a Bayesian neural network with a single hidden layer converges to a Gaussian process as the number of hidden units grows, for a certain choice of prior. We'll use that to gain some insight on how the choice of prior affects the network's function. This part of the discussion will be broadly based on chapter 2 of [Radford Neal's PhD thesis](http://www.cs.toronto.edu/~radford/ftp/thesis.pdf). This is seriously challenging material, so I can only promise a high level overview.

## Classic resources for Bayesian neural networks

Substantial work on Bayesian neural networks was done by David MacKay and Radford Neal during their PhDs. Their theses are the de facto references on the subject.

  - David MacKay's PhD thesis, [Bayesian Methods for Adapted Models](http://www.inference.phy.cam.ac.uk/mackay/thesis.pdf) was published in 1991. To solve the inference problem, MacKay used analytical approximations to the posterior, mainly Gaussian approximations based on the Laplace method.
  - Radford Neal's PhD thesis [Bayesian Learning for Neural Networks](http://www.cs.toronto.edu/~radford/ftp/thesis.pdf) was published in 1994. Neal made two major contributions:
    - Firstly, he studied from a theoretical perspective the properties of the network's function that different choices of prior give rise to. To do this, he pointed out the connection between Bayesian neural networks and Gaussian processes. He also considered priors for which the Bayesian neural network does not converge to a Gaussian process.
    - Secondly, he was the first to introduce the use of Hamiltonian Monte Carlo for solving the inference problem. Before him, Hamiltonian Monte Carlo (proposed in 1987) was little known outside physics.

After Neal's work, people realized that with Gaussian processes they could analytically do Bayesian neural networks with infinite capacity, and research turned into this direction instead. See for example, [Computing with Infinite Networks](http://papers.nips.cc/paper/1197-computing-with-infinite-networks.pdf) by Chris Williams in 1996, where he derives analytically the covariance functions induced by two different types of non-linearities, namely Gaussian and probit.

## Other ways to do Bayesian neural networks (or at least sort of...)

Other than the classic approaches, there are some modern, and even more approximate, ways of doing Bayesian neural networks, some of which are listed here.

  - [Bayesian Dark Knowledge, 2015](http://papers.nips.cc/paper/5965-bayesian-dark-knowledge.pdf). Using Monte Carlo to solve the inference problem is expensive at prediction time. This paper proposes training a single (non-Bayesian) neural network to mimic the Monte Carlo predictions of a Bayesian neural network. That way, the prediction cost is "compiled away" and the mimicking network can be used instead at prediction time.
  - [Scalable Bayesian Optimization Using Deep Neural Networks, 2015](http://machinelearning.wustl.edu/mlpapers/paper_files/icml2015_snoek15.pdf). Instead of being Bayesian about the whole network, be Bayesian about the last layer only. That way we get Bayesian linear regression, which is analytically tractable, on top of a traditional neural network.
  - [Variational Dropout and the Local Reparameterization Trick, 2015](http://papers.nips.cc/paper/5666-variational-dropout-and-the-local-reparameterization-trick.pdf) and [Dropout as a Bayesian Approximation, 2015](http://arxiv.org/pdf/1506.02142v4.pdf). Both of these argue that dropout can be seen as a (rather crude) variational approximation to a Bayesian neural network. Their proposal is: train a neural network with dropout, at prediction time drop out connections at random to get sample predictions, and use the variance of sample predictions to measure uncertainty.

