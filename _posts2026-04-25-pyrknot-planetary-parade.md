---
layout: post
title: "Jane Street Puzzle: March 2026: Planetary Parade"
date: 2026-05-02
categories: [puzzles]
---

I'm posting this a little late but I want to work through some of the older puzzles as practice before I move on to the current ones. Here's the problem as stated on the Jane Street website:

We are enjoying a planetary parade during the beginning of this month here on Earth, but my alien friend from the distant planet Pyrknot (well modeled as a perfect sphere) isn't too impressed. Their single-star system has six planets other than Pyrknot with such chaotic orbits that each night these planets independently appear in uniformly random locations in the Pyrknothian sky (the planets aren't visible in daylight). If at a given moment there exists somewhere on Pyrknot that all six planets are visible, then from my friend's position on the surface they have an $\alpha$ probability of also seeing all of the planets.

My friend is considering building a tower to improve their chances of seeing these planetary parades. Assume the tower allows the viewing of every celestial body that could be seen from at least one location on the surface of Pyrknot less than a distance $r$ from the base of the tower. In the limit of $r$ being small compared to the radius $R$ of Pyrknot, the new probability of seeing the planetary parade from the top of the tower is linearly approximated by $\alpha + \beta \cdot (r/R)$. Find $\alpha$ and $\beta$ in exact terms.

Ok, so now we know the problem. I want to work through computing the probability that my friend on Pyrknot sees a planetary parade, both from the surface and from the top of a small tower. The setup is that Pyrknot is a perfect sphere with radius $R$, orbiting a single star, and sharing its system with six other planets. Each night those six planets appear independently at uniformly random locations in the sky, and none of them are visible in daylight. The question asks for the probability $\alpha$ that my friend 
can see all six planets given that someone on the surface can, and then there's the linear correction $\beta \cdot (r/R)$ that a tower of effective radius $r$ provides.

The first thing you need to figure out is the geometry of who can see what. From any point on the surface of a perfect sphere, you can see exactly one hemisphere of the sky. That means every celestial body (the six planets plus the star) defines a great circle on the surface of alien planet. On one side of that great circle, the body is above the horizon. On the other side it's the opposite. So the problem of figuring out which surface points can see which bodies reduces to understanding how seven great circles partition the sphere.

The partition structure comes from Euler's formula for a sphere, $V - E + F = 2$. Every pair of great circles intersects at exactly two antipodal points, so the vertex count is

$$V = 2 \binom{7}{2} = 2 \times 21 = 42$$

Each of the seven great circles gets cut into arcs by its intersections with the other six. That is $6 \times 2 = 12$ intersection points per circle, which means 12 arcs per circle, giving

$$E = 7 \times 12 = 84$$

and therefore

$$F = 2 - V + E = 2 - 42 + 84 = 44$$

So you end up with 44 regions on the surface of Pyrknot, each of which sees a distinct subset of the seven bodies. For a planetary parade to be visible from a region, that region needs all six planets above the horizon and the star below the horizon. At most one region can satisfy this at any given moment. By symmetry all 44 regions have the same expected area, so conditional on a parade existing somewhere, my friend is in the correct region with probability $1/44$. That gives $\alpha = 1/44$.

Now for the tower, and the part that requires a bit more care than it might look at first.

The tower lets my friend see any celestial body that is visible from at least one surface point within distance $r$ of the base. To first order in $r/R$, this is equivalent to pushing the boundary of their region outward by $r$ in every direction. The region gains a strip of area along its perimeter, and the area of that strip is approximately the perimeter times $r$.

The next question is what the expected perimeter of a single region is. Each of the 7 great circles has total length $2\pi R$, and each arc segment borders two adjacent regions, so the sum of perimeters across all 44 regions is

$$\text{total perimeter} = 7 \times 2 \times 2\pi R / 2 = 14\pi R$$

This actually isn't that great so far because you need to figure out some details. Each great circle of length $2\pi R$ contributes that length to the perimeter of the regions on both sides of it, but each segment is shared by exactly two regions. So summing perimeters over all regions counts each great circle arc exactly twice, giving $7 \times 2 \times 2\pi R / 2 = 14\pi R$. By symmetry, the expected perimeter of a single region is

$$\bar{P} = \frac{14\pi R}{44} = \frac{7}{11}\pi R$$

This is where some details actually change the answer. Some of the information from the boundary segements can be discarded. Of the seven great circles, six come from planets and one comes from the star. When the tower pushes past a planet boundary, it lets my friend see an additional planet they could not see before. That is good, but when it pushes past the star boundary, it means the top of the tower is now in daylight, and the parade is ruined. So the star boundary actually shrinks the region we're interested in instead of expanding it.

By symmetry, $6/7$ of the expected perimeter comes from planet boundaries and $1/7$ comes from the star boundary. The net first order change in area is

$$\Delta A = \frac{6}{7} \cdot \frac{7}{11}\pi R \cdot r - \frac{1}{7} \cdot \frac{7}{11}\pi R \cdot r = \frac{6}{11}\pi R r - \frac{1}{11}\pi R r = \frac{5}{11}\pi R r$$

As a fraction of the total surface area $4\pi R^2$:

$$\frac{\Delta A}{4\pi R^2} = \frac{(5/11)\pi R r}{4\pi R^2} = \frac{5}{44} \cdot \frac{r}{R}$$

So the linearized probability of seeing the parade from the tower is

$$P = \frac{1}{44} + \frac{5}{44} \cdot \frac{r}{R}$$

which gives $\alpha = 1/44$ and $\beta = 5/44$.

There is more to say about what happens when $r$ is not small relative to $R$, and whether the linear approximation remains useful at larger tower sizes.
