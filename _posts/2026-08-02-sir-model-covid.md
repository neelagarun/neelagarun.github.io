---
layout: post
title: "SIR Modeling: The Math Behind an Epidemic Curve, and How to Simulate One"
date: 2026-08-02
categories: [statistics]
---
Mathematics turns out to be a useful
tool for the quantitative analysis of infection dynamics, the same way it's
useful for predicting any other observable physical phenomenon. Future
infection rates can be predicted with real accuracy, provided we're working
from accurate current conditions, and that should offer some comfort: it
means we can watch how our behavior today shapes the pandemic's future, and
how changes in that behavior push the curve one way or the other.

Relatively simple models can be built from just a handful of parameters.
The parameters get updated as better information comes in, and the model
updates with them. I want to walk through where the core equations come
from, define the number everyone kept quoting during COVID (\\(R_0\\)), and
then actually simulate the thing in Python so the curves aren't just
abstract.

### A short history

Early disease modeling traces back to physicists, of all people. Daniel
Bernoulli, better known for fluid dynamics, derived an early epidemiological
model describing smallpox spread through a population, back when
variolation (an early, crude form of smallpox vaccination) was showing
some real efficacy at controlling outbreaks.

Modern infectious disease modeling has its core in the SIR model, a
compartmental model: Susceptible, Infectious, Recovered/Removed. In its
most basic form, with only those three categories, it shows how an
infectious agent propagates through a population as a function of time.

### Setting up the model

Take a population of size \\(N\\), and split it into three groups: \\(S\\),
\\(I\\), and \\(R\\). To make the math easier, assume the population size
stays constant (an oversimplification, but a fine starting point). Each
group is a proportion of the total, and each group is a function of time:

$$\frac{S(t)}{N} + \frac{I(t)}{N} + \frac{R(t)}{N} = 1$$

Since each term here is a proportion, we can drop the \\(N\\) and write the
lower case versions instead: \\(s(t)\\), \\(i(t)\\), \\(r(t)\\). What we
actually care about is how these functions change over time, so we
differentiate each one to get the instantaneous rate of change, moving from
discrete to continuous time along the way.

There has to be some transfer of people from the susceptible group into the
infectious group. That transfer rate is governed by \\(\beta\\), the
probability that a susceptible person becomes infected on contact with an
infectious one. There also has to be a transfer from infectious to
recovered, governed by \\(\gamma\\), which is inversely proportional to how
long a person stays infectious. In this simple version of the model, a
person stops being infectious whether they recover or die; the model
doesn't distinguish between the two. (More realistic versions handle
re-entry into the susceptible pool after recovery, or after a period of
immunity wears off, but that's an extension for another post.)

Putting the parameters together with the groups gives the classic system of
nonlinear ordinary differential equations:

$$
\begin{aligned}
\frac{ds(t)}{dt} &= -\beta \, s(t) \, i(t) \\
\frac{di(t)}{dt} &= \beta \, s(t) \, i(t) - \gamma \, i(t) \\
\frac{dr(t)}{dt} &= \gamma \, i(t)
\end{aligned}
$$

Solving this system gives the epidemic curve, once \\(\beta\\) and
\\(\gamma\\) are pinned down. The catch is that these constants aren't
universal. They shift depending on the pathogen, the season, and how the
population is behaving, so any solution is only as good as the parameter
estimates feeding it.

### The number everyone kept citing: R0

One term worth defining on its own is the basic reproductive number,
\\(R_0\\): the average number of people each infected person goes on to
infect. If \\(R_0 > 1\\), the disease is in an epidemic state and growing
exponentially. If \\(R_0 = 1\\), it's endemic, holding roughly steady in the
population. If \\(R_0 < 1\\), it eventually dies out on its own.

Estimates for SARS-CoV-2's \\(R_0\\) landed somewhere between 2 and 3.5,
which is a wide enough range that it produces a wide range of outcomes once
you plug it into a compartmental model. It's also worth noting that
\\(R_0\\) isn't a fixed property of the virus. It shifts with behavior
(mask-wearing, distancing) and with therapeutics: a shorter illness
duration lowers it, all else equal. This leads to a genuinely
counterintuitive result: a disease that killed within a day or two would,
mechanically, have a lower \\(R_0\\) than one that let people walk around
infectious for two weeks, simply because there's less time to spread it.

### Simulating it

None of this needs anything exotic. `scipy.integrate.odeint` will solve the
system directly given the three equations above, starting conditions, and a
time range.

```python
import numpy as np
from scipy.integrate import odeint
import matplotlib.pyplot as plt

def sir_model(y, t, beta, gamma):
    s, i, r = y
    ds_dt = -beta * s * i
    di_dt = beta * s * i - gamma * i
    dr_dt = gamma * i
    return [ds_dt, di_dt, dr_dt]

# Parameters
r0 = 2.0
gamma = 1 / 10  # infectious for about 10 days on average
beta = r0 * gamma

# Initial conditions: proportions of the population
i0 = 1e-5
s0 = 1 - i0
r0_init = 0
y0 = [s0, i0, r0_init]

t = np.linspace(0, 250, 500)

result = odeint(sir_model, y0, t, args=(beta, gamma))
s, i, r = result.T

peak_day = t[np.argmax(i)]
peak_fraction = np.max(i)

print(f"R0: {r0}")
print(f"Peak infected fraction: {peak_fraction:.4f}")
print(f"Peak occurs at day: {peak_day:.1f}")

plt.figure(figsize=(8, 5))
plt.plot(t, s, label="Susceptible")
plt.plot(t, i, label="Infectious")
plt.plot(t, r, label="Recovered")
plt.xlabel("Days")
plt.ylabel("Fraction of population")
plt.title(f"SIR model, R0 = {r0}")
plt.legend()
plt.tight_layout()
plt.savefig("sir_simulation.png", dpi=150)
```

Running this with \\(R_0 = 2\\) produces the familiar shape: susceptible
drops off, infectious spikes and then decays, recovered climbs and
plateaus. Because the whole population was never infected before the
disease burned itself out, susceptible doesn't bottom out at zero; there's
a chunk of people who never got exposed at all.

Bump \\(R_0\\) up toward 3.5 and rerun it, and the peak gets both taller and
earlier. That's the entire mechanism behind flattening the curve: lowering
\\(\beta\\) (through distancing, masks, closures) lowers the effective
\\(R_0\\), which pushes the peak lower and later, giving healthcare systems
more room to keep up.

### Where the simple model breaks down

The basic SIR model assumes uniform mixing: every susceptible person is
equally likely to bump into every infectious person, which isn't how real
populations behave. People cluster around shared destinations (a workplace,
a campus, a grocery store), and that clustering matters a lot for how fast
a disease actually spreads, even when the aggregate contact rate looks the
same on paper.

A more realistic model needs to account for that structure, plus a few
other things this version leaves out entirely: travel between otherwise
separate communities, reinfection after immunity wears off, and different
transmission probabilities for asymptomatic versus symptomatic carriers.
Each of those turns three coupled equations into something considerably
messier, but the same core idea holds: get the parameters right, and the
model tells you, quantitatively, what a given policy or behavior change is
actually going to do to the curve.
