---
layout: post
title: "My First Post: The Yard-Sale Model"
date: 2026-04-02
categories: physics
---

There is an interesting problem that asks the following question: suppose you have N objects and you
randomly pair them two at a time. Each object contains some amount of "wealth", "energy", or whatever
you want to call it.

The algorithm proceeds as follows. Randomly select and pair all N objects into groups of two. Flip a
fair coin. Whichever object wins the coin toss receives a fixed fraction of the poorer object's wealth.

For a concrete example: Object A has 100 units and Object B has 50 units. Object A wins the toss and
receives 10% of Object B's energy. At the end of this trial, Object A has 105 units and Object B has
45 units. You then continue running trials an arbitrary number of times.

The question you want to answer is: what is the resulting distribution of energy across the system?
The proposed distribution is the Boltzmann distribution:

\\[P(E) = \frac{1}{Z} e^{-E / k_B T}\\]

where \\(P(E)\\) is the probability that a given object has energy \\(E\\), \\(k_B\\) is the Boltzmann
constant, \\(T\\) is the temperature of the system, and \\(Z\\) is the partition function, a normalizing
constant that ensures all probabilities sum to 1.

The intuition for why this distribution arises is that in systems where energy is exchanged randomly
between many agents, the most statistically likely outcome is an exponential distribution of energy.
High energy states are exponentially less probable than low energy states, meaning most objects will
end up with very little energy while a small number accumulate a disproportionate share.

There are other modifications you can make to this system. For example, you can place each agent on a
network and have them interact with each other according to the rules of the network.

There are many ways to choose a network. A network that follows a power law would mimic the way humans
interact with each other, so we can choose the Barabasi-Albert network for our first choice, for
reasons that I will explain in a much longer post. The Barabasi-Albert model grows a network
iteratively: starting from a small seed graph, at each time step a new node is added and connects to
\\(m\\) existing nodes with probability proportional to their current degree:

\\[\Pi(k_i) = \frac{k_i}{\sum_j k_j}\\]

where \\(k_i\\) is the degree of node \\(i\\). This preferential attachment mechanism means that
highly connected nodes accumulate even more connections over time, producing a degree distribution
that follows a power law:

\\[P(k) \sim k^{-3}\\]

The next question you might ask is: what happens to the resulting distribution as the number of trials
goes to infinity with these changing conditions in the simulation?

And finally, before my next post explaining these things in much greater detail: what happens when you
add energy to the system? There are two (probably more than that) ways to add energy: deterministically
or stochastically.

My proposed contribution to the system is a stochastic energy injection term derived from an adapted
Ornstein-Uhlenbeck process. The change in wealth of agent \\(i\\) at time \\(t\\) is given by:

\\[\Delta w_i(t) = \left( x_0 e^{-\theta t} + \mu(1 - e^{-\theta t}) + \frac{\sigma}{\sqrt{2\theta}}\sqrt{1 - e^{-2\theta t}} \cdot Z \right) W(t) \frac{w_i^{\lambda}(t)}{\sum_{j=1}^{N} w_j^{\lambda}(t)}\\]

where \\(x_0\\) is the initial energy level, \\(\mu\\) is the long-run mean to which the process
reverts, \\(\theta\\) controls the speed of mean reversion, \\(\sigma\\) is the volatility of the
stochastic term, \\(Z\\) is a standard normal random variable, \\(W(t)\\) is the total energy in the
system at time \\(t\\), and \\(\lambda\\) controls how energy injection is distributed across agents
relative to their current wealth.

The parameter \\(\lambda\\) deserves special attention. When \\(\lambda = 0\\), energy is injected
uniformly across all agents regardless of their current wealth, which acts as an equalizing force
and pushes the system away from condensation. When \\(\lambda = 1\\), energy is injected
proportionally to each agent's current wealth, meaning richer agents receive more energy and the
rich get richer. For \\(\lambda > 1\\), this effect is superlinear: wealth concentration accelerates
and the system tends toward condensation much faster, where a single agent accumulates nearly all
the energy. For \\(\lambda < 0\\), the injection is anti-proportional, actively redistributing energy
toward poorer agents and counteracting the condensation that the yard-sale mechanism would otherwise
produce. In this sense, \\(\lambda\\) acts as a policy parameter: it encodes assumptions about how
energy or wealth enters a system.

In my next post I will elaborate on what has been hinted at here: the economic implications of a
physical system with no readily apparent applications in stereotypical statistical mechanics.
