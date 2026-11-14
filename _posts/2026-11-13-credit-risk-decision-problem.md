---
layout: post
title: "Credit Risk as a Decision Problem: Gradient Boosting, Cutoff Policies, and Knowing When Not to Ship"
date: 2026-11-13
categories: [machine-learning, statistics]
---

Most writing about credit risk models treats the problem as classification: predict whether a
borrower defaults, report an AUC, and move on. But a lender doesn't actually need a
classification. A lender needs a decision, approve or deny, and the quality of that decision
depends on things the classifier knows nothing about: what a default costs relative to what a
repaid loan earns, how much risk the business is willing to carry, and whether the probabilities
coming out of the model mean what they say. The model is maybe half the problem.

Before getting into it, I want to note that I am going to stay away from the specifics of how the
model itself is built: the features, the tuning, and the way it is packaged and deployed are
things I'm not going to share here. What I can talk about is the statistical framework around it,
which is probably the more interesting part anyway, and which applies to essentially any scoring
model you could put in the middle.

The data is a public set of roughly 32,000 personal loans with known outcomes, about 22% of which
defaulted. Each row has the usual applicant information (income, employment length, housing
status, loan amount and purpose, credit bureau history) along with two fields that turn out to
matter enormously: the lender's own risk grade and the interest rate it assigned. I will come back
to those, because they change what the model actually is.

The first choice, which sounds cosmetic but isn't, is what to predict. The raw label is default.
I flip it and model the probability of repayment instead:

\\[p(x) = P(\text{repay} \mid x)\\]

and approve whenever \\(p(x) \geq t\\) for some cutoff \\(t\\). The reason for doing this is
that it makes a positive prediction mean *approve*, which means the expensive mistake, approving a
loan that goes bad, is a false positive. Precision on the positive class then becomes the
quantity the business actually cares about: of the loans we approved, what fraction paid us back.
Recall is the secondary objective: of the borrowers who would have repaid, how many did we
actually lend to. Every good borrower you deny is revenue you left on the table, so you want
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
theoretically optimal cutoff is 0.75, not 0.5. This is a clean result, but it has a hidden
assumption baked into it: it only holds if \\(p(x)\\) is *calibrated*, meaning that among all
applicants the model scores at 0.8, about 80% actually repay. Gradient boosted trees are not
guaranteed to be calibrated. They optimize log loss, which is a proper scoring rule and so pushes
in the right direction, but with enough depth and enough trees they can become overconfident in
the tails.

So rather than trusting the formula, I pick the cutoff empirically by directly maximizing
realized profit over the candidate thresholds:

\\[t_{\text{profit}} = \arg\max_t \sum_{i} \mathbb{1}[p_i \geq t] \big( y_i - c \, (1 - y_i) \big)\\]

The nice thing is that this gives you a free diagnostic. If the model is well calibrated, the
empirically optimal cutoff should land close to \\(c / (1 + c)\\). In my case it landed within a
couple of hundredths of 0.75, which is a reassuring, independent confirmation of what the
calibration metrics were already saying (expected calibration error on cross-validated
predictions was under one percentage point). When those two numbers disagree badly, it's a sign
the probabilities shouldn't be taken at face value, and you would want to recalibrate with
something like isotonic regression or Platt scaling before building any policy on top of them.

The second policy is a constraint rather than an objective. A lot of lenders don't think in cost
ratios; they think "no more than 5% of what we approve should default." That is a constrained
optimization problem:

\\[\max_t \ \text{Recall}(t) \quad \text{subject to} \quad \text{Precision}(t) \geq \pi\\]

which is structurally the same as a Neyman–Pearson problem with the roles of the two error types
arranged around precision instead of false positive rate. Since recall is monotonically
non-increasing in \\(t\\), the solution is just the *lowest* cutoff whose precision clears the
floor \\(\pi\\). One subtlety worth mentioning is that precision, unlike recall, is not monotonic
in \\(t\\). As you raise the cutoff, you can drop a run of good loans and watch precision dip
before it climbs again. So you can't just binary search for the crossing point; you have to scan
the whole precision–recall curve and take the best recall among every threshold that satisfies
the constraint.

There is another catch here that surprised me. Precision floors close to the base rate are
degenerate. About 78% of the loans in this data repay, so a floor of, say, 90% sounds strict,
but it turned out you can satisfy it by approving almost everyone. The reason is that a chunk of
applicants, around 14% of them, receive near-zero scores and essentially every one of them
defaults. Cutting off just that sliver lifts the precision of the remaining pool above 90% on its
own, and the "optimal" cutoff collapses to something like 0.03. The policy is technically correct
and practically useless. The lesson is that a precision floor only means something once it's set
well above what simple exclusion of the obvious defaults already achieves, and in practice the
meaningful range here started somewhere above 91%.

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
per-fold difference in some metric \\(d_j\\), and run a paired t-test. The problem is that the
folds are not independent. The training sets overlap heavily (in 5-fold CV, any two training sets
share 75% of their rows), so the per-fold differences are positively correlated and the naive
variance estimate is far too small. You end up declaring significance on noise. Nadeau and Bengio
proposed a corrected statistic for exactly this situation. Over \\(J\\) total resamples with
\\(n_1\\) training and \\(n_2\\) test rows per resample:

\\[t = \frac{\bar{d}}{\sqrt{\left( \dfrac{1}{J} + \dfrac{n_2}{n_1} \right) \hat{\sigma}_d^2}}\\]

where \\(\bar{d}\\) is the mean difference and \\(\hat{\sigma}_d^2\\) is the sample variance of the
differences. The \\(n_2 / n_1\\) term is the correction. For 5-fold CV it equals 0.25, which
dwarfs the \\(1/J\\) term once you repeat CV a few times, and that's the point: adding more
repetitions of the same CV on the same data makes the naive test look more and more confident,
while the corrected one correctly recognizes that you haven't actually collected any new
information. I used repeated stratified CV with this correction, and fixed the criterion for
shipping a new model before running any of the experiments: no primary business metric worse,
at least one significantly better.

The tuned candidate improved recall at the 95% floor by a few tenths of a point and profit by a
fraction of a unit per hundred applicants, and neither difference came close to significance.
More tellingly, when I re-ran the comparison on freshly drawn folds that played no part in
selecting the candidate, the gain shrank further. That shrinkage is exactly what you'd expect from
the winner's curse: if you try several hundred configurations and keep the best one, its measured
score is the maximum of several hundred noisy estimates, and the maximum of noisy estimates is
biased upward. The honest estimate of how good the winner is has to come from data that wasn't
used to pick it. Some of the feature additions were actually significantly *worse* on log loss
and PR-AUC, which is a useful reminder that trees are already quite good at constructing the
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

\\[\varepsilon(n) \approx a \, n^{-b} + \varepsilon_\infty\\]

and the curve was still clearly on the steep part. The last few thousand training rows bought
roughly 0.8 points of recall at the 95% floor, more than all the tuning combined. That is a strong
signal about where effort should go next. The catch is that the obvious way to use more data,
refitting on every labelled row including the test set, leaves you with no held-out data to
validate the refit on. It's a real tradeoff and it's a business decision more than a statistical
one.

It's worth noting what the model is actually relying on. By permutation importance (the drop in
test PR-AUC when one input is shuffled and the rest are held fixed), the main drivers are income,
housing status, the lender's grade, loan-to-income ratio, and loan purpose. A prior default on the
credit file adds almost nothing once the grade is known, which makes sense: the grade has
presumably already priced it in. That brings me back to the two lender-assigned fields. If you
remove the grade and the interest rate, recall at the 95% floor falls from about 93% to about 75%.
So this model is not a replacement for the lender's underwriting; it is a refinement that sits on
top of it, and it should only ever be applied to applications that have already been graded.
Formally, what it estimates is \\(P(\text{repay} \mid x, \text{grade}, \text{rate})\\), and
there's no reason to expect that to transfer to a setting where those inputs don't exist.

There is a deeper version of that problem which no amount of modeling fixes. The data only
contains loans that were approved and booked. Everyone the lender turned down is missing, so what
the model actually learns is

\\[P(\text{repay} \mid x, \text{approved}) \neq P(\text{repay} \mid x)\\]

and the gap between those two is largest precisely in the region you most care about, the
marginal applicants near the lender's own cutoff. This is the reject inference problem, and the
standard remedies (augmentation, parcelling, or simply approving a small random slice of
applicants you would otherwise deny to observe what happens) all require either strong
assumptions or real money. It's the single biggest reason to be cautious about how far any
model trained on booked loans can be pushed.

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

Finally, the interpretability and fairness side. A single tree ensemble has the nice property that
Shapley value attributions can be computed exactly rather than approximated, and they're
additive: for each applicant,

\\[\text{logit}\big(p(x)\big) = \phi_0 + \sum_{k} \phi_k(x)\\]

where \\(\phi_0\\) is the average log-odds and \\(\phi_k(x)\\) is the contribution of input
\\(k\\) for that specific applicant. That matters because in most jurisdictions a denied applicant
is owed a reason, and "the model said so" isn't one. It's also part of why the simpler model was
preferable to the ensemble: the explanations are exact rather than an approximation of an
approximation. On fairness, I want to be direct: applicant age is one of the inputs, and
age-based credit decisions are restricted in many places (the US Equal Credit Opportunity Act, for
example). Income and housing status can act as proxies for protected characteristics, and since
the data carries no protected attributes, no disparate impact analysis is possible on it. Before
anything like this went near a real lending decision, those would need to be addressed with a
legal review and representative data.

There is a lot more to say: how the policies behave across a range of cost ratios and precision
floors, what monitoring a model like this should look like once real outcomes start maturing
(population stability on the score, realized precision against the floor, calibration by decile),
and how the reject inference problem might be attacked. I will get into those in future posts.
