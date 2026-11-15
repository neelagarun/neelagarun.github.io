---
layout: post
title: "A Cell Sitting on a Nanoscale Coaxial Electrode: Solving Laplace's Equation Around the Membrane"
excerpt: "The coax post estimated a cell's membrane voltage as if the cell sat in a uniform field. Solving the full three-dimensional problem with spherical harmonics and images shows the response is concentrated in a patch a few hundred nanometers wide under the cell, and is hundreds of times larger than the uniform-field estimate."
date: 2026-10-29
categories: [physics]
image: /assets/images/cell_membrane_patch.png
---

In the post on [why a nanoscale coaxial electrode keeps its field local](/physics/2026/06/22/nanocoax-field-locality.html),
I worked out the far field of the electrode and then estimated what a cell would feel by plugging the local field into the
standard formula for a sphere in a uniform field, \\(\Delta V\_m = \frac{3}{2} E R \cos \theta\\). I said at the time that this
was only a scaling argument, since the field near the electrode changes over a few hundred nanometers and a cell is several
microns across, and I left the case of a cell sitting right on top of the electrode for later. This post does that case
properly. The cell becomes a boundary in a three-dimensional Laplace problem, the field gets expanded in spherical harmonics
around it, and the substrate gets handled with images. The answer turns out to be very different from the uniform-field
estimate, both in size and in where on the cell it happens.

The setup follows the earlier post. The substrate is an insulating plane at \\(z = 0\\), and the coaxial electrode at the origin
is modeled as a ring of current \\(I\\) leaving the plane at radius \\(a = 100\\) nm and a ring returning it at radius
\\(b = 300\\) nm. The cell is a sphere of radius \\(R\_c = 5\\) μm centered on the electrode's axis at height \\(h = R\_c + g\\), so
\\(g\\) is the gap between the bottom of the cell and the substrate. The fluid around everything is saline with conductivity
\\(\sigma\\), and in the steady state the potential satisfies \\(\nabla^2 \phi = 0\\) everywhere in the fluid.

The cell needs boundary conditions, and they come from what the membrane is doing. A lipid membrane is a very good insulator,
so once it has charged up no current crosses it, which means \\(\partial \phi / \partial n = 0\\) on the outside surface. The
inside of the cell is a conducting fluid, and with no current flowing in it, it's an equipotential at some value
\\(\phi\_{\text{in}}\\). That value is fixed by charge. The membrane acts as a capacitor with capacitance \\(C\_m\\) per unit area,
storing charge \\(C\_m (\phi\_{\text{in}} - \phi\_{\text{out}})\\) per unit area, and since the cell started out neutral the total
has to be zero:

\\[\oint C\_m \big( \phi\_{\text{in}} - \phi\_{\text{out}}(\theta) \big) \, dA = 0 \quad \Longrightarrow \quad \phi\_{\text{in}} = \frac{1}{4 \pi R\_c^2} \oint \phi\_{\text{out}} \, dA\\]

so the inside sits at the average of the outside surface potential, and the voltage across the membrane at polar angle
\\(\theta\\), measured from the top of the cell, is \\(\Delta V\_m(\theta) = \phi\_{\text{in}} - \phi\_{\text{out}}(R\_c, \theta)\\).

The first step is the field the electrode would make with no cell present, written in a form centered on the cell. From the
coax post, the potential of current leaving an insulating plane is the free-space potential doubled, so a point source
\\(I\\) on the plane gives \\(I / (2 \pi \sigma \lvert \mathbf{x} - \mathbf{x}\' \rvert)\\). For a point \\(\mathbf{x}\\) at distance
\\(r\\) from the cell's center and a source point \\(\mathbf{x}\'\\) at distance \\(\rho\\) from it, with \\(r < \rho\\), the inverse
distance expands in Legendre polynomials,

\\[\frac{1}{\lvert \mathbf{x} - \mathbf{x}\' \rvert} = \sum\_{l=0}^{\infty} \frac{r^l}{\rho^{l+1}} P\_l(\cos \gamma)\\]

where \\(\gamma\\) is the angle between the two directions as seen from the center. A ring of radius \\(c\\) on the plane sits at
distance \\(\rho\_c = \sqrt{c^2 + h^2}\\) from the center, at a fixed polar angle \\(\beta\_c\\) with \\(\cos \beta\_c = -h / \rho\_c\\), since
the ring is below the center. Averaging around the ring with the addition theorem, the same step as in the coax post, replaces
\\(P\_l(\cos \gamma)\\) with \\(P\_l(\cos \theta) P\_l(\cos \beta\_c)\\). So near the cell the electrode's potential is

\\[\phi\_{\text{inc}}(r, \theta) = \sum\_{l=0}^{\infty} A\_l^{(0)} r^l P\_l(\cos \theta), \qquad A\_l^{(0)} = \frac{I}{2 \pi \sigma} \left[ \frac{P\_l(\cos \beta\_a)}{\rho\_a^{l+1}} - \frac{P\_l(\cos \beta\_b)}{\rho\_b^{l+1}} \right]\\]

and this converges everywhere on the cell's surface as long as \\(R\_c < \rho\_a\\), which holds for any positive gap. How fast it
converges is another matter. The terms shrink roughly like \\((R\_c / \rho)^l\\), and for a gap of half a micron that ratio is
about 0.91, so it takes on the order of a hundred terms to settle. I used up to 300.

The cell's response to each term can be found exactly. Outside a sphere, the axisymmetric solutions of Laplace's equation are
\\(r^l P\_l(\cos \theta)\\) and \\(r^{-(l+1)} P\_l(\cos \theta)\\), so the potential around the cell is

\\[\phi(r, \theta) = \sum\_{l=0}^{\infty} \left( A\_l r^l + \frac{B\_l}{r^{l+1}} \right) P\_l(\cos \theta)\\]

where the \\(A\_l\\) describe the field arriving at the cell and the \\(B\_l\\) describe the field the cell sends back out. The
insulating membrane requires \\(\partial \phi / \partial r = 0\\) at \\(r = R\_c\\) for every \\(\theta\\), and since the Legendre
polynomials are independent, each \\(l\\) has to satisfy it separately,

\\[l A\_l R\_c^{l-1} - (l + 1) \frac{B\_l}{R\_c^{l+2}} = 0 \quad \Longrightarrow \quad B\_l = \frac{l}{l + 1} A\_l R\_c^{2l+1}\\]

Putting that back in at the surface,

\\[\phi\_{\text{out}}(R\_c, \theta) = \sum\_{l=0}^{\infty} A\_l R\_c^l \left( 1 + \frac{l}{l + 1} \right) P\_l(\cos \theta) = \sum\_{l=0}^{\infty} \frac{2 l + 1}{l + 1} A\_l R\_c^l P\_l(\cos \theta)\\]

The surface average of every \\(P\_l\\) with \\(l \geq 1\\) is zero, so \\(\phi\_{\text{in}}\\) is just the \\(l = 0\\) term, and the membrane
voltage is

\\[\Delta V\_m(\theta) = -\sum\_{l=1}^{\infty} \frac{2 l + 1}{l + 1} A\_l R\_c^l P\_l(\cos \theta)\\]

The \\(l = 1\\) term alone is the uniform-field result. A uniform field \\(E\\) along \\(z\\) has \\(\phi = -E r \cos \theta\\), so
\\(A\_1 = -E\\), and the formula gives \\(\Delta V\_m = \frac{3}{2} E R\_c \cos \theta\\), which is where the factor of \\(3/2\\) in the
coax post came from. The higher terms are what the uniform-field estimate leaves out.

There's still the substrate. The incident field already satisfies the insulating condition on the plane, but the field the cell
sends back, the \\(B\_l\\) terms, doesn't. Reflected currents can't pass through the substrate either, and the fix is the same as
for a source on the plane: mirror the cell's outgoing field in the plane. That puts an image cell at height \\(-h\\), a distance
\\(d = 2 h\\) below the real one, carrying multipoles \\(B\_n\\) whose axis is flipped by the reflection. The image's field is then
part of what arrives at the real cell, so it has to be expanded around the real cell's center and added to the \\(A\_l\\).

That re-expansion is easiest along the axis. An axisymmetric solution of Laplace's equation is completely determined by its
values on the axis, since \\(\sum A\_l r^l P\_l(\cos \theta)\\) evaluated at \\(\theta = 0\\) is just \\(\sum A\_l z^l\\), so I only need
the image field's Taylor series in \\(z\\), the height above the real cell's center. The reflection turns \\(P\_n(\cos \theta)\\) into
\\(P\_n(-\cos \theta) = (-1)^n P\_n(\cos \theta)\\), and a point on the axis at height \\(z\\) is at distance \\(z + d\\) from the image
center, so the image's \\(n\\)th multipole contributes \\((-1)^n B\_n / (z + d)^{n+1}\\) on the axis. The binomial series gives

\\[\frac{1}{(z + d)^{n+1}} = \frac{1}{d^{n+1}} \sum\_{l=0}^{\infty} \binom{n + l}{l} \left( -\frac{z}{d} \right)^l\\]

and reading off the coefficient of \\(z^l\\),

\\[A\_l = A\_l^{(0)} + \sum\_{n=1}^{\infty} (-1)^{n+l} \binom{n + l}{l} \frac{B\_n}{d^{n+l+1}}, \qquad B\_n = \frac{n}{n + 1} A\_n R\_c^{2n+1}\\]

Since each \\(B\_n\\) depends on \\(A\_n\\), this is a linear system for the \\(A\_l\\), which I truncated at 300 terms and solved directly.
Solving it all at once accounts for the field bouncing back and forth between the cell and the substrate indefinitely, which adding one reflection at a time would only approach. Working in units where \\(R\_c = 1\\) keeps the binomial coefficients and powers of \\(d\\) from
overflowing.

I'll report the membrane voltage in units of \\(I / (2 \pi \sigma R\_c)\\), which for a current of 1 μA in saline with
\\(\sigma = 1.5\\) S/m and \\(R\_c = 5\\) μm is about 21 mV. For four gaps, the voltage across the membrane at the very bottom of the
cell came out as

\\[g = 2 \text{ μm}: \; -0.045, \qquad g = 1 \text{ μm}: \; -0.365, \qquad g = 0.5 \text{ μm}: \; -2.52, \qquad g = 0.25 \text{ μm}: \; -12.4\\]

and increasing the number of terms from 100 to 300 left the first three unchanged to five digits and moved the last by less than
0.2%. The image terms add between 2% and 9%, more at smaller gaps, which is what you'd expect since the cell and its image get
closer together. For comparison, the uniform-field estimate, using the field at the cell's center, \\(\frac{3}{2} \lvert A\_1 \rvert R\_c\\),
predicts a bottom voltage 21, 71, 227, and 543 times smaller than the full solution for the same four gaps.

The size of the gap is easiest to understand from the near-field limit. When the gap is small compared with the cell, the bottom
of the cell looks locally like a flat insulating wall facing the electrode, and an insulating wall doubles the potential in front
of it, by the same image argument as the substrate. So the outside potential at the bottom of the cell is roughly twice the
electrode's potential at height \\(g\\) with no cell present. From the coax post that's

\\[\phi\_{\text{inc}}(0, g) = \frac{I}{2 \pi \sigma} \left[ \frac{1}{\sqrt{a^2 + g^2}} - \frac{1}{\sqrt{b^2 + g^2}} \right]\\]

which for \\(g = 0.5\\) μm is \\(0.234\\) μm⁻¹ times \\(I / (2 \pi \sigma)\\), or \\(1.17\\) in units of \\(I / (2 \pi \sigma R\_c)\\). Doubling it
gives \\(2.34\\), against \\(2.52\\) from the full calculation, and the remaining difference comes from the cell's curvature and the
repeated reflections. The uniform-field estimate misses this completely, because the field at the cell's center, more than five
microns from the electrode, has already fallen off as the fourth power of distance.

The response is also very localized. The magnitude of \\(\Delta V\_m\\) drops to half its peak within 10.6° of the bottom for a 2 μm gap,
5.8° for 1 μm, 3.4° for 0.5 μm, and 2.3° for 0.25 μm, which on a 5 μm cell are arcs of about 0.92, 0.51, 0.30, and 0.20 μm. The
patch the electrode affects is about as wide as the gap, plus the electrode's own size, and the rest of the membrane barely sees it.
Elsewhere on the cell the voltage is small and of the opposite sign, about \\(+0.004\\) in the same units for the 0.5 μm gap, which
is just the zero-net-charge condition spreading a little compensating voltage over everything outside the patch. With the inner
ring sourcing current, the patch is pushed negative, and reversing the current reverses it.

![Membrane voltage divided by its peak against distance along the membrane from the bottom of the cell, for gaps of 2, 1, 0.5 and 0.25 microns. The voltage falls to half its peak within about 0.9, 0.5, 0.3 and 0.2 microns, and the curve narrows as the gap shrinks.](/assets/images/cell_membrane_patch.png)

To check the series solution I also solved the full problem numerically. Since everything is symmetric about the axis, Laplace's
equation reduces to two dimensions in \\((r, z)\\),

\\[\frac{1}{r} \frac{\partial}{\partial r} \left( r \frac{\partial \phi}{\partial r} \right) + \frac{\partial^2 \phi}{\partial z^2} = 0\\]

which I discretized with a finite-volume scheme on a grid with 20 nm spacing over a region 20 μm wide and 25 μm tall, about a
million cells. The cell is the set of grid cells inside the sphere, with no current allowed into it, the substrate is a no-flux
boundary except for the two source rings, and the potential is set to zero at the far edges. Solving the resulting sparse system
takes a few seconds. As a test of the solver alone, with no cell present it reproduces the exact on-axis potential, for example
1.154 against 1.153 (in units of \\(I / (2 \pi \sigma)\\) per μm) at a height of 0.25 μm.

With the cell in place, the numerical membrane voltage follows the series closely. For the 1 μm gap, at 140° and at 90° the two agree to two significant figures, and closer to the bottom the numerical values run a few percent higher, \\(-0.067\\) against \\(-0.063\\) at 170°. At the bottom itself the numerical value is 5% to 7% larger, \\(-0.387\\) against \\(-0.365\\) for the 1 μm gap, which comes from the grid: the sphere is a staircase of
square cells, and I sampled the potential about a grid spacing outside it, slightly closer to the electrode than the true surface.
Halving the spacing didn't shrink the difference much, which points to the sampling and not to a problem with the series.

For a cell sitting near a coaxial electrode, then, the membrane voltage is close to twice the electrode's potential at the bottom of the cell, confined to a patch of membrane a few hundred nanometers across,
and two to three orders of magnitude larger than the uniform-field estimate. That supports the selectivity argument from the coax
post even more strongly than the original estimate did, since the neighbor 15 μm away still sees the small far field while the cell
on top sees a large, local response.

A few things limit how far this goes. It's a steady-state calculation, and in reality the membrane takes time to charge, on the order of
the membrane capacitance times the resistance of the surrounding fluid, so a short pulse will see less than this. The cell is a perfect
sphere on the axis, while real cells flatten as they adhere. And for an adherent cell the gap can be well under 100 nm, smaller than the
electrode itself, at which point the space under the cell becomes a thin sheet of fluid with its own resistance. That cleft problem is
two-dimensional and has a different structure from anything here, and it's the case I'd like to work through next.
