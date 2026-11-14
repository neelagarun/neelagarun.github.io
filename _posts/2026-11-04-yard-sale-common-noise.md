---
layout: post
title: "The Yard-Sale Model with a Common Shock: When the Wealth Distribution Becomes Random"
date: 2026-11-04
categories: [statistics, physics]
---

During my master's at BU I worked on statistical mechanics problems, and some of that work touched the
Yard-Sale model of wealth exchange. This post is my own exploration of one extension of it, and none of it
describes results from that research. The question is what happens to the model when every agent is also
hit by the same random shock at the same time, the way a stock market crash hits everyone who owns stocks.
In the standard model, the wealth distribution evolves by a deterministic partial differential equation.
With a common shock, the distribution itself becomes random, and the equation becomes a stochastic partial
differential equation. I'll derive both equations and then work out when the common shock affects inequality and in which direction.

The Yard-Sale model is a population of \\(N\\) agents with wealth \\(w\_1, \ldots, w\_N\\). At each step, two agents
are paired at random and trade. The amount at stake is a fixed fraction \\(\beta\\) of the poorer agent's wealth,

\\[\Delta = \beta \min(w\_i, w\_j)\\]

and a fair coin decides who wins it. The winner gains \\(\Delta\\) and the loser loses \\(\Delta\\). The rule is meant to
capture the fact that in a voluntary exchange, neither side can lose more than the poorer side has.

Every trade is fair in expectation. Each agent gains or loses \\(\Delta\\) with probability one half, so the expected
change in wealth is zero for both. But the trade isn't fair in a multiplicative sense. Suppose \\(w\_i \leq w\_j\\). The
poorer agent's wealth is multiplied by \\(1 + \beta\\) or \\(1 - \beta\\), so the expected change in its logarithm is

\\[\mathbb{E}[\Delta \log w\_i] = \frac{1}{2} \log(1 + \beta) + \frac{1}{2} \log(1 - \beta) = \frac{1}{2} \log(1 - \beta^2) \approx -\frac{\beta^2}{2}\\]

The richer agent's wealth changes by \\(\pm \beta w\_i\\), which is a fraction \\(\beta r\\) of its wealth, where
\\(r = w\_i / w\_j \leq 1\\). Its expected log change is

\\[\mathbb{E}[\Delta \log w\_j] = \frac{1}{2} \log(1 - \beta^2 r^2) \approx -\frac{\beta^2 r^2}{2}\\]

which is smaller in magnitude. Both sides lose expected log wealth, but the poorer side loses more, because it
bets a larger fraction of what it has. This is the same volatility drag that makes a stock that goes up 20% and
then down 20% end below where it started. Over many trades, an agent's wealth is a product of many random factors,
and its typical value is governed by the expected logarithm, which can be negative even when the expected value stays put. In the model, poorer agents
have systematically worse expected log growth, and the result is condensation: with no other forces, wealth
concentrates in fewer and fewer hands.

I simulated 2,000 agents starting with equal wealth and \\(\beta = 0.2\\), pairing all of them at random once per step.
The Gini coefficient, which is zero for perfect equality and approaches one when a single agent holds everything,
was 0.65 after 100 steps, 0.94 after 1,000, 0.986 after 5,000, and 0.996 after 20,000. Over that time, the single
richest agent's share went from 0.5% to 17% and was still rising. The expected log change for the poorer agent at
\\(\beta = 0.2\\) is \\(\frac{1}{2} \log(0.96) \approx -0.0204\\), close to the approximation \\(-\beta^2 / 2 = -0.02\\).

To get an equation for the whole distribution, take the limit where trades are small and frequent. Suppose each
agent trades at rate one with a partner drawn from the current wealth distribution, with density \\(\rho(w, t)\\)
normalized to integrate to one. In a short time \\(dt\\), an agent with wealth \\(w\\) makes on average \\(dt\\) trades. Each
trade changes its wealth by a mean-zero amount with variance

\\[\mathbb{E}\big[ \Delta^2 \big] = \beta^2 \, \mathbb{E}\_{w\'}\big[ \min(w, w\')^2 \big] = \beta^2 B(w), \qquad B(w) = \int\_0^\infty \min(w, w\')^2 \, \rho(w\') \, dw\'\\]

where \\(w\'\\) is the partner's wealth. So over \\(dt\\) the wealth change has mean zero and variance
\\(\beta^2 B(w) \, dt\\), and in the limit of many small trades the agent's wealth follows the diffusion

\\[d w = \beta \sqrt{B(w)} \, d W\\]

with \\(W\\) a Brownian motion. The function \\(B\\) can be split according to whether the partner is poorer or richer,

\\[B(w) = \int\_0^w w\'^2 \rho(w\') \, dw\' + w^2 \int\_w^\infty \rho(w\') \, dw\'\\]

For an agent much poorer than everyone else, almost every partner is richer, the second term dominates, and
\\(B(w) \approx w^2\\). Then \\(d w \approx \beta w \, d W\\), which is geometric Brownian motion, with the volatility drag of
\\(-\beta^2 / 2\\) in log wealth found above. For an agent much richer than everyone else, the first term dominates and
\\(B(w) \to \mathbb{E}[w\'^2]\\), a constant. The rich agent's wealth wanders additively, with no drag in log terms.

The density follows from the diffusion by a standard argument with a test function. Take any smooth \\(\varphi\\) and
apply Itô's formula to \\(\varphi(w)\\):

\\[d \varphi(w) = \varphi\'(w) \, \beta \sqrt{B(w)} \, d W + \frac{1}{2} \varphi\'\'(w) \, \beta^2 B(w) \, dt\\]

Averaging over all agents, the Brownian term has mean zero, and the average of \\(\varphi\\) over the population is
\\(\int \varphi \rho \, dw\\), so

\\[\frac{d}{dt} \int \varphi(w) \rho(w, t) \, dw = \int \frac{\beta^2}{2} B(w) \, \varphi\'\'(w) \, \rho(w, t) \, dw\\]

Integrating by parts twice moves both derivatives onto \\(B \rho\\), with the boundary terms vanishing because
\\(\rho\\) decays at infinity and \\(B(0) = 0\\). Since the identity holds for every \\(\varphi\\),

\\[\partial\_t \rho = \gamma \, \partial\_w^2 \big( B(w) \rho \big), \qquad \gamma = \frac{\beta^2}{2}\\]

This is a Fokker-Planck equation, but a nonlinear one, because \\(B\\) depends on \\(\rho\\) itself. Equations of this kind,
where each particle's dynamics depend on the distribution of all the others, are called McKean-Vlasov equations.
Multiplying by \\(w\\) and integrating by parts twice shows that the mean is conserved:

\\[\frac{d}{dt} \int w \rho \, dw = \gamma \int w \, \partial\_w^2 (B \rho) \, dw = \gamma \Big[ w \, \partial\_w (B\rho) - B \rho \Big]\_0^\infty = 0\\]

Without anything else in the model, this equation has no steady state with a smooth density. Its solutions drift
toward the condensed state found in the simulation.

The standard way to stop condensation is redistribution. Suppose each agent pays a fraction \\(\chi\\) of its wealth per
unit time into a pot that is shared equally. An agent with wealth \\(w\\) then gains
\\(\chi (\bar{W} - w) \, dt\\), where \\(\bar{W}\\) is the average wealth, which is positive for agents below average and
negative above it. The diffusion becomes \\(d w = \chi (\bar{W} - w) \, dt + \beta \sqrt{B(w)} \, d W\\), the drift adds
a first-derivative term to the Fokker-Planck equation by the same test-function argument, and

\\[\partial\_t \rho = -\partial\_w \Big[ \chi (\bar{W} - w) \rho \Big] + \gamma \, \partial\_w^2 \big( B \rho \big)\\]

The mean is still conserved, since \\(\int \chi(\bar{W} - w) \rho \, dw = \chi(\bar{W} - \bar{W}) = 0\\). Now there is a steady
state. Write the equation as \\(\partial\_t \rho = -\partial\_w J\\), with the probability flux

\\[J = \chi (\bar{W} - w) \rho - \gamma \, \partial\_w (B \rho)\\]

In a steady state, \\(J\\) is constant in \\(w\\), and since it vanishes at \\(w = 0\\) it vanishes everywhere. Setting
\\(g = B \rho\\),

\\[\gamma \, g\'(w) = \chi (\bar{W} - w) \frac{g(w)}{B(w)} \quad \Longrightarrow \quad \frac{g\'}{g} = \frac{\chi}{\gamma} \cdot \frac{\bar{W} - w}{B(w)}\\]

which integrates to

\\[\rho(w) = \frac{C}{B(w)} \exp\!\left( \frac{\chi}{\gamma} \int\_{\bar{W}}^{w} \frac{\bar{W} - s}{B(s)} \, ds \right)\\]

with \\(C\\) fixed by normalization. This isn't yet a solution, because \\(B\\) depends on \\(\rho\\), but it says a lot about
the shape. Near zero, \\(B(s) \approx s^2\\), so the integral behaves like \\(\int \bar{W} / s^2 \, ds = -\bar{W} / s\\), and

\\[\rho(w) \approx \frac{C\'}{w^2} \exp\!\left( -\frac{\chi \bar{W}}{\gamma w} \right) \quad \text{as } w \to 0\\]

The exponential makes the density vanish very quickly at small wealth: redistribution keeps anyone from getting too
close to zero. At large wealth, \\(B(s) \to B\_\infty = \mathbb{E}[w^2]\\), the integrand becomes
\\((\bar{W} - s) / B\_\infty\\), and

\\[\rho(w) \approx C\'\' \exp\!\left( -\frac{\chi}{2 \gamma B\_\infty} (w - \bar{W})^2 \right) \quad \text{as } w \to \infty\\]

a Gaussian tail. That's a consequence of the trade rule, where the rich can only ever stake an amount bounded by their
partner's wealth, so their wealth moves additively.

To find the actual steady state I solved the formula by iteration: start with a guess for \\(\rho\\), compute \\(B\\), compute a
new \\(\rho\\) from the formula, and repeat until it stops changing. With \\(\beta = 0.2\\), so \\(\gamma = 0.02\\), with
\\(\chi = 0.01\\), and with \\(\bar{W} = 1\\), it converged in 34 iterations to a density with mean 0.999 and a Gini
coefficient of 0.480. The same parameters in the agent simulation, 5,000 agents per run, gave a steady-state Gini
of 0.478. The continuum equation describes the agent model to within a fraction of a percent. In that steady state,
the top 10% of agents hold about 33% of the wealth, the median agent has 0.65 times the average, and about 10% of
agents have less than a fifth of the average.

Now add the common shock. Suppose every agent also holds some of its wealth in a single risky asset whose return over
\\(dt\\) is \\(\mu \, dt + \sigma \, dB\_t\\), where \\(B\_t\\) is one Brownian motion shared by everyone. For now, let every
agent hold the same fraction, so that the shock adds \\(w (\mu \, dt + \sigma \, dB\_t)\\) to each agent's wealth. Agent
\\(i\\) then follows

\\[d w\_i = \chi (\bar{W} - w\_i) \, dt + \beta \sqrt{B(w\_i)} \, d W\_i + w\_i (\mu \, dt + \sigma \, dB\_t)\\]

where the \\(W\_i\\) are independent for different agents and \\(B\_t\\) is the same for all of them.

The derivation of the density equation changes in one place. Apply Itô's formula to \\(\varphi(w\_i)\\) again. The
quadratic variation now has two pieces, \\(\beta^2 B(w\_i) \, dt\\) from the trades and \\(\sigma^2 w\_i^2 \, dt\\) from the
shock, with no cross term because \\(W\_i\\) and \\(B\_t\\) are independent:

\\[d \varphi(w\_i) = \varphi\'(w\_i) \Big[ \chi(\bar{W} - w\_i) + \mu w\_i \Big] dt + \frac{1}{2} \varphi\'\'(w\_i) \Big[ \beta^2 B(w\_i) + \sigma^2 w\_i^2 \Big] dt + \varphi\'(w\_i) \beta \sqrt{B(w\_i)} \, d W\_i + \varphi\'(w\_i) \, \sigma w\_i \, d B\_t\\]

Now average over agents. With many agents, the idiosyncratic terms \\(d W\_i\\) average to zero by the law of large
numbers, since they're independent across agents. The common term doesn't, because every agent has the same
\\(d B\_t\\). So the empirical distribution of wealth, which I'll still write \\(\rho\\), becomes random, and
\\(\int \varphi \rho \, dw\\) satisfies

\\[d \int \varphi \rho \, dw = \int \Big\\{ \varphi\' \big[ \chi(\bar{W} - w) + \mu w \big] + \frac{1}{2} \varphi\'\' \big[ 2 \gamma B + \sigma^2 w^2 \big] \Big\\} \rho \, dw \, dt + \sigma \int \varphi\' \, w \, \rho \, dw \; dB\_t\\]

Integrating by parts in every term, once for each first derivative and twice for each second derivative,

\\[d \rho = \Big[ -\partial\_w \big( (\chi(\bar{W} - w) + \mu w) \rho \big) + \gamma \, \partial\_w^2 (B \rho) + \frac{\sigma^2}{2} \partial\_w^2 (w^2 \rho) \Big] dt - \sigma \, \partial\_w (w \rho) \, dB\_t\\]

This is the stochastic Fokker-Planck equation. Mathematically, \\(\rho\\) is the distribution of a single agent's wealth
conditional on the path of the common shock. The last term is called transport noise: at each instant the whole
distribution is stretched or squeezed along the wealth axis by a random amount. The \\(\frac{\sigma^2}{2} \partial\_w^2(w^2 \rho)\\)
term is its Itô correction, and the two have to appear together for the equation to be consistent. Multiplying by \\(w\\) and
integrating gives the dynamics of average wealth,

\\[d \bar{W} = \mu \bar{W} \, dt + \sigma \bar{W} \, dB\_t\\]

since the trades and the tax redistribute without creating or destroying wealth. Total wealth is a geometric Brownian
motion, as you'd expect when everyone holds the same asset.

The obvious question is whether this randomness affects inequality, and for proportional shocks the answer is no,
exactly. Inequality measures like the Gini coefficient don't depend on units, so they're functions of relative wealth
\\(x = w / \bar{W}\\). Its dynamics come from Itô's formula for a ratio:

\\[d \left( \frac{w}{\bar{W}} \right) = \frac{d w}{\bar{W}} - \frac{w \, d \bar{W}}{\bar{W}^2} - \frac{d \langle w, \bar{W} \rangle}{\bar{W}^2} + \frac{w \, d \langle \bar{W} \rangle}{\bar{W}^3}\\]

The shock contributes \\(w (\mu \, dt + \sigma \, dB)\\) to \\(d w\\) and \\(\bar{W} (\mu \, dt + \sigma \, dB)\\) to \\(d \bar{W}\\). In the
first two terms these give \\(x (\mu \, dt + \sigma \, dB) - x (\mu \, dt + \sigma \, dB) = 0\\). In the last two, the
cross-variation is \\(\sigma^2 w \bar{W} \, dt\\) and the variation of \\(\bar{W}\\) is \\(\sigma^2 \bar{W}^2 \, dt\\), giving
\\(-\sigma^2 x \, dt + \sigma^2 x \, dt = 0\\). Everything from the shock cancels. The trade term also rescales neatly: since
\\(\min(w, w\')^2\\) scales as the square of wealth, \\(B(w) = \bar{W}^2 \tilde{B}(x)\\), where \\(\tilde{B}\\) is the same function computed
from the distribution of relative wealth. So

\\[d x = \chi (1 - x) \, dt + \beta \sqrt{\tilde{B}(x)} \, d W\_i\\]

with no common noise at all. The density of relative wealth follows the original deterministic equation, and the Gini
coefficient follows a deterministic path. A shock that multiplies everyone's wealth by the same factor changes how rich the population is and leaves the sharing of that wealth unchanged.

The simulation agrees. I ran 200 independent populations of 2,000 agents each, with \\(\sigma = 0.04\\) per step, starting
from the steady state, and measured how much each population's Gini coefficient changed over 1,000 steps. With no shock
at all, the change had a standard deviation of 0.0055 across populations, which is pure finite-population noise. With the
proportional shock, the standard deviation was between 0.0050 and 0.0059 for three different values of \\(\mu\\), and the
average change was within 0.0004 of zero in every case. The shock is invisible in the inequality.

That cancellation depends on everyone having the same exposure, and that's not how wealth is held. Wealthier households
tend to hold more of their wealth in risky assets. To model this, let each agent hold a fraction \\(e(x)\\) of its wealth in the
risky asset, with \\(e\\) increasing in relative wealth. I used \\(e(x) = x / (1 + x)\\), so an agent at half the average wealth
holds a third of its wealth in the asset, an agent at the average holds half, and an agent at five times the average holds
five sixths. The shock term becomes \\(e(x\_i) \, w\_i (\mu \, dt + \sigma \, dB\_t)\\).

Repeat the ratio calculation. Averaging the shock term over agents, the total wealth now gets
\\(\bar{W} \bar{e} (\mu \, dt + \sigma \, dB\_t)\\), where

\\[\bar{e} = \int x \, e(x) \, \rho(x) \, dx\\]

is the wealth-weighted average exposure, the fraction of total wealth held in the risky asset. In the first two terms of the
ratio formula, the shock now contributes \\(x e (\mu \, dt + \sigma \, dB) - x \bar{e} (\mu \, dt + \sigma \, dB) = x (e - \bar{e})(\mu \, dt + \sigma \, dB)\\),
which no longer cancels. In the last two, the cross-variation is \\(\sigma^2 e w \cdot \bar{e} \bar{W} \, dt\\) and the variation of
\\(\bar{W}\\) is \\(\sigma^2 \bar{e}^2 \bar{W}^2 \, dt\\), so they contribute

\\[-\frac{\sigma^2 e \bar{e} w \bar{W}}{\bar{W}^2} dt + \frac{w \sigma^2 \bar{e}^2 \bar{W}^2}{\bar{W}^3} dt = -\sigma^2 \bar{e} \, x (e - \bar{e}) \, dt\\]

Putting it together, the relative wealth of an agent follows

\\[d x = \chi (1 - x) \, dt + \beta \sqrt{\tilde{B}(x)} \, d W\_i + x \big( e(x) - \bar{e} \big) \Big[ \big( \mu - \sigma^2 \bar{e} \big) dt + \sigma \, dB\_t \Big]\\]

This is the central result of the post. An agent whose exposure is above the wealth-weighted average, \\(e > \bar{e}\\), gets a
common shock to its relative wealth proportional to how far above average it is. Its relative wealth drifts up on average if

\\[\mu > \sigma^2 \bar{e}\\]

and down otherwise. The term \\(-\sigma^2 \bar{e}\\) comes entirely from the Itô cross-variation between an agent's wealth and the
total. It's the same volatility drag that drives condensation in the first place, appearing now at the level of the whole
economy. When the risky asset falls, the more exposed agents lose more, and the total, which is mostly made of exposed
wealth, falls too. Measured relative to that total, the rebound and the fall don't cancel, and the more exposed agents end
up behind unless the asset's expected return is large enough to pay for it.

Since the wealthy are the more exposed agents here, the sign of \\(\mu - \sigma^2 \bar{e}\\) decides whether a risky asset with
no other effect on the economy pushes inequality up or down over time. A zero-mean shock, \\(\mu = 0\\), lowers inequality on
average while making it random. A risky asset with an expected return above \\(\sigma^2 \bar{e}\\) raises it.

The density of relative wealth now satisfies a stochastic PDE that no longer reduces to a deterministic one. By the same
test-function argument applied to the equation for \\(x\\),

\\[d \rho = \Big[ -\partial\_x \Big( \big( \chi(1 - x) + (\mu - \sigma^2 \bar{e}) x (e - \bar{e}) \big) \rho \Big) + \gamma \, \partial\_x^2 (\tilde{B} \rho) + \frac{\sigma^2}{2} \partial\_x^2 \Big( x^2 (e - \bar{e})^2 \rho \Big) \Big] dt - \sigma \, \partial\_x \Big( x (e - \bar{e}) \rho \Big) dB\_t\\]

When \\(e\\) is constant, \\(e = \bar{e}\\) and every term involving the shock vanishes, which recovers the proportional case. Note
that \\(\bar{e}\\) depends on \\(\rho\\), so the noise coefficient itself depends on the current distribution, which makes the equation
nonlinear in a second way.

For the steady state above, the wealth-weighted exposure is \\(\bar{e} \approx 0.586\\). The population-average exposure is only
0.41, but the wealthier agents, who hold most of the wealth, are more exposed. With \\(\sigma = 0.04\\), the predicted breakeven
return is \\(\mu^\ast = \sigma^2 \bar{e} = 0.0016 \times 0.586 \approx 0.00094\\) per step. I ran the same 200 populations with the
wealth-dependent exposure and four values of \\(\mu\\). Over 1,000 steps, the average change in the Gini coefficient was \\(-0.0032\\)
at \\(\mu = 0\\), \\(-0.0027\\) at \\(\mu = 0.0004\\), \\(-0.0015\\) at \\(\mu = 0.0008\\), and \\(+0.0018\\) at \\(\mu = 0.0016\\). The standard error of
each average is about 0.001, so the sign changes between 0.0008 and 0.0016, which brackets the predicted 0.00094. The Gini
coefficient is a more complicated function of the distribution than the drift of a single agent's relative wealth, so this checks the sign and the location of the crossover, and the derivation doesn't predict the size of the change.

The common shock also makes inequality much less predictable. The standard deviation of the Gini change across populations rose
from 0.0055 with no shock, or with a proportional shock, to between 0.0137 and 0.0149 with wealth-dependent exposure, about two and a
half times as large. Subtracting the finite-population noise in quadrature, the common shock alone contributes about 0.013. Two
economies with identical rules and identical starting points can end up with noticeably different inequality purely because of the
path the market took, and the stochastic PDE is the object that describes that whole family of possible paths.

The model leaves out a great deal. The exposure function is one I picked for illustration, and real portfolios depend on far more
than relative wealth. Exposures here respond instantly to wealth, with no rebalancing costs or delays. The risky asset has constant
volatility and no jumps, which leaves out exactly the kind of crash that motivated the question. The redistribution is a flat tax
paid continuously. And the continuum limit assumes many agents trading small amounts, which the simulation suggests is accurate for the
steady state but which I haven't checked for the fluctuations of the Gini coefficient itself.

Two directions I'd like to work through next are a proper derivation of the drift of the Gini coefficient under the stochastic PDE,
which would turn the check on the crossover into a prediction of its size, and what happens when the risky asset's return depends on
how much of the economy holds it, which would let the shock feed back on itself.
