

# Bayesian Optimization #
__Prerequisites__

Most Bayesian Optimization techniques use Gaussian Processes to form their
acquisition functions.  As Gaussian Processes are not focused on in most of
the machine learning courses at Edinburgh, you might want to revise
[Gaussian Processes](gaussian-processes.md).

Also, if you would like to prepare with a fast introduction, including a
_long list_ of interesting applications then you may want to watch 
[Nando de Freitas' lecture on Bayesian Optimization](https://youtu.be/YB64VoGQsK8).

__Overview__

Bayesian optimization (BO) is a modern approach to global optimization of blackbox-style functions. For the most part, BO techniques assume that the target function is expensive to evaluate and, therefore, seek to minimize the number of queries made during the process of optimization.

Here, our goal will be to provide a high-level survey of key concepts in Bayesian optimization, specifically as pertains to notions of automated Machine learning (autoML).


__Suggested Material__

Paper   | Contents 
:----------:|:----------------------
[Taking the Human Out of the Loop:<br> A Review of Bayesian Optimization](https://www.cs.ox.ac.uk/people/nando.defreitas/publications/BayesOptLoop.pdf) | Broad-spectrum discussion of BO and its<br> various applications
[Practical Bayesian Optimization of<br> Machine Learning Algorithms](https://papers.nips.cc/paper/4522-practical-bayesian-optimization-of-machine-learning-algorithms.pdf)| Presentation of BO in the specific context<br> of Machine Learning
[A Taxonomy of Global Optimization<br> Methods Based on Response Surfaces](http://www.ressources-actuarielles.net/EXT/ISFA/1226.nsf/0/e7dc33e4da12c5a9c12576d8002e442b/$FILE/Jones01.pdf)| In-depth review of techniques for global<br> optimization; for those interested in theory
[Gaussian Processes: Quick Intro](http://www.robots.ox.ac.uk/~mebden/reports/GPtutorial.pdf) | Gaussian processes for ML 10 minutes

__Demonstrations__

Demonstrations were done using [MOE][] docker example.

__More Interesting Talks__

The 2015 [workshop on Gaussian Processes for Global Optimisation][gpss] has
some good talks on newer work involving Bayesian optimization, including a
talk by Nando de Freitas on key challenges.

[moe]: https://github.com/Yelp/MOE
[gpss]: http://gpss.cc/gpgo15/
