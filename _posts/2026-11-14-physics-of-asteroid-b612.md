---
layout: post
title: "Physics of Asteroid B-612 from Antoine de Saint-Exupéry's The Little Prince"
excerpt: "What the little prince's home planet would need to be made of for him to walk on it, how he could watch so many sunsets in one day, and why his rose could never have had air to breathe."
date: 2026-11-14
categories: [physics]
---

The little prince lives on asteroid B-612, a planet so small that it's barely bigger than a house. He walks
around it, sweeps out its volcanoes, pulls up baobab sprouts, and tends a single rose that he keeps under
a glass globe at night. He also tells the pilot that he once watched the sun set 44 times in a single day,
just by moving his chair a few steps each time. (Some translations say 43.) The book never asks whether any
of this is physically possible, and that's the right choice for the book. But the numbers are easy to work
out, and they turn out to be surprising in both directions. Some of what the prince describes is
impossible by many orders of magnitude, and one part of it, the sunsets, works almost exactly as told.

I'll take B-612 to be a sphere of radius \\(R = 10\\) m. A house is somewhere around 10 to 20 m across, so
this is generous if anything. I'll give the prince an eye height of about 1 m standing, since he's a child,
and I'll assume B-612 orbits at roughly Earth's distance from the sun, coming back to that assumption at the
end.

Start with gravity. For a uniform sphere of density \\(\rho\\), the mass is
\\(M = \frac{4}{3} \pi R^3 \rho\\), and the gravitational acceleration at the surface is

\\[g = \frac{G M}{R^2} = \frac{G}{R^2} \cdot \frac{4}{3} \pi R^3 \rho = \frac{4}{3} \pi G \rho R\\]

At fixed density, the surface gravity is proportional to the radius, which is the basic problem with small planets. An ordinary asteroid is made of rock, with a density of around \\(3{,}000\\) kg/m³. Plugging in,

\\[g = \frac{4}{3} \pi \times 6.674 \times 10^{-11} \times 3000 \times 10 \approx 8.4 \times 10^{-6} \text{ m/s}^2\\]

which is less than a millionth of Earth's \\(9.81\\) m/s². A rocky B-612 would have a total mass of about
\\(1.3 \times 10^{7}\\) kg, roughly the mass of a large ship.

The more useful number is the escape velocity, the speed at which something thrown upward never comes back.
Setting the kinetic energy equal to the gravitational potential energy at the surface,

\\[\frac{1}{2} m v\_{\text{esc}}^2 = \frac{G M m}{R} \quad \Longrightarrow \quad v\_{\text{esc}} = \sqrt{\frac{2 G M}{R}} = \sqrt{2 g R} = R \sqrt{\frac{8 \pi G \rho}{3}}\\]

For the rocky B-612,

\\[v\_{\text{esc}} = 10 \times \sqrt{\frac{8 \pi \times 6.674 \times 10^{-11} \times 3000}{3}} = 10 \times \sqrt{1.68 \times 10^{-6}} \approx 1.3 \text{ cm/s}\\]

That's about the speed of a slowly crawling ant. A person standing up from a chair moves their center of mass at
something like half a meter per second, about forty times faster than that. If the prince stood up from
watching a sunset, he would leave B-612 permanently.

He could, in principle, orbit it instead. A circular orbit just above the surface needs a speed where gravity
supplies the centripetal acceleration, \\(v\_{\text{circ}}^2 / R = G M / R^2\\), so
\\(v\_{\text{circ}} = v\_{\text{esc}} / \sqrt{2} \approx 0.92\\) cm/s. The period of that orbit is

\\[T = \frac{2 \pi R}{v\_{\text{circ}}} = \frac{2 \pi R}{\sqrt{G M / R}} = 2 \pi \sqrt{\frac{R^3}{G \cdot \frac{4}{3} \pi R^3 \rho}} = \sqrt{\frac{3 \pi}{G \rho}}\\]

The radius cancels completely. The period of a grazing orbit around any uniform sphere depends only on its
density. For rock, \\(T = \sqrt{3 \pi / (6.674 \times 10^{-11} \times 3000)} \approx 6{,}860\\) s, or about an
hour and fifty minutes. It's close to the period of a grazing orbit around Mars, about 100 minutes, since Mars is only a little denser than ordinary rock. So the prince could circle his home in a little under two hours, drifting along at under a
centimeter per second, as long as nothing bumped him.

For the prince to walk around B-612 the way the book describes, he needs something close to Earth's gravity.
Inverting the formula for \\(g\\),

\\[\rho = \frac{3 g}{4 \pi G R} = \frac{3 \times 9.81}{4 \pi \times 6.674 \times 10^{-11} \times 10} \approx 3.5 \times 10^{9} \text{ kg/m}^3\\]

That's about 1.2 million times the density of rock, and it puts B-612 in the range of the material at the
center of a white dwarf star. A teaspoon of it would have a mass of about 18 tonnes. The
whole planet would have a mass of \\(M = \frac{4}{3} \pi (10)^3 \times 3.5 \times 10^9 \approx 1.5 \times 10^{13}\\)
kg, about the mass of a mountain a couple of kilometers across, packed into a ball 20 m wide. With Earth gravity at the surface, the
escape velocity becomes \\(\sqrt{2 g R} = \sqrt{2 \times 9.81 \times 10} \approx 14\\) m/s, so at least the prince
could stand up safely.

The trouble is that matter this dense can't hold itself together at this size. At \\(3.5 \times 10^9\\)
kg/m³, the atoms are crushed and the electrons form a degenerate gas, where the pressure comes from the Pauli
exclusion principle and doesn't depend on temperature. In a white dwarf, the star's own enormous gravity holds
that pressure in. On B-612, it has to be done by the gravity of a 10 m ball.

To compare the two, start with the pressure gravity can provide. For a uniform sphere, the pressure at
radius \\(r\\) satisfies hydrostatic balance, \\(dP / dr = -\rho g(r)\\), with the gravity inside the sphere
\\(g(r) = \frac{4}{3} \pi G \rho r\\). Integrating from the center to the surface, where the pressure is zero,

\\[P\_c = \int\_0^R \rho \cdot \frac{4}{3} \pi G \rho r \, dr = \frac{4}{3} \pi G \rho^2 \cdot \frac{R^2}{2} = \frac{2 \pi}{3} G \rho^2 R^2\\]

which for the dense B-612 gives \\(P\_c \approx 1.7 \times 10^{11}\\) Pa, about half the pressure at the center
of the Earth. That sounds like a lot, until you compute what the electrons push back with.

Each nucleus in white dwarf matter comes with about one electron per two nucleon masses, so the electron number
density is \\(n\_e = \rho / (2 m\_p) \approx 1.0 \times 10^{36}\\) electrons per cubic meter. Filling momentum
states up to the Fermi momentum, two electrons per state, gives
\\(n\_e = \frac{8 \pi p\_F^3}{3 h^3}\\), or \\(p\_F = \hbar (3 \pi^2 n\_e)^{1/3}\\). Here
\\(p\_F c \approx 0.62\\) MeV, above the electron's rest energy of 0.51 MeV, so the electrons are relativistic. In
that limit each electron carries energy \\(p c\\), and the pressure of a degenerate gas works out to the expression below. The nonrelativistic formula gives nearly the same number here, so the result doesn't depend on which limit you use.

\\[P\_{\text{deg}} = \frac{\hbar c}{4} (3 \pi^2)^{1/3} n\_e^{4/3} \approx 2.6 \times 10^{22} \text{ Pa}\\]

The degeneracy pressure is about \\(1.5 \times 10^{11}\\) times larger than anything B-612's gravity can hold in.
Nothing stops it from expanding. The energy stored in that compressed electron gas is on the order of
\\(P\_{\text{deg}} \times V \approx 2.6 \times 10^{22} \times \frac{4}{3} \pi (10)^3 \approx 10^{26}\\) J, which is
around 26 billion megatons of TNT. A planet dense enough to walk on would explode the instant it existed.

Even setting that aside, the prince would feel strange on it. His feet and his head are at different distances
from the center, and on a planet this small the difference is a large fraction of the radius. At his feet the
gravity is \\(g\\), and at his head, a height \\(h\\) higher,

\\[g(R + h) = \frac{G M}{(R + h)^2} = g \frac{R^2}{(R + h)^2} = 9.81 \times \frac{100}{121} \approx 8.1 \text{ m/s}^2\\]

for \\(h = 1\\) m. The gravity at his head would be about 17% weaker than at his feet. For small
\\(h\\), expanding \\((1 + h / R)^{-2} \approx 1 - 2 h / R\\) gives a difference of \\(2 g h / R\\), which shows that the
stretching is controlled by the ratio of his height to the planet's radius. On Earth that ratio is about
\\(10^{-7}\\). On B-612 it's a tenth.

So for the gravity, the book is impossible in either direction. A rock B-612 would let the prince float away by
standing up, and a B-612 he could walk on would explode. The sunsets turn out very differently.

Watching a sunset depends on where the horizon is, and on a small planet the horizon is close and steep. An eye
at height \\(h\\) above a sphere of radius \\(R\\) sees the surface out to the point where the line of sight is
tangent to the sphere. The tangent line is perpendicular to the radius at that point, so the line of sight, the
radius to the tangent point, and the line from the center to the eye form a right triangle with hypotenuse
\\(R + h\\). The distance to the horizon is

\\[d = \sqrt{(R + h)^2 - R^2} = \sqrt{2 R h + h^2}\\]

and the horizon lies below the eye's horizontal plane by the dip angle \\(\delta\\), with

\\[\cos \delta = \frac{R}{R + h}\\]

On Earth, for a 1 m eye height, \\(\delta\\) is about 0.03°, too small to notice. On B-612, with \\(R = 10\\) m,

\\[d = \sqrt{2 \times 10 \times 1 + 1} \approx 4.6 \text{ m}, \qquad \delta = \arccos\!\left( \frac{10}{11} \right) \approx 24.6°\\]

The horizon is less than five meters away, and it sits nearly a quarter of a right angle below eye level. Sitting
down, with eyes at 0.5 m, the dip is \\(\arccos(10 / 10.5) \approx 17.8°\\). The sun sets when it drops below the
horizon, so it sets later for a standing observer than a sitting one, by the difference in dip angle.

Now the rotation. The book doesn't say how long a day on B-612 is, so suppose it rotates once every 24 hours, like
Earth. The sun then moves across the sky at \\(360° / 24 \text{ h} = 15°\\) per hour, or a quarter of a degree per
minute. The sun's disc is about 0.53° across, so a single sunset, from the first edge touching the horizon to the
last edge disappearing, takes about

\\[\frac{0.53°}{0.25° \text{ per minute}} \approx 2.1 \text{ minutes}\\]

When the sun has just set, walking a distance \\(s\\) back toward it moves the prince by an angle \\(s / R\\) around the
planet, which raises the sun by the same angle above his horizon. It then sets again after the planet has rotated by
that angle, which takes

\\[\Delta t = \frac{s / R}{\omega} = \frac{s / R}{2 \pi / (24 \text{ h})}\\]

For one step of \\(s = 1\\) m, the angle is \\(1 / 10\\) radian, about 5.7°, and the sun sets again after

\\[\Delta t = \frac{0.1}{2 \pi / 86{,}400 \text{ s}} \approx 1{,}375 \text{ s} \approx 23 \text{ minutes}\\]

So 44 sunsets, each followed by a one-meter move of his chair, take \\(44 \times 23 \approx 1{,}000\\) minutes, about 17
hours. The book's account works almost exactly as told on a planet with a 10 m radius and a 24 hour day. Each sunset
lasts about two minutes, there are about twenty minutes of daylight between them, and the whole sequence fills most of
a day. The only thing B-612 can't provide is the gravity to keep the chair on the ground.

The steep horizon gives him an even easier way to do it. If he's sitting when the sun sets and simply stands up, the dip
increases from 17.8° to 24.6°, so the sun is suddenly 6.9° above his horizon again. At 15° per hour, it sets the second
time about

\\[\frac{6.9°}{15° \text{ per hour}} \approx 27 \text{ minutes}\\]

later. On B-612, standing up brings back nearly half an hour of sunlight. On Earth, the same move gains about two seconds.

He could also stay at sunset permanently. Keeping the sun right on the horizon means walking at the speed the ground
moves under it, \\(v = \omega R\\). On Earth's equator that's \\(2 \pi \times 6.371 \times 10^6 \text{ m} / 86{,}400 \text{ s} \approx 463\\)
m/s, faster than a commercial jet. On B-612 it's

\\[v = \frac{2 \pi \times 10}{86{,}400} \approx 0.73 \text{ mm/s}\\]

which is about 7 cm every 100 seconds. He could hold the sun on the horizon all day by shuffling his chair a few
centimeters every couple of minutes.

The last question is the rose. On the planet, the rose complains about drafts and asks for the glass globe at night,
which implies there's air on B-612 to have drafts in. Whether a planet keeps an atmosphere depends on how fast its gas
molecules move compared with its escape velocity. In a gas at temperature \\(T\\), molecules of mass \\(m\\) have a range
of speeds, and the most probable speed in the Maxwell-Boltzmann distribution is

\\[v\_{\text{th}} = \sqrt{\frac{2 k T}{m}}\\]

For nitrogen at 288 K, with \\(m = 28 \times 1.66 \times 10^{-27}\\) kg,

\\[v\_{\text{th}} = \sqrt{\frac{2 \times 1.38 \times 10^{-23} \times 288}{4.65 \times 10^{-26}}} \approx 414 \text{ m/s}\\]

A useful way to compare is the ratio of gravitational binding energy to thermal energy, which I'll write as

\\[\lambda = \frac{G M m}{k T R} = \left( \frac{v\_{\text{esc}}}{v\_{\text{th}}} \right)^2\\]

where the second form follows from \\(v\_{\text{esc}}^2 = 2 G M / R\\) and \\(v\_{\text{th}}^2 = 2 k T / m\\). For Earth, with
\\(v\_{\text{esc}} = 11.2\\) km/s, \\(\lambda \approx 730\\), and only a vanishingly small fraction of molecules ever reach
escape speed. For a rocky B-612, \\(\lambda \approx (0.013 / 414)^2 \approx 10^{-9}\\). For the white dwarf B-612, with
\\(v\_{\text{esc}} = 14\\) m/s, \\(\lambda \approx (14 / 414)^2 \approx 0.0011\\). Even the impossibly dense version of the
planet can't come close.

It helps to see what \\(\lambda\\) means for the shape of an atmosphere. In an isothermal atmosphere, the number density
\\(n(r)\\) follows the Boltzmann factor for the gravitational potential energy \\(-G M m / r\\),

\\[n(r) = n(R) \exp\!\left( \frac{G M m}{k T} \left( \frac{1}{r} - \frac{1}{R} \right) \right)\\]

As \\(r \to \infty\\), the density doesn't go to zero. It approaches a constant,

\\[\frac{n(\infty)}{n(R)} = \exp\!\left( -\frac{G M m}{k T R} \right) = e^{-\lambda}\\]

For Earth, \\(e^{-730}\\) is effectively zero, and the atmosphere is bound. For the dense B-612,
\\(e^{-0.0011} \approx 0.999\\). The density at infinity is 99.9% of the density at the surface, which means gravity
does essentially nothing to hold the gas. The usual scale height of an atmosphere, \\(H = k T / (m g)\\), comes out to
about 8.7 km for nitrogen under Earth gravity, and B-612 is only 10 m in radius. Its atmosphere would have to extend
nearly a thousand planetary radii before thinning out, and the planet's gravity falls off long before that.

The fraction of molecules moving faster than escape velocity makes the same point. For a Maxwell-Boltzmann
distribution, the fraction with speed above \\(v\\), writing \\(x = v / v\_{\text{th}}\\), is

\\[P(\text{speed} > v) = 1 - \operatorname{erf}(x) + \frac{2 x}{\sqrt{\pi}} e^{-x^2}\\]

With \\(x = 14 / 414 \approx 0.034\\) for the dense planet, this is 0.99997. Essentially every molecule is moving fast
enough to leave, and each one crosses the planet's size in about \\(R / v\_{\text{th}} \approx 10 / 414 \approx 0.024\\) s.
Any air on B-612 would be gone in a fraction of a second.

So the rose lives in a vacuum, and its temperature is set by sunlight and thermal radiation alone. At Earth's distance
from the sun, sunlight delivers \\(S \approx 1{,}361\\) W/m². A surface that absorbs all of it and radiates as a blackbody,
facing the sun directly, reaches a temperature where absorbed and emitted power balance,

\\[\sigma T^4 = S \quad \Longrightarrow \quad T = \left( \frac{1361}{5.67 \times 10^{-8}} \right)^{1/4} \approx 394 \text{ K}\\]

which is about 121 °C. Averaged over a spinning sphere, the absorbed sunlight is spread over four times the area that
intercepts it, giving \\(T = (S / 4 \sigma)^{1/4} \approx 278\\) K, close to freezing. If B-612 is in the asteroid belt,
at about 2.7 times Earth's distance, sunlight is weaker by \\(2.7^2\\), and the temperatures drop by a factor of
\\(\sqrt{2.7}\\), to 240 K facing the sun and 169 K on average.

Nights would be much harder on the rose. Model a petal as a sheet of water 0.3 mm thick, with heat capacity per unit area
\\(C = \rho c d = 1000 \times 4180 \times 3 \times 10^{-4} \approx 1{,}250\\) J/(m² K). With no air, it loses heat only by
radiating from both faces, at \\(2 \sigma T^4 \approx 680\\) W/m² near 278 K, so it cools at

\\[\frac{dT}{dt} = -\frac{2 \sigma T^4}{C} \approx -\frac{680}{1250} \approx -0.54 \text{ K/s}\\]

Starting from 15 °C, the petal reaches freezing in under 30 seconds after sunset. Before it froze, the water in it would
already be boiling off, since the vapor pressure of water at 15 °C is about 1.7 kPa and the surrounding pressure is
zero.

That gives the glass globe a real job. Sealed, it would hold a small pocket of air around the rose, which the planet
itself can't. Glass also absorbs thermal infrared, so it would trap much of the heat the rose radiates and slow the
nighttime cooling considerably. The prince puts the globe over the rose to protect it from drafts and the cold, and on
B-612 that's the one piece of equipment that would do the most good, though it would need to stay on during the day as
well.

There are a few assumptions behind these numbers that would change them. I took the radius as 10 m. Since surface gravity
scales with radius at fixed density, a 20 m B-612 would need half the density to give Earth gravity, still about 600,000
times denser than rock, which doesn't change any conclusion. The sunset calculation assumed a 24 hour day, but the
formula \\(\Delta t = (s / R) / \omega\\) works for any rotation period, so you can read off whatever day length makes your
preferred number of sunsets fit. I ignored the volcanoes, which would vent gas that escapes as fast as everything else,
and I treated the petal as pure water, which overstates its heat capacity slightly and makes the cooling estimate, if
anything, too slow.

I'd like to do the lamplighter's planet next, which turns once a minute and keeps speeding up. That one has a short answer for how dense a planet has to be to survive a one minute day before the lamplighter gets thrown off.
