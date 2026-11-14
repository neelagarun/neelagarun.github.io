---
layout: post
title: "Credit Risk as a Decision Problem: Gradient Boosting, Cutoff Policies, and Knowing When Not to Ship"
date: 2026-10-21
redirect_from:
  - /machine-learning/statistics/2026/11/13/credit-risk-decision-problem.html
categories: [machine-learning, statistics]
---

Most writing about credit risk models treats the problem as classification: predict whether a
borrower defaults, report an AUC, and move on. But a lender doesn't need a
classification. A lender needs a decision, approve or deny, and the quality of that decision
depends on things the classifier knows nothing about: what a default costs relative to what a
repaid loan earns, how much risk the business is willing to carry, and whether the probabilities
coming out of the model mean what they say. The model is maybe half the problem.

Before getting into it, I want to note that I am going to stay away from the specifics of how the
model itself is built: the features, the tuning, and the way it is packaged and deployed are
things I'm not going to share here. What I can talk about is the statistical framework around it,
which applies to essentially any scoring
model you could put in the middle.

The data is a public set of roughly 32,000 personal loans with known outcomes, about 22% of which
defaulted. Each row has the usual applicant information (income, employment length, housing
status, loan amount and purpose, credit bureau history) along with two fields that turn out to
matter enormously: the lender's own risk grade and the interest rate it assigned. I will come back
to those, because they change what the model is.

The first choice, which sounds cosmetic but isn't, is what to predict. The raw label is default.
I flip it and model the probability of repayment instead:

\\[p(x) = P(\text{repay} \mid x)\\]

and approve whenever \\(p(x) \geq t\\) for some cutoff \\(t\\). The reason for doing this is
that it makes a positive prediction mean *approve*, which means the expensive mistake, approving a
loan that goes bad, is a false positive. Precision on the positive class then becomes the
quantity the business cares about: of the loans we approved, what fraction paid us back.
Recall is the secondary objective: of the borrowers who would have repaid, how many did we lend to. Every good borrower you deny is revenue you left on the table, so you want
recall as high as you can get it, but only inside whatever precision constraint the lender is
willing to live with.

The naive approach is to take the classifier's default cutoff of 0.5 and call it a day. There is
nothing special about 0.5. It's the Bayes optimal threshold only when both kinds of mistakes cost
the same, and in lending they don't come close. Suppose a repaid loan earns \\(g\\) and a default
loses \\(\ell\\). The expected value of approving an applicant with repayment probability \\(p\\)
is

\\[\mathbb{E}[\text{value} \mid p] = p \, g - (1 - p) \, \ell\\]

which is positive exactly when

\\[p \geq t^{\ast} = \frac{\ell}{g + \ell} = \frac{c}{1 + c}, \quad c = \frac{\ell}{g}\\]

So at a cost ratio of 3:1, where a default loses three times what a good loan earns, the
theoretically optimal cutoff is 0.75, not 0.5. This result has a hidden
assumption baked into it: it only holds if \\(p(x)\\) is *calibrated*, meaning that among all
applicants the model scores at 0.8, about 80% actually repay. Gradient boosted trees are not
guaranteed to be calibrated. They optimize log loss, which is a proper scoring rule and so pushes
in the right direction, but with enough depth and enough trees they can become overconfident in
the tails.

So rather than trusting the formula, I pick the cutoff empirically by directly maximizing
realized profit over the candidate thresholds:

\\[t\_{\text{profit}} = \arg\max\_t \sum\_{i} \mathbb{1}[p\_i \geq t] \big( y\_i - c \, (1 - y\_i) \big)\\]

This also gives you a free diagnostic. If the model is well calibrated, the
empirically optimal cutoff should land close to \\(c / (1 + c)\\). In my case it landed within a
couple of hundredths of 0.75, which is a reassuring, independent confirmation of what the
calibration metrics were already saying (expected calibration error on cross-validated
predictions was under one percentage point). When those two numbers disagree badly, it's a sign
the probabilities shouldn't be taken at face value, and you would want to recalibrate with
something like isotonic regression or Platt scaling before building any policy on top of them.

That diagnostic can be made precise, and the argument is a little more subtle than it
looks. Let \\(f(s)\\) be the density of scores across applicants, and define the calibration map

\\[q(s) = P(\text{repay} \mid p(x) = s)\\]

which is the true repayment rate among applicants the model scores at \\(s\\). A perfectly
calibrated model has \\(q(s) = s\\). Over a population of \\(N\\) applicants, the expected profit
of approving everyone above \\(t\\) is

\\[\Pi(t) = N \int\_t^1 \big[ q(s) \, g - (1 - q(s)) \, \ell \big] f(s) \, ds\\]

and differentiating with respect to the lower limit gives

\\[\frac{d\Pi}{dt} = -N f(t) \big[ q(t) \, g - (1 - q(t)) \, \ell \big] = 0 \quad \Longrightarrow \quad q(t^{\ast}) = \frac{\ell}{g + \ell}\\]

So the empirical argmax isn't finding the point where the *score* equals \\(c / (1 + c)\\). It's
finding the point where the *true repayment rate* equals \\(c / (1 + c)\\). Those coincide only if
\\(q(t) = t\\) at that point. The interesting consequence is that the model only has to be
calibrated *locally*, near the cutoff, for the policy to be right. A model that is badly
overconfident at 0.99 and 0.01 can still make perfectly good decisions at 0.75, which is part of
why I care more about the cutoff landing where theory says it should than about any single global
calibration number.

That said, the global numbers are still worth tracking, and the right ones are proper scoring
rules, meaning scores that are minimized in expectation only by reporting the true probability.
Log loss is the one the trees optimize directly:

\\[\mathcal{L} = -\frac{1}{N} \sum\_{i=1}^{N} \big[ y\_i \ln p\_i + (1 - y\_i) \ln (1 - p\_i) \big]\\]

and the Brier score is just the mean squared error of the probabilities,
\\(\text{BS} = \frac{1}{N} \sum\_i (p\_i - y\_i)^2\\). What makes the Brier score useful is
Murphy's decomposition. Bin the predictions into \\(K\\) groups, where group \\(k\\) has
\\(n\_k\\) loans, mean predicted probability \\(\bar{p}\_k\\), and observed repayment rate
\\(\bar{y}\_k\\), and let \\(\bar{y}\\) be the overall repayment rate. Then, up to a within-bin
term that vanishes as the bins get fine,

\\[\text{BS} \approx \underbrace{\sum\_{k} \frac{n\_k}{N} (\bar{p}\_k - \bar{y}\_k)^2}\_{\text{reliability}} \; - \; \underbrace{\sum\_{k} \frac{n\_k}{N} (\bar{y}\_k - \bar{y})^2}\_{\text{resolution}} \; + \; \underbrace{\bar{y} (1 - \bar{y})}\_{\text{uncertainty}}\\]

Another way of reading this: uncertainty is a property of the data that no model can touch,
reliability is how far the probabilities are from the truth (smaller is better), and resolution
is how well the model separates borrowers into groups with genuinely different repayment rates
(larger is better). A model that predicts the base rate for everyone is perfectly reliable and
completely useless, because its resolution is zero. The expected calibration error I quoted above
is the same idea as the reliability term, with absolute values in place of squares:

\\[\text{ECE} = \sum\_{k} \frac{n\_k}{N} \left\lvert \bar{y}\_k - \bar{p}\_k \right\rvert\\]

And if the reliability term ever does get large, Platt scaling fits two parameters on held-out
data, \\(\tilde{p} = 1 / \big(1 + e^{-(a z + b)}\big)\\) where \\(z\\) is the model's raw
log-odds, while isotonic regression fits an arbitrary monotone step function. Platt is safer with
little data; isotonic is more flexible with a lot of it.

The second policy is a constraint rather than an objective. A lot of lenders don't think in cost
ratios; they think "no more than 5% of what we approve should default." That is a constrained
optimization problem:

\\[\max\_t \ \text{Recall}(t) \quad \text{subject to} \quad \text{Precision}(t) \geq \pi\\]

which is structurally the same as a Neyman–Pearson problem with the roles of the two error types
arranged around precision instead of false positive rate. Since recall is monotonically
non-increasing in \\(t\\), the solution is just the *lowest* cutoff whose precision clears the
floor \\(\pi\\). Precision, unlike recall, is not monotonic
in \\(t\\). As you raise the cutoff, you can drop a run of good loans and watch precision dip
before it climbs again. So you can't just binary search for the crossing point; you have to scan
the whole precision–recall curve and take the best recall among every threshold that satisfies
the constraint.

The cleanest way to see why is to write both metrics in terms of the score distributions. Let
\\(\rho\\) be the base rate of repayment, and let \\(S\_1(t)\\) and \\(S\_0(t)\\) be the fractions
of repaid and defaulted loans, respectively, that score at or above \\(t\\). Then

\\[\text{Recall}(t) = S\_1(t), \qquad \text{Precision}(t) = \frac{\rho \, S\_1(t)}{\rho \, S\_1(t) + (1 - \rho) \, S\_0(t)} = \left( 1 + \frac{1 - \rho}{\rho} \cdot \frac{S\_0(t)}{S\_1(t)} \right)^{-1}\\]

Recall is a survival function, so it can only go down as \\(t\\) goes up. Precision depends on
the *ratio* \\(S\_0(t) / S\_1(t)\\), and both of those fall as \\(t\\) rises. If the next stretch of
scores you cut off happens to be all repaid loans, \\(S\_1\\) drops while \\(S\_0\\) doesn't move,
the ratio goes up, and precision goes *down*. The floor constraint itself rearranges to

\\[\text{Precision}(t) \geq \pi \quad \Longleftrightarrow \quad \frac{S\_0(t)}{S\_1(t)} \leq \frac{\rho}{1 - \rho} \cdot \frac{1 - \pi}{\pi}\\]

which says the floor is really a cap on how many defaults you let through per good loan you
approve.

There is another catch here that surprised me. Precision floors close to the base rate are
degenerate. About 78% of the loans in this data repay, so a floor of, say, 90% sounds strict,
but it turned out you can satisfy it by approving almost everyone. The reason is that a chunk of
applicants, around 14% of them, receive near-zero scores and essentially every one of them
defaults. Cutting off just that sliver lifts the precision of the remaining pool above 90% on its
own, and the "optimal" cutoff collapses to something like 0.03. The policy is technically correct
and practically useless. The formula above makes this mechanical. At \\(t = 0\\) everyone is
approved, \\(S\_0 = S\_1 = 1\\), and precision is exactly the base rate \\(\rho\\). Trimming a
bottom tail that is almost entirely defaults cuts \\(S\_0\\) sharply while leaving \\(S\_1\\)
essentially at 1, so the ratio collapses and precision jumps, without the model having to rank
anyone in the middle of the distribution correctly at all. The lesson is that a precision floor
only means something once it's set well above what simple exclusion of the obvious defaults
already achieves, and in practice the meaningful range here started somewhere above 91%.

Now an important detail that I think gets skipped constantly: where the cutoffs are chosen. If
you choose a threshold on the test set and then report test set metrics at that threshold, you
have optimized on the evaluation data and every number you report is optimistically biased. This
is the same leakage problem as tuning hyperparameters on the test set, just one step later in the
pipeline. Both cutoffs here are chosen on out-of-fold predictions from cross-validation on the
training split. Every training example gets a score from a model that never saw it, the policies
are fit on those pooled scores, and then they're frozen and applied unchanged to a held-out test
set that is touched exactly once. The test set came back at 95.2% precision against a 95% floor,
which is about what you'd hope for if the out-of-fold scores are an honest proxy for future ones.

At the 95% floor, the model approves a little over three quarters of applicants, still lends to
about 93% of the borrowers who would have repaid, and cuts the number of approved defaults by
roughly 83% relative to approving everyone. Under the 3:1 cost ratio, profit per hundred
applicants goes up by about a factor of five over the approve-everyone baseline. ROC-AUC on the
test set is around 0.95.

Those numbers are only part of the story, though, because they invite the obvious question of
whether a fancier model would do better. I spent a while on this. Across a broad comparison of
model families, linear models, kernel methods, neural networks, bagging, and boosting, gradient
boosted trees won by a wide margin. At a 95% precision floor, logistic regression approves only
about two thirds of the good borrowers; boosted trees approve over 90%. That gap is large and it
says something real about the data: default risk here is driven by thresholds and interactions
(renting *and* a high loan-to-income ratio, for instance), which trees represent naturally and a
linear model in the raw features cannot.

The harder comparisons are between models that are all good. A stacked ensemble of the best few
models narrowly beat a single boosted model on paper, and a later round of deeper tuning and
additional features produced a candidate that beat the production model on every metric. Neither
was shipped, and the reason why is the part of this I'd most want someone to take away.

The standard approach to comparing two models is to run \\(k\\)-fold cross-validation, take the
per-fold difference in some metric \\(d\_j\\), and run a paired t-test. The problem is that the
folds are not independent. The training sets overlap heavily (in 5-fold CV, any two training sets
share 75% of their rows), so the per-fold differences are positively correlated and the naive
variance estimate is far too small. You end up declaring significance on noise. Nadeau and Bengio
proposed a corrected statistic for exactly this situation. Over \\(J\\) total resamples with
\\(n\_1\\) training and \\(n\_2\\) test rows per resample:

\\[t = \frac{\bar{d}}{\sqrt{\left( \dfrac{1}{J} + \dfrac{n\_2}{n\_1} \right) \hat{\sigma}\_d^2}}\\]

where \\(\bar{d}\\) is the mean difference and \\(\hat{\sigma}\_d^2\\) is the sample variance of the
differences. The \\(n\_2 / n\_1\\) term is the correction. For 5-fold CV it equals 0.25, which
dwarfs the \\(1/J\\) term once you repeat CV a few times, and that's the point: adding more
repetitions of the same CV on the same data makes the naive test look more and more confident,
while the corrected one correctly recognizes that you haven't actually collected any new
information. I used repeated stratified CV with this correction, and fixed the criterion for
shipping a new model before running any of the experiments: no primary business metric worse,
at least one significantly better.

On the held-out test set, where there is only one split and so nothing to average over, the
right tool is a different one. McNemar's test only looks at the applicants where the two models
disagree. If \\(b\\) is the number of loans model A classifies correctly and model B gets wrong,
and \\(c\\) is the reverse, then under the null hypothesis that the models are equally good,

\\[\chi^2 = \frac{\big( \lvert b - c \rvert - 1 \big)^2}{b + c} \;\sim\; \chi^2\_1\\]

with the \\(-1\\) as a continuity correction. The thing I like about this test is what it ignores:
the thousands of loans both models get right, or both get wrong, carry no information about which
model is better, and they don't enter the statistic at all. When two strong models agree on the
vast majority of applicants, as they did here, \\(b + c\\) is small and the test is honest about
how little evidence there is.

The tuned candidate improved recall at the 95% floor by a few tenths of a point and profit by a
fraction of a unit per hundred applicants, and neither difference came close to significance.
More tellingly, when I re-ran the comparison on freshly drawn folds that played no part in
selecting the candidate, the gain shrank further. That shrinkage is exactly what you'd expect from
the winner's curse: if you try several hundred configurations and keep the best one, its measured
score is the maximum of several hundred noisy estimates, and the maximum of noisy estimates is
biased upward. You can put a rough number on how biased. Suppose, pessimistically, that every
one of \\(K\\) configurations has the same true score \\(\mu\\), and each is measured with
independent noise, \\(\hat{\mu}\_k = \mu + \sigma Z\_k\\) with \\(Z\_k \sim \mathcal{N}(0, 1)\\).
Then for large \\(K\\),

\\[\mathbb{E}\Big[ \max\_{k \leq K} \hat{\mu}\_k \Big] \approx \mu + \sigma \sqrt{2 \ln K}\\]

With a few hundred trials, \\(\sqrt{2 \ln K}\\) is already above 3. In other words, the best
configuration of a few hundred identical ones would look about three standard errors better than
it really is, purely from noise. Real trials are correlated with each other, so the effective
\\(K\\) is smaller and the bias is less severe than that, but the direction is never in doubt. The
honest estimate of how good the winner is has to come from data that wasn't used to pick it.
Some of the feature additions were actually significantly *worse* on log loss
and PR-AUC, which shows that trees are already quite good at constructing the
ratios and interactions you might hand-engineer for them, and extra columns mostly give them more
ways to overfit.

So the existing model stayed. I think a "consistently better but not significantly better"
result is one of the most tempting places to make a bad decision, because every individual number
points the same way. But a new model is not free. It changes outputs that downstream users have
calibrated their expectations against, and if the improvement is indistinguishable from noise
you're taking on that cost for nothing.

What *did* move the needle was data. A learning curve, training on increasing subsets of the
training split and evaluating out of fold, had not flattened. Empirically, learning curves for
models like this tend to follow something like a power law,

\\[\varepsilon(n) \approx a \, n^{-b} + \varepsilon\_\infty\\]

and the curve was still clearly on the steep part. The last few thousand training rows bought
roughly 0.8 points of recall at the 95% floor, more than all the tuning combined. That is a strong
signal about where effort should go next. The catch is that the obvious way to use more data,
refitting on every labelled row including the test set, leaves you with no held-out data to
validate the refit on. It's a real tradeoff and it's a business decision more than a statistical
one.

To see what the model relies on, I used permutation importance, which measures how much
a metric \\(M\\) degrades when one input is scrambled:

\\[I\_k = M\big(y, f(X)\big) - \frac{1}{R} \sum\_{r=1}^{R} M\big(y, f(X^{(k, r)})\big)\\]

where \\(X^{(k, r)}\\) is the data with column \\(k\\) randomly permuted in repetition \\(r\\),
which destroys that input's relationship with the label while keeping its marginal distribution
intact. Using test PR-AUC as \\(M\\), the main drivers are income, housing status, the lender's
grade, loan-to-income ratio, and loan purpose. A prior default on the credit file adds almost
nothing once the grade is known, which makes sense: the grade has presumably already priced it in.
That's also a good illustration of what permutation importance does and doesn't measure. It
measures an input's *unique* contribution given everything else. When two inputs carry
overlapping information, shuffling one leaves the other to carry the signal, so both can look
unimportant even though removing both would hurt a lot. A low score means "redundant here," not
"irrelevant." That brings me back to the two lender-assigned fields. If you
remove the grade and the interest rate, recall at the 95% floor falls from about 93% to about 75%.
So this model refines the lender's underwriting and sits on top of it, and it should only ever be applied to applications that have already been graded.
Formally, what it estimates is \\(P(\text{repay} \mid x, \text{grade}, \text{rate})\\), and
there's no reason to expect that to transfer to a setting where those inputs don't exist.

There is a deeper version of that problem which no amount of modeling fixes. The data only
contains loans that were approved and booked. Everyone the lender turned down is missing, so what
the model learns is

\\[P(\text{repay} \mid x, \text{approved}) \neq P(\text{repay} \mid x)\\]

and the gap between those two is largest precisely in the region you most care about, the
marginal applicants near the lender's own cutoff. This is the reject inference problem, and the
standard remedies (augmentation, parcelling, or simply approving a small random slice of
applicants you would otherwise deny to observe what happens) all require either strong
assumptions or real money.

The most principled of those assumptions is worth writing down, because it shows exactly where
things break. Let \\(A = 1\\) if a loan was approved and \\(e(x) = P(A = 1 \mid x)\\) be the
approval probability, sometimes called the propensity. If approval depends only on what's in
\\(x\\), then for any loss \\(L\\) the population risk can be recovered from approved loans alone
by inverse-probability weighting:

\\[\mathbb{E}\big[ L(y, p(x)) \big] = \mathbb{E}\left[ \frac{A \cdot L(y, p(x))}{e(x)} \right]\\]

Each approved loan stands in for \\(1 / e(x)\\) applicants like it. The catch is the denominator.
This only works if \\(e(x) > 0\\) everywhere, a condition usually called overlap or positivity.
A lender's approval policy is close to deterministic, so for whole regions of applicant space
\\(e(x) = 0\\): nobody like them was ever approved, and no amount of reweighting can conjure
outcomes that were never observed. And in this data it's worse still, because the rejected
applicants aren't in the file at all, so \\(e(x)\\) can't even be estimated. That's why the
random-approval approach is the direct fix even though it costs money. Approving a small random
fraction \\(\varepsilon\\) of applicants who would otherwise be denied guarantees
\\(e(x) \geq \varepsilon\\) everywhere and makes the weighting valid by construction. It's the
main reason to be cautious about how far any model trained on booked loans can be
pushed.

One more data issue, because I think it's a good cautionary tale. There is a second, larger loan
dataset in circulation that is often presented as approval data and looks like a natural thing to
add to training. When I matched its rows against this dataset on the columns that hadn't been
perturbed, using only rows whose combination of values was unique so the matches couldn't be
coincidental, a large fraction of it turned out to be a disguised copy of these same loans, with
income inflated, a few columns altered or invented, and a batch of synthetic rows generated from
the originals. Its "approval" label agreed with the default label here 99.98% of the time on the
matched rows, and thousands of the copies were loans from my held-out test set. Training on it
would have produced spectacular numbers for entirely the wrong reason. The general lesson is that
leakage doesn't only happen inside your pipeline; it can arrive pre-packaged in a dataset, and the
only defense is to check.

Finally, the interpretability and fairness side. The standard way to attribute a single
prediction to its inputs is the Shapley value from cooperative game theory. With \\(F\\) the set
of inputs, the contribution of input \\(k\\) to the prediction for applicant \\(x\\) is

\\[\phi\_k(x) = \sum\_{S \subseteq F \setminus \\{k\\}} \frac{\lvert S \rvert! \, \big( \lvert F \rvert - \lvert S \rvert - 1 \big)!}{\lvert F \rvert!} \Big[ v\_x\big(S \cup \\{k\\}\big) - v\_x(S) \Big]\\]

where \\(v\_x(S) = \mathbb{E}\big[ f(X) \mid X\_S = x\_S \big]\\) is the model's expected output when
only the inputs in \\(S\\) are known. In words: average the marginal effect of revealing input
\\(k\\) over every possible order in which the inputs could be revealed. It's the unique
attribution satisfying a short list of fairness axioms (efficiency, symmetry, a dummy property,
and additivity), which is why it has become the default. The problem is that the sum runs over
all \\(2^{\lvert F \rvert - 1}\\) subsets, which is exponential. For tree ensembles there's an algorithm,
TreeSHAP, that exploits the tree structure to compute the values exactly in
\\(O(T L D^2)\\) time for \\(T\\) trees with at most \\(L\\) leaves and depth \\(D\\). So a single
tree ensemble has the property that Shapley attributions can be computed exactly rather than
approximated, and they're additive: for each applicant,

\\[\text{logit}\big(p(x)\big) = \phi\_0 + \sum\_{k} \phi\_k(x)\\]

where \\(\phi\_0\\) is the average log-odds and \\(\phi\_k(x)\\) is the contribution of input
\\(k\\) for that specific applicant. That matters because in most jurisdictions a denied applicant
is owed a reason, and "the model said so" isn't one. It's also part of why the simpler model was
preferable to the ensemble: the explanations are exact rather than an approximation of an
approximation. On fairness, I want to be direct: applicant age is one of the inputs, and
age-based credit decisions are restricted in many places (the US Equal Credit Opportunity Act, for
example). Income and housing status can act as proxies for protected characteristics, and since
the data carries no protected attributes, no disparate impact analysis is possible on it. Before
anything like this went near a real lending decision, those would need to be addressed with a
legal review and representative data.

One last piece of math, because it's the first thing I would watch if a model like this were
running on live applications. Outcomes take months to mature, so you can't measure precision in
real time, but you can measure whether the population being scored still looks like the one the
model was built on. The usual statistic is the population stability index. Bin the scores, let
\\(e\_i\\) be the fraction of the reference population in bin \\(i\\) and \\(a\_i\\) the fraction of
recent applicants, and compute

\\[\text{PSI} = \sum\_{i} (a\_i - e\_i) \ln \frac{a\_i}{e\_i} = D\_{\text{KL}}(a \,\Vert\, e) + D\_{\text{KL}}(e \,\Vert\, a)\\]

which is just a symmetrized KL divergence between the two distributions. The common rule of thumb
is that below 0.1 is stable, 0.1 to 0.2 is worth a look, and above 0.2 means the population has
shifted enough that the model's cutoffs, which were tuned on the old distribution, shouldn't be
trusted until outcomes confirm them.

There is a lot more to say: how the policies behave across a range of cost ratios and precision
floors, what the rest of a monitoring setup should look like once real outcomes start maturing
(realized precision against the floor, calibration by decile), and how the reject inference
problem might be attacked in practice. I will get into those in future posts.
