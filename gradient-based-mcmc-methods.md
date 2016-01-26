# Gradient based MCMC methods

The aim of this session will be to introduce MCMC methods for performing inference with probability densities over real-valued variables which make use of gradient of the target density - in particular Hamiltonian dynamics based methods though I will also briefly go over (Langevin) diffusion based methods. I will aim to cover the basic theory and show how to implement the methods on a few toy examples.

## Prerequisites

I will assume everyone is comfortable with the fundamentals of Markov Chain Monte Carlo, in particular it would be good to make sure you are familiar with all of the following:

  * definitions of Markov property, transition operator, invariant distribution
  * detailed balance condition for discrete and continuous state-space Markov chains
  * general idea of ergodicity and the need for it (exact mathematical details won't be important for what I'll cover)
  * constructing Markov chains by composing multiple transition operators
  * Metropolis-Hastings acceptance rule
  
The [MCMC part of MLPR](http://www.inf.ed.ac.uk/teaching/courses/mlpr/2015/slides/13_mcmc.pdf) covered all of this so if you have done MLPR (and can remember it!) that should be sufficient.

### Resources for reviewing MCMC

For basic Markov chain terminology and concepts section 29.6 of [Mackay ITILA](http://www.inference.phy.cam.ac.uk/mackay/itprnn/ps/356.384.pdf) or section 11.2.1 of [Bishop PRML](http://research.microsoft.com/en-us/um/people/cmbishop/PRML/) are quite compact introductions. Iain Murray's NIPS tutorial [slides and link to video here](http://homepages.inf.ed.ac.uk/imurray2/teaching/15nips/) is also a good though more wide ranging introduction.

The Metacademy pages for [Markov Chain Monte Carlo](https://www.metacademy.org/graphs/concepts/markov_chain_monte_carlo) and [Hamiltonian Monte Carlo](https://www.metacademy.org/graphs/concepts/hamiltonian_monte_carlo) are also good for breaking down the prerequisites and for pointers to resources.

## Hamiltonian Monte Carlo (HMC)

There are good short introductions to Hamiltonian Monte Carlo (originally introduced as [Hybrid Monte Carlo by Duane et al.](http://www.sciencedirect.com/science/article/pii/037026938791197X)) in section 30.1 of [Mackay ITILA](http://www.inference.phy.cam.ac.uk/mackay/itprnn/ps/387.412.pdf) and section 11.5 of [Bishop PRML](http://research.microsoft.com/en-us/um/people/cmbishop/PRML/).

I will aim to cover
  * idea of introducing auxilliary momentum variables
  * exact Hamiltonian dynamics and why they are a useful basis for a MCMC method
  * how to approximate Hamiltonian dynamics in discrete time
  * role of the mass matrix and injection of noise via the momentum variables
  * pulling everything together in a simple `python` implementation [[repository here](https://github.com/ANC-PIGlets/gradient-based-mcmc)].
  
Other useful resources if you want to look at this in more depth:

  * [MCMC using Hamiltonian Dynamics, Neal (2011)](http://arxiv.org/pdf/1206.1901.pdf) - a very detailed tutorial which goes in to a lot more depth than the textbook introductions and covers a lot of practical implementation details and extensions to the basic method.
  * [A General Metric for Riemannian Manifold Hamiltonian Monte Carlo, Betancourt (2012)](http://arxiv.org/pdf/1212.4693v2.pdf) - introduction section covers basics of both standard ('Euclidean manifold') HMC and the more complex Riemannian Manifold HMC (RMHMC) which allows the use of a position-dependent mass matrix to aid exploration of distributions with locally varying curvature.
  * [Riemann Manifold Langevin and Hamiltonian Monte Carlo, Girolami and Calderhead (2010)](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.221.6963&rep=rep1&type=pdf) - paper which introduced RMHMC, which has a much more detailed but also I felt more mathematically demanding description of the method than the above paper. This also provides a nice general introduciton to HMC and the Metropolis Adjusted Langevin Algorithm (see below).

## Diffusions and the Metropolis-adjusted Langevin algorithm (MALA)

Another very related approach to using gradient information in MCMC is in Langevin diffusion based methods.

In [Mackay ITILA pg. 496](http://www.inference.phy.cam.ac.uk/mackay/itprnn/ps/492.503.pdf) this is intuitively summed up as gradient descent with noise. In slightly more detail, it is a simple time discretisation of a continuous time Langevin diffusion which has the target distribution as the equilibrium state, with a Metropolis-Hastings accept step used to correct for the bias introduced by the discretisation (hence the often used nomenclature Metropolis-adjusted Langevin algorithm (MALA)). It can be seen as equivalent to performing HMC with proposed moves which take only a single gradient descent step.

## Stochastic gradient methods

Following on from the introduction to MALA if there is time I may very briefly mention the ideas behind stochastic gradient based MCMC methods such as in the papers [Bayesian Learning by Stochastic Gradient Langevin Dynamics, Welling and Teh (2011)](http://www.icml-2011.org/papers/398_icmlpaper.pdf) and [Stochastic Gradient Hamiltonian Monte Carlo, Chen et al. (2014)](http://arxiv.org/pdf/1402.4102v2.pdf). I suspect that we might not get that far however!
