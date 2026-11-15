---
layout: post
title: "Physics of Asteroid B-612 from Antoine de Saint-Exupéry's The Little Prince"
excerpt: "I work out what B-612 would have to be made of for the little prince to walk on it, which turns out to be impossible either way you try it. His dozens of sunsets in a single day hold up surprisingly well, and his rose never could have had air to breathe."
date: 2026-10-13
redirect_from:
  - /physics/2026/11/14/physics-of-asteroid-b612.html
categories: [physics]
---

I reread The Little Prince recently, and I wanted to work through the physics of the prince's home planet, asteroid B-612, since the book describes it in enough detail to check. It's supposed to be barely bigger than a house, and the prince walks around on it, cleans out its volcanoes, pulls up the baobab sprouts every morning, and keeps a rose that he covers with a glass globe at night. He also tells the pilot about a day when he watched the sun set 44 times by moving his chair a few steps each time, although some translations say 43. The book obviously isn't asking to be checked, but the numbers are easy enough to work out that I couldn't resist. They split in an interesting way: the gravity is wrong by many orders of magnitude whatever you assume, while the sunsets work almost exactly the way he describes them.

Before getting into it, I need a few numbers the book doesn't give. I'll take B-612 to be a sphere of radius \\(R = 10\\) m, since a house is somewhere around 10 to 20 m across, and I'll give the prince an eye height of about 1 m when he's standing, since he's a child. I'll also put B-612 at about Earth's distance from the sun and come back to that at the end, where it sets the temperature.

The first thing to figure out is the gravity. For a uniform sphere of density \\(\rho\\), the mass is \\(M = \frac{4}{3} \pi R^3 \rho\\), and the gravitational acceleration at the surface is

\\[g = \frac{G M}{R^2} = \frac{G}{R^2} \cdot \frac{4}{3} \pi R^3 \rho = \frac{4}{3} \pi G \rho R\\]

so at a fixed density the surface gravity is proportional to the radius, and a planet nearly a million times smaller than Earth gets roughly a million times less gravity if it's made of similar stuff. A real asteroid is mostly rock, with a density of around \\(3{,}000\\) kg/m³, and plugging that in gives

\\[g = \frac{4}{3} \pi \times 6.674 \times 10^{-11} \times 3000 \times 10 \approx 8.4 \times 10^{-6} \text{ m/s}^2\\]

which is less than a millionth of Earth's \\(9.81\\) m/s², on a planet with a total mass of about \\(1.3 \times 10^{7}\\) kg.

Ok, so that's not much gravity. The number that tells you what it feels like is the escape velocity, the speed at which something moving away from the surface never comes back. Setting the kinetic energy equal to the gravitational potential energy at the surface,

\\[\frac{1}{2} m v\_{\text{esc}}^2 = \frac{G M m}{R} \quad \Longrightarrow \quad v\_{\text{esc}} = \sqrt{\frac{2 G M}{R}} = \sqrt{2 g R} = R \sqrt{\frac{8 \pi G \rho}{3}}\\]

and for the rocky version of B-612,

\\[v\_{\text{esc}} = 10 \times \sqrt{\frac{8 \pi \times 6.674 \times 10^{-11} \times 3000}{3}} = 10 \times \sqrt{1.68 \times 10^{-6}} \approx 1.3 \text{ cm/s}\\]

When a person stands up out of a chair, their center of mass moves at something like half a meter per second, which is about forty times that escape velocity, so the first time the prince got up after watching a sunset he would drift off B-612 and never come back down.

He could orbit it, at least in principle. A circular orbit just above the surface needs gravity to supply the centripetal acceleration, \\(v\_{\text{circ}}^2 / R = G M / R^2\\), so \\(v\_{\text{circ}} = v\_{\text{esc}} / \sqrt{2} \approx 0.92\\) cm/s, and the period of that orbit is

\\[T = \frac{2 \pi R}{v\_{\text{circ}}} = \frac{2 \pi R}{\sqrt{G M / R}} = 2 \pi \sqrt{\frac{R^3}{G \cdot \frac{4}{3} \pi R^3 \rho}} = \sqrt{\frac{3 \pi}{G \rho}}\\]

where the radius cancels and leaves a period that depends only on the density. I didn't expect that when I started, and it means that a grazing orbit around anything made of rock takes about the same time, whether the rock is 10 m across or the size of Mars. For \\(\rho = 3000\\) kg/m³ it comes out to \\(\sqrt{3 \pi / (6.674 \times 10^{-11} \times 3000)} \approx 6{,}860\\) s, a little under two hours, and a low orbit around Mars, which is only a bit denser than ordinary rock, takes about 100 minutes.

So a rock B-612 doesn't work, and the natural next question is what B-612 would have to be made of for the prince to walk around on it normally. Setting \\(g\\) equal to Earth's and inverting the formula,

\\[\rho = \frac{3 g}{4 \pi G R} = \frac{3 \times 9.81}{4 \pi \times 6.674 \times 10^{-11} \times 10} \approx 3.5 \times 10^{9} \text{ kg/m}^3\\]

which is about 1.2 million times the density of rock and puts B-612 in the same range as the material inside a white dwarf star. The planet's mass would be \\(\frac{4}{3} \pi (10)^3 \times 3.5 \times 10^9 \approx 1.5 \times 10^{13}\\) kg, and its escape velocity would be \\(\sqrt{2 g R} = \sqrt{2 \times 9.81 \times 10} \approx 14\\) m/s, which at least lets him stand up.

The problem with this version is that matter at that density can't stay that dense unless something squeezes it. At \\(3.5 \times 10^9\\) kg/m³ the electrons are packed so tightly that they form a degenerate gas, where the pressure comes from the Pauli exclusion principle and doesn't depend on temperature at all. In a real white dwarf it's the gravity of a whole star that holds that pressure in. Here it would have to be the gravity of a ball 10 m in radius, so the thing to compare is how much pressure that gravity can supply against how much the electrons push back with.

For a uniform sphere, hydrostatic balance says \\(dP / dr = -\rho g(r)\\), where the gravity inside the sphere at radius \\(r\\) is \\(g(r) = \frac{4}{3} \pi G \rho r\\). Integrating from the center out to the surface, where the pressure drops to zero, gives the central pressure

\\[P\_c = \int\_0^R \rho \cdot \frac{4}{3} \pi G \rho r \, dr = \frac{4}{3} \pi G \rho^2 \cdot \frac{R^2}{2} = \frac{2 \pi}{3} G \rho^2 R^2\\]

and for the dense B-612 that's \\(P\_c \approx 1.7 \times 10^{11}\\) Pa, which is about half the pressure at the center of the Earth and sounds like plenty until you look at the electrons.

White dwarf matter has about one electron for every two nucleon masses, so the electron number density is \\(n\_e = \rho / (2 m\_p) \approx 1.0 \times 10^{36}\\) per cubic meter. Filling up the momentum states to the Fermi momentum with two electrons per state gives \\(n\_e = \frac{8 \pi p\_F^3}{3 h^3}\\), or \\(p\_F = \hbar (3 \pi^2 n\_e)^{1/3}\\), and at this density \\(p\_F c \approx 0.62\\) MeV, which is above the electron's rest energy of 0.51 MeV, so the fastest electrons are relativistic. In that limit each one carries energy \\(p c\\), and the pressure of the degenerate gas is

\\[P\_{\text{deg}} = \frac{\hbar c}{4} (3 \pi^2)^{1/3} n\_e^{4/3} \approx 2.6 \times 10^{22} \text{ Pa}\\]

and I checked that the nonrelativistic formula gives nearly the same number here, so the choice of limit barely changes the answer. Either way the electron pressure is about \\(1.5 \times 10^{11}\\) times larger than anything the planet's gravity can hold in. The energy stored in the compressed gas is on the order of \\(P\_{\text{deg}} \times V \approx 2.6 \times 10^{22} \times \frac{4}{3} \pi (10)^3 \approx 10^{26}\\) J, or about 26 billion megatons of TNT, released more or less instantly as the planet blows itself apart.

Even if something held it together, the prince would notice that his head and his feet are at different distances from the center, which on a planet this small is a big fraction of the radius. With \\(g\\) at his feet and his head \\(h = 1\\) m higher,

\\[g(R + h) = \frac{G M}{(R + h)^2} = g \frac{R^2}{(R + h)^2} = 9.81 \times \frac{100}{121} \approx 8.1 \text{ m/s}^2\\]

so the gravity at his head is about 17% weaker than at his feet. Expanding \\((1 + h / R)^{-2} \approx 1 - 2 h / R\\) for small \\(h\\) gives a difference of \\(2 g h / R\\), and the stretching depends on the ratio of his height to the planet's radius, which is about \\(10^{-7}\\) on Earth and a tenth on B-612.

The sunsets are where things get more interesting, because on a small planet the horizon is very close and sits well below eye level. An eye at height \\(h\\) above a sphere of radius \\(R\\) sees the surface out to the point where the line of sight just grazes the sphere. That line is perpendicular to the radius at the point where it touches, so the line of sight, the radius to that point, and the line from the center up to the eye make a right triangle with hypotenuse \\(R + h\\), and the distance to the horizon is

\\[d = \sqrt{(R + h)^2 - R^2} = \sqrt{2 R h + h^2}\\]

while the horizon sits below the eye's horizontal plane by the dip angle \\(\delta\\), where

\\[\cos \delta = \frac{R}{R + h}\\]

On Earth the dip for a 1 m eye height is about 0.03°, which nobody notices, but on B-612

\\[d = \sqrt{2 \times 10 \times 1 + 1} \approx 4.6 \text{ m}, \qquad \delta = \arccos\!\left( \frac{10}{11} \right) \approx 24.6°\\]

so the horizon is under five meters away and almost 25° below eye level. When he's sitting, with his eyes at about 0.5 m, the dip is \\(\arccos(10 / 10.5) \approx 17.8°\\), and since the sun sets when it drops below the horizon, it sets later for him standing than sitting by the difference between the two angles.

The book never says how long a day on B-612 is, so I'll assume it rotates once every 24 hours like Earth, which means the sun moves across the sky at \\(360° / 24 \text{ h} = 15°\\) per hour, or a quarter of a degree per minute. The sun's disc is about 0.53° across, so one sunset, from the bottom edge touching the horizon until the top edge disappears, takes about

\\[\frac{0.53°}{0.25° \text{ per minute}} \approx 2.1 \text{ minutes}\\]

If the sun has just set and the prince walks a distance \\(s\\) back toward it, he moves through an angle \\(s / R\\) around the planet, which lifts the sun by the same angle above his horizon, and it sets again once the planet has turned through that angle, after

\\[\Delta t = \frac{s / R}{\omega} = \frac{s / R}{2 \pi / (24 \text{ h})}\\]

For a one meter move the angle is \\(1 / 10\\) of a radian, about 5.7°, which gives

\\[\Delta t = \frac{0.1}{2 \pi / 86{,}400 \text{ s}} \approx 1{,}375 \text{ s} \approx 23 \text{ minutes}\\]

and 44 sunsets with a one meter move after each take \\(44 \times 23 \approx 1{,}000\\) minutes, or about 17 hours. I expected this part to fall apart like the gravity did, but it holds up. A 10 m planet with a 24 hour day gives the prince about two minutes of sunset, then about twenty minutes of daylight after each move, and a sequence that fills most of a day, which is pretty close to how he tells it.

There's also an easier way for him to do it that comes straight from the dip angle. If he's sitting when the sun goes down and then stands up, the dip goes from 17.8° to 24.6°, which puts the sun 6.9° back above his horizon. At 15° per hour it takes about

\\[\frac{6.9°}{15° \text{ per hour}} \approx 27 \text{ minutes}\\]

to set again, compared with about two seconds for the same move on Earth. And if he wanted to stay at sunset the whole time, he would have to move along with the ground at \\(v = \omega R\\), which on Earth's equator is \\(2 \pi \times 6.371 \times 10^6 \text{ m} / 86{,}400 \text{ s} \approx 463\\) m/s, but on B-612 is

\\[v = \frac{2 \pi \times 10}{86{,}400} \approx 0.73 \text{ mm/s}\\]

which he could keep up with by moving his chair about 7 cm every hundred seconds.

The rose is the part I find most fun, because the book has it complaining about drafts and asking for the globe at night, which assumes B-612 has air. Whether a planet holds onto an atmosphere depends on how fast its gas molecules move compared with its escape velocity. In a gas at temperature \\(T\\), molecules of mass \\(m\\) have a spread of speeds, and the most probable speed in the Maxwell-Boltzmann distribution is

\\[v\_{\text{th}} = \sqrt{\frac{2 k T}{m}}\\]

which for nitrogen at 288 K, with \\(m = 28 \times 1.66 \times 10^{-27}\\) kg, is

\\[v\_{\text{th}} = \sqrt{\frac{2 \times 1.38 \times 10^{-23} \times 288}{4.65 \times 10^{-26}}} \approx 414 \text{ m/s}\\]

The comparison I'll use is the ratio of gravitational binding energy to thermal energy,

\\[\lambda = \frac{G M m}{k T R} = \left( \frac{v\_{\text{esc}}}{v\_{\text{th}}} \right)^2\\]

where the second form comes from \\(v\_{\text{esc}}^2 = 2 G M / R\\) and \\(v\_{\text{th}}^2 = 2 k T / m\\). For Earth, with \\(v\_{\text{esc}} = 11.2\\) km/s, \\(\lambda \approx 730\\), and almost no molecules ever reach escape speed. For the rocky B-612, \\(\lambda \approx (0.013 / 414)^2 \approx 10^{-9}\\), and even for the white dwarf version, with \\(v\_{\text{esc}} = 14\\) m/s, it's only \\(\lambda \approx (14 / 414)^2 \approx 0.0011\\).

It helps to see what a small \\(\lambda\\) does to the shape of an atmosphere. In an isothermal atmosphere the number density \\(n(r)\\) follows the Boltzmann factor for the gravitational potential energy \\(-G M m / r\\),

\\[n(r) = n(R) \exp\!\left( \frac{G M m}{k T} \left( \frac{1}{r} - \frac{1}{R} \right) \right)\\]

and as \\(r \to \infty\\) the density doesn't go to zero but levels off at

\\[\frac{n(\infty)}{n(R)} = \exp\!\left( -\frac{G M m}{k T R} \right) = e^{-\lambda}\\]

For Earth, \\(e^{-730}\\) is so small it might as well be zero, and the atmosphere stays bound, but for the dense B-612 it's \\(e^{-0.0011} \approx 0.999\\), so the density far away is 99.9% of the density at the surface and gravity is doing almost nothing to hold the gas in place. The scale height \\(H = k T / (m g)\\) for nitrogen under Earth gravity is about 8.7 km, close to a thousand times B-612's radius, and the planet's gravity has fallen off long before an atmosphere could thin out over that distance.

You can get at the same thing from the fraction of molecules moving faster than escape velocity. For a Maxwell-Boltzmann distribution, writing \\(x = v / v\_{\text{th}}\\), the fraction with speed above \\(v\\) is

\\[P(\text{speed} > v) = 1 - \operatorname{erf}(x) + \frac{2 x}{\sqrt{\pi}} e^{-x^2}\\]

and with \\(x = 14 / 414 \approx 0.034\\) for the dense planet this is 0.99997, so nearly every molecule is fast enough to leave, and each one crosses a distance the size of the planet in about \\(R / v\_{\text{th}} \approx 10 / 414 \approx 0.024\\) s. Whatever air B-612 started with would be gone in a few hundredths of a second.

That leaves the rose in a vacuum, where its temperature is set only by sunlight coming in and thermal radiation going out. At Earth's distance from the sun, sunlight delivers \\(S \approx 1{,}361\\) W/m², and a surface facing the sun that absorbs all of it and radiates as a blackbody settles where the two balance,

\\[\sigma T^4 = S \quad \Longrightarrow \quad T = \left( \frac{1361}{5.67 \times 10^{-8}} \right)^{1/4} \approx 394 \text{ K}\\]

which is about 121 °C. Averaged over a spinning sphere, the sunlight is spread over four times the area that catches it, which gives \\(T = (S / 4 \sigma)^{1/4} \approx 278\\) K, a little above freezing. If B-612 is out in the asteroid belt at about 2.7 times Earth's distance, the sunlight is weaker by a factor of \\(2.7^2\\) and both temperatures drop by \\(\sqrt{2.7}\\), to 240 K facing the sun and 169 K on average.

At night it gets much worse for the rose. If I model a petal as a sheet of water 0.3 mm thick, its heat capacity per unit area is \\(C = \rho c d = 1000 \times 4180 \times 3 \times 10^{-4} \approx 1{,}250\\) J/(m² K), and with no air around it the only way it loses heat is by radiating from both sides, at \\(2 \sigma T^4 \approx 680\\) W/m² near 278 K, so it cools at

\\[\frac{dT}{dt} = -\frac{2 \sigma T^4}{C} \approx -\frac{680}{1250} \approx -0.54 \text{ K/s}\\]

and starting at 15 °C it reaches freezing less than 30 seconds after sunset. In practice the water would be boiling off before that, since water at 15 °C has a vapor pressure of about 1.7 kPa and there's nothing outside pushing back.

So the glass globe is the one thing on the planet that would help the rose. If it's sealed, it holds a small pocket of air, which B-612 can't do on its own. Glass also absorbs thermal infrared, so it would trap a lot of the heat the rose radiates and slow down how fast it cools at night, although the prince would need to leave it on during the day too.

A few of these numbers depend on choices I made. I picked a 10 m radius, and because surface gravity scales with radius at fixed density, a 20 m B-612 would only need half the density to give Earth gravity, which is still around 600,000 times denser than rock and changes none of the conclusions. The sunset numbers assume a 24 hour day, but the formula \\(\Delta t = (s / R) / \omega\\) works for any rotation period, so you can pick whatever day length makes your preferred number of sunsets fit. I ignored the volcanoes, which would vent gas that escapes as quickly as everything else, and I treated the petal as pure water, which probably overstates its heat capacity a bit and so makes the cooling estimate slower than it would really be.

I'd like to do the lamplighter's planet next, which turns once a minute and keeps speeding up, since there's a short calculation for how dense a planet has to be to survive a one minute day before the lamplighter gets thrown off.
