---
layout: post
title: "23 Countries Down, 172 to Go: How Long Until I've Been Everywhere?"
date: 2026-11-13 12:00:00 +0000
categories: [travel]
---

I've been to 23 countries so far, and I'd like to get to all of them. That raises a
question I couldn't stop myself from formalizing: how long is that actually going to take?
This is the first post in the travel section, so it seemed fitting that it would be about
travel and also, unavoidably, about probability.

The first thing you have to decide is what "all of them" means. There is no single answer.
The UN has 193 member states, and adding the two observer states (Vatican City and Palestine)
gives 195, which is the number most people use. Other lists include Taiwan, Kosovo, or
various territories and get you closer to 200. I'll use 195, which leaves

\\[n = 195, \qquad k = 23, \qquad n - k = 172\\]

countries remaining.

The naive approach is to treat this as the coupon collector problem. Suppose every trip
lands me in a country chosen uniformly at random from all 195, including ones I've already
been to. If I currently have \\(j\\) countries, the next trip is a new one with probability
\\((n - j)/n\\), so the number of trips until I get a new country is geometric with mean

\\[\mathbb{E}[T_j] = \frac{n}{n - j}\\]

Summing from where I am now to the end:

\\[\mathbb{E}[T] = \sum_{j=k}^{n-1} \frac{n}{n - j} = n \sum_{m=1}^{n-k} \frac{1}{m} = n \, H_{n-k}\\]

where \\(H_m\\) is the \\(m\\)th harmonic number. With \\(n - k = 172\\),
\\(H_{172} \approx \ln 172 + \gamma \approx 5.73\\), which gives

\\[\mathbb{E}[T] = 195 \times H_{172} \approx 1{,}117 \text{ trips}\\]

with a standard deviation of about 247 trips. I ran a quick simulation to check this and got
1,117.0 averaged over 20,000 runs, so the formula holds up.

The interesting thing about this number is where it comes from. The very next trip is new
with probability \\(172/195\\), so it takes about 1.13 trips on average. Getting from 23 to
roughly half the world, 98 countries, takes only about 111 trips. The other 1,000 or so are
spent on the back half, and the very last country alone takes 195 trips on average, because
at that point every trip has a \\(1/195\\) chance of landing somewhere new. That is the
general shape of coupon collecting: the early progress is cheap and the tail is brutal.

At three international trips a year, 1,117 trips is about 372 years, which is not
encouraging.

The problem is that uniform sampling is a bad model in two opposite directions, and it's
worth taking them one at a time.

The first is that countries are not equally likely. Over a lifetime of trips, I'm going to
end up in France far more often than I end up in Nauru, Turkmenistan, or North Korea,
whether I'm trying to or not. So instead of uniform probabilities, give each country
\\(i\\) its own probability \\(p_i\\) per trip. The sum above no longer has a clean form, but
there's a nice trick: if you pretend trips arrive as a Poisson process with rate 1, then each
country's first visit is an independent exponential with rate \\(p_i\\), and the expected
time until all remaining countries are visited is

\\[\mathbb{E}[T] = \int_0^\infty \left( 1 - \prod_{i \in \text{remaining}} \left( 1 - e^{-p_i t} \right) \right) dt\\]

To see what this does, I made up a rough split of the 172 remaining countries: 150 "easy"
ones with weight 1, 17 "medium" ones with weight 0.2, and 5 "hard" ones with weight 0.02, so
a hard country is fifty times less likely to come up on any given trip than an easy one.
The weights are invented, but the point doesn't depend on them much. The expected number
of trips jumps from about 1,100 to about 20,000.

Almost all of that comes from the five hard countries. Each one has a per-trip probability of
roughly \\(1/8{,}800\\), and the expected time until you've collected all of a group of
\\(m\\) equally rare coupons with probability \\(p\\) is \\(H_m / p\\). For five of them,

\\[\frac{H_5}{p_{\min}} \approx 2.28 \times 8{,}825 \approx 20{,}150\\]

which is essentially the entire answer. The other 167 countries are a rounding error. Another
way of reading this: the time to collect everything is set by the rarest few coupons, and how
many easy ones there are barely matters. If you want to know how long the whole list takes,
the only question worth asking is how hard the hardest five are.

The second way the model is wrong cuts in the other direction, and it's the one that
actually matters. Trips aren't random. I choose where to go. Coupon collecting is slow
because of repeats, and someone who is deliberately trying to see every country can simply
refuse to repeat. In the limit where every trip goes only to new places, the problem stops
being probabilistic at all:

\\[T = \left\lceil \frac{172}{c} \right\rceil\\]

where \\(c\\) is the number of new countries per trip. A single-country trip gives
\\(c = 1\\), but a few weeks in the Balkans or Central America can easily cover four or five.
At \\(c = 2\\) and three trips a year, that's 86 trips, or about 29 years. At \\(c = 3\\) it's
about 19.

The real answer probably sits somewhere between the two models, and the lesson about the
hard countries survives into the deliberate version. The easy countries will get done almost
by default. The finish line is decided by the small set of places that need visas, permits,
guided tours, or a stable political situation to visit, and those are the ones worth planning
around early instead of saving for the end.

There is more to say here, like how much repeat trips to favorite places cost in expected
years, and whether it's possible to estimate my own \\(p_i\\) from my travel history so far.
But unlike the coupon collector, I don't plan to let the repeats get in the way.
