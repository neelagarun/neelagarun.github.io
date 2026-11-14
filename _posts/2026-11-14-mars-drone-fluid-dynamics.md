---
layout: post
title: "Flying a Drone on Mars: Thin Air, Slow Sound, and the Fluid Dynamics of Hovering in Less Than 1% of an Atmosphere"
date: 2026-11-14 18:00:00 +0000
categories: [physics]
---

I want to work through what it would take to fly a small rotorcraft on Mars. NASA flew a
small helicopter there in 2021, so it can clearly be done. I'm more interested in why it's hard, and I'll approach that by designing a hypothetical drone on paper, starting from conservation laws, and keeping track of where the Martian atmosphere makes each step harder than it would be on Earth. The numbers below are round values I picked, and the design is my own.

The problem comes from two directions at once. The air is so thin that a rotor has to move a lot of
it to make any thrust, which means big rotors spinning fast. But the speed of sound on Mars is low,
which limits how fast the rotor can spin. And the air is very viscous relative to its density, so
the blades end up working in a regime where ordinary airfoils perform poorly. Most of this post is
about putting numbers on each of those constraints and seeing how they interact.

Start with the atmosphere. Mars's atmosphere is
mostly carbon dioxide, with a surface pressure that changes with season and location but sits around
600 Pa, about 0.6% of Earth's sea level pressure of 101,325 Pa. A typical surface temperature is
around 210 K. For an ideal gas,

\\[p = \rho R_s T \quad \Longrightarrow \quad \rho = \frac{p}{R_s T}, \qquad R_s = \frac{R_u}{\mathcal{M}}\\]

where \\(R_u = 8.314\\) J/(mol K) is the universal gas constant and \\(\mathcal{M}\\) is the molar
mass. For CO₂, \\(\mathcal{M} = 0.04401\\) kg/mol, so

\\[R_s = \frac{8.314}{0.04401} \approx 188.9 \text{ J/(kg K)}, \qquad \rho = \frac{600}{188.9 \times 210} = \frac{600}{39{,}670} \approx 0.0151 \text{ kg/m}^3\\]

Sea level air on Earth is 1.225 kg/m³, so Mars's air is about \\(1.225 / 0.0151 \approx 81\\) times
less dense.

The speed of sound is almost as important, so I'll derive it. Sound is a small pressure wave,
and its speed is set by how much the pressure changes when you compress the gas slightly:
\\(a^2 = (\partial p / \partial \rho)_s\\), where the subscript means the compression happens without
heat exchange, since a sound wave moves too fast for heat to flow. For an ideal gas compressed
adiabatically, \\(p = K \rho^\gamma\\) for some constant \\(K\\), where \\(\gamma\\) is the ratio of
specific heats. Differentiating,

\\[\frac{\partial p}{\partial \rho} = \gamma K \rho^{\gamma - 1} = \gamma \frac{p}{\rho} = \gamma R_s T \quad \Longrightarrow \quad a = \sqrt{\gamma R_s T}\\]

For CO₂ at these temperatures, \\(\gamma \approx 1.3\\), so

\\[a = \sqrt{1.3 \times 188.9 \times 210} = \sqrt{51{,}570} \approx 227 \text{ m/s}\\]

compared with about 340 m/s on Earth. Sound travels about a third slower on Mars. That has a large effect, because the tips of a rotor blade are where the air moves fastest relative to the blade.

The third property is viscosity, and kinetic theory explains why it behaves the way it does. A rough
picture of viscosity is that molecules carry momentum between layers of fluid moving at different
speeds. A molecule travels on average one mean free path \\(\lambda\\) between collisions, moving at
a mean speed \\(\bar{c}\\), and a simple version of the theory gives

\\[\mu \approx \frac{1}{2} \rho \, \bar{c} \, \lambda, \qquad \bar{c} = \sqrt{\frac{8 R_s T}{\pi}}\\]

The mean free path is inversely proportional to the number of molecules per unit volume, so
\\(\lambda \propto 1 / \rho\\), and the product \\(\rho \lambda\\) doesn't depend on density. That's
why the viscosity of a gas depends on temperature but barely at all on pressure. CO₂ at 210 K has
\\(\mu \approx 1.07 \times 10^{-5}\\) Pa s, close to air on Earth. But the quantity that controls
how a flow behaves is the kinematic viscosity, viscosity divided by density,

\\[\nu = \frac{\mu}{\rho} = \frac{1.07 \times 10^{-5}}{0.0151} \approx 7.1 \times 10^{-4} \text{ m}^2/\text{s}\\]

which is about \\(7.1 \times 10^{-4} / 1.5 \times 10^{-5} \approx 47\\) times larger than Earth's air.
In terms of how the flow behaves, Martian air is thick even though there's very little of it.

The same formula lets us check that the air still behaves as a continuum. Rearranging for the mean
free path and using \\(\rho = p / (R_s T)\\),

\\[\lambda = \frac{2 \mu}{\rho \bar{c}} = \frac{2 \mu R_s T}{p} \sqrt{\frac{\pi}{8 R_s T}} = \frac{\mu}{p} \sqrt{\frac{\pi R_s T}{2}}\\]

With \\(\bar{c} = \sqrt{8 \times 188.9 \times 210 / \pi} \approx 318\\) m/s,

\\[\lambda = \frac{2 \times 1.07 \times 10^{-5}}{0.0151 \times 318} \approx 4.5 \times 10^{-6} \text{ m}\\]

about 4.5 microns. For a blade with an 8 cm chord, the Knudsen number is
\\(\text{Kn} = \lambda / c \approx 4.5 \times 10^{-6} / 0.081 \approx 5.5 \times 10^{-5}\\). Slip effects at
surfaces start to matter around \\(\text{Kn} \approx 10^{-3}\\), so we're nearly twenty times below that
and the Navier-Stokes equations apply. So despite the low pressure, the continuum description holds.

One more property: the atmosphere thins with altitude. A thin slab of air of thickness \\(dz\\) has
to be held up by the pressure difference across it, so \\(dp = -\rho g \, dz\\). Substituting
\\(\rho = p / (R_s T)\\) and treating the temperature as constant,

\\[\frac{dp}{p} = -\frac{g}{R_s T} \, dz \quad \Longrightarrow \quad \ln \frac{p}{p_0} = -\frac{g z}{R_s T} \quad \Longrightarrow \quad p(z) = p_0 \, e^{-z / H_s}, \qquad H_s = \frac{R_s T}{g}\\]

With Mars's gravity \\(g = 3.71\\) m/s²,

\\[H_s = \frac{188.9 \times 210}{3.71} \approx 10{,}700 \text{ m}\\]

The density falls by a factor of \\(e\\) every 10.7 km, slightly slower than on Earth, because the
weaker gravity more than makes up for the heavier gas. The comparison in one place:

| Property | Earth (sea level) | Mars (surface) | Ratio |
|---|---|---|---|
| Density | 1.225 kg/m³ | 0.0151 kg/m³ | 1/81 |
| Gravity | 9.81 m/s² | 3.71 m/s² | 0.38 |
| Speed of sound | 340 m/s | 227 m/s | 0.67 |
| Kinematic viscosity | 1.5 × 10⁻⁵ m²/s | 7.1 × 10⁻⁴ m²/s | 47 |

Mars's weaker gravity partly makes up for the thin air, and momentum theory shows by how much. It treats the rotor as an idealized disk that adds
momentum to the air passing through it, without caring how.

Put a disk of area \\(A\\) in still air and suppose it accelerates the flow to the induced velocity
\\(v_i\\) at the disk and to some velocity \\(w\\) far downstream. By conservation of mass, the mass
flow rate is the same through every cross section of the stream tube that passes through the disk,

\\[\dot{m} = \rho A v_i\\]

The air starts at rest far above and ends at speed \\(w\\) far below, so by conservation of momentum
the thrust is the rate at which momentum leaves in the wake,

\\[T = \dot{m} w = \rho A v_i w\\]

There are two ways to pin down \\(w\\), and they give the same answer. The first uses energy. The
power the disk puts into the flow equals the kinetic energy carried away by the wake per unit time,
\\(P = \tfrac{1}{2} \dot{m} w^2\\), and it also equals the force on the air times the speed of the
air at the disk, \\(P = T v_i\\). Setting them equal,

\\[\rho A v_i \, w \, v_i = \frac{1}{2} \rho A v_i \, w^2 \quad \Longrightarrow \quad w = 2 v_i\\]

The second uses Bernoulli's equation, which holds along a streamline anywhere the disk isn't adding
energy. Above the disk, the air goes from rest at the ambient pressure \\(p_\infty\\) to speed
\\(v_i\\) at pressure \\(p_u\\) just above the disk. Below the disk, it goes from \\(v_i\\) at pressure
\\(p_l\\) to \\(w\\) back at \\(p_\infty\\):

\\[p_\infty = p_u + \frac{1}{2} \rho v_i^2, \qquad p_l + \frac{1}{2} \rho v_i^2 = p_\infty + \frac{1}{2} \rho w^2\\]

Adding these, the \\(v_i\\) terms cancel, and the pressure jump across the disk is
\\(p_l - p_u = \tfrac{1}{2} \rho w^2\\). The thrust is the pressure jump times the area, so
\\(T = \tfrac{1}{2} \rho A w^2\\). Setting that equal to \\(\rho A v_i w\\) gives \\(w = 2 v_i\\) again.
Half of the acceleration happens above the disk and half below. Substituting back,

\\[T = 2 \rho A v_i^2 \quad \Longrightarrow \quad v_i = \sqrt{\frac{T}{2 \rho A}}, \qquad P_{\text{ideal}} = T v_i = \frac{T^{3/2}}{\sqrt{2 \rho A}}\\]

In hover the thrust equals the weight, \\(T = m g\\). For the same vehicle with the same rotor, the
ratio of ideal hover power on Mars to Earth is

\\[\frac{P_{\text{Mars}}}{P_{\text{Earth}}} = \left( \frac{g_{\text{Mars}}}{g_{\text{Earth}}} \right)^{3/2} \sqrt{\frac{\rho_{\text{Earth}}}{\rho_{\text{Mars}}}} = \left( \frac{3.71}{9.81} \right)^{3/2} \sqrt{81} = (0.378)^{3/2} \times 9 \approx 0.232 \times 9 \approx 2.1\\]

The weaker gravity cuts the power by a factor of about four, the thin air multiplies it by nine, and
the same drone ends up needing about twice as much power to hover on Mars. The induced velocity is
worse:

\\[\frac{v_{i, \text{Mars}}}{v_{i, \text{Earth}}} = \sqrt{\frac{g_{\text{Mars}}}{g_{\text{Earth}}} \cdot \frac{\rho_{\text{Earth}}}{\rho_{\text{Mars}}}} = \sqrt{0.378 \times 81} = \sqrt{30.6} \approx 5.5\\]

The rotor has to push air downward five and a half times as fast to hold up the same vehicle. A
bigger rotor helps, but only as \\(1 / \sqrt{A}\\): doubling the disk area cuts the ideal power by a
factor of \\(\sqrt{2}\\), about 29%.

To make this concrete I'll pick a design. Take a drone with mass \\(m = 2\\) kg, so its weight on
Mars is \\(W = 2 \times 3.71 = 7.42\\) N, and give it a rotor system of radius \\(R = 0.6\\) m, so the
disk area is \\(A = \pi \times 0.6^2 \approx 1.131\\) m². In practice you'd probably use two
counter-rotating rotors stacked on one shaft, which cancels the torque and keeps the vehicle compact.
I'll treat that pair as a single disk with all four blades counted together. The hover induced
velocity is

\\[v_h = \sqrt{\frac{W}{2 \rho A}} = \sqrt{\frac{7.42}{2 \times 0.0151 \times 1.131}} = \sqrt{\frac{7.42}{0.0342}} = \sqrt{217} \approx 14.7 \text{ m/s}\\]

and the ideal hover power is \\(W v_h = 7.42 \times 14.7 \approx 109\\) W. On Earth, the same drone
weighs 19.6 N, its induced velocity is \\(\sqrt{19.6 / (2 \times 1.225 \times 1.131)} \approx 2.66\\) m/s,
and its ideal hover power is about \\(19.6 \times 2.66 \approx 52\\) W. So far the numbers are
manageable. The harder problems show up when you ask how the blades produce that thrust.

Momentum theory doesn't know about blades, so the next step is blade element theory. Slice a blade
into strips of width \\(dr\\) at radius \\(r\\). Ignoring for now the small effect of the induced
velocity on the local airspeed, a strip sees air moving past it at \\(\Omega r\\), where
\\(\Omega\\) is the angular velocity of the rotor, and produces lift

\\[dL = \frac{1}{2} \rho (\Omega r)^2 c \, C_L \, dr\\]

where \\(c\\) is the chord and \\(C_L\\) is the lift coefficient of that section. With \\(N\\) blades of
constant chord and constant \\(C_L\\), the total thrust is

\\[T = N \int_0^R \frac{1}{2} \rho \Omega^2 r^2 c \, C_L \, dr = \frac{1}{2} N \rho \Omega^2 c \, C_L \cdot \frac{R^3}{3} = \frac{N \rho \Omega^2 c \, C_L R^3}{6}\\]

It's standard to write this in dimensionless form, using the tip speed \\(V_{\text{tip}} = \Omega R\\)
and the solidity \\(\sigma = N c / (\pi R)\\), the fraction of the disk area covered by blades.
Dividing the thrust by \\(\rho A V_{\text{tip}}^2 = \rho \pi R^2 \cdot \Omega^2 R^2\\),

\\[C_T = \frac{T}{\rho A V_{\text{tip}}^2} = \frac{N \rho \Omega^2 c \, C_L R^3 / 6}{\rho \pi R^4 \Omega^2} = \frac{N c}{\pi R} \cdot \frac{C_L}{6} = \frac{\sigma C_L}{6}\\]

The speed of sound breaks this equation. Rearranging for the solidity needed to hover,

\\[\sigma = \frac{6 C_T}{C_L} = \frac{6 W}{\rho A V_{\text{tip}}^2 C_L}\\]

On Earth, if you need more thrust you spin faster, and \\(V_{\text{tip}}^2\\) does the work. On Mars,
\\(\rho\\) in the denominator is 81 times smaller, so to keep the same solidity you'd want to spin
\\(\sqrt{81} = 9\\) times faster. But the tips can't approach the speed of sound. As the local Mach
number near the tip gets close to one, shock waves form on the blade, drag rises sharply, and the
rotor gets loud and inefficient. A common design target is a tip Mach number around 0.7. Below that,
compressibility is a correction. The Prandtl-Glauert rule says the lift slope of a thin airfoil
grows by a factor \\(1 / \sqrt{1 - M^2}\\), which at \\(M = 0.7\\) is
\\(1 / \sqrt{1 - 0.49} = 1 / \sqrt{0.51} \approx 1.40\\). Above it, the flow over the upper surface
of the blade goes locally supersonic and the drag climbs. With \\(a = 227\\) m/s,

\\[V_{\text{tip}} = 0.7 \times 227 \approx 159 \text{ m/s}, \qquad \Omega = \frac{159}{0.6} \approx 265 \text{ rad/s} \approx 2{,}530 \text{ rpm}\\]

Taking a mean lift coefficient \\(C_L = 0.6\\), the thrust coefficient and the required solidity are

\\[C_T = \frac{7.42}{0.0151 \times 1.131 \times 159^2} = \frac{7.42}{431.8} \approx 0.0172, \qquad \sigma = \frac{6 \times 0.0172}{0.6} \approx 0.17\\]

and with four blades the chord is

\\[c = \frac{\sigma \pi R}{N} = \frac{0.172 \times \pi \times 0.6}{4} \approx 0.081 \text{ m}\\]

That's a wide blade by helicopter standards, where solidities under 0.1 are typical.

The same rotor, with the same solidity and lift coefficient, would hover on Earth at a tip speed of

\\[V_{\text{tip, Earth}} = \sqrt{\frac{6 W_{\text{Earth}}}{\rho_{\text{Earth}} A \sigma C_L}} = \sqrt{\frac{6 \times 19.62}{1.225 \times 1.131 \times 0.172 \times 0.6}} = \sqrt{\frac{117.7}{0.1430}} \approx 28.7 \text{ m/s}\\]

So the Martian rotor has to spin about \\(159 / 28.7 \approx 5.5\\) times faster than it would on
Earth, the same factor as the induced velocity. The factors match because both the tip speed and the
induced velocity scale as \\(\sqrt{W / (\rho A)}\\).

Going one level deeper into the blade shows how the pitch has to be set. The
induced velocity tilts the air coming at each blade section downward. At radius \\(r\\), the section
sees the rotational speed \\(\Omega r\\) in the plane and the induced velocity \\(v_i\\) through the
disk, so the air arrives at an inflow angle \\(\phi \approx v_i / (\Omega r)\\). Define the inflow
ratio \\(\lambda = v_i / V_{\text{tip}}\\) and the dimensionless radius \\(x = r / R\\), so
\\(\phi = \lambda / x\\). Combining \\(T = 2 \rho A v_i^2\\) with the definition of \\(C_T\\),

\\[C_T = \frac{2 \rho A v_i^2}{\rho A V_{\text{tip}}^2} = 2 \lambda^2 \quad \Longrightarrow \quad \lambda = \sqrt{\frac{C_T}{2}} = \sqrt{\frac{0.0172}{2}} \approx 0.093\\]

which matches \\(v_h / V_{\text{tip}} = 14.7 / 159 \approx 0.093\\). If the blade is pitched at angle
\\(\theta(x)\\) and the section lift is linear in angle of attack with slope \\(a_0\\), then
\\(C_L(x) = a_0 (\theta - \lambda / x)\\). One convenient choice is the so-called ideal twist,
\\(\theta = \theta_t / x\\), where \\(\theta_t\\) is the pitch at the tip, which makes the inflow uniform.
Then \\(C_L(x) = a_0 (\theta_t - \lambda) / x\\). The thrust from a strip, in the same dimensionless
form as before, is \\(dC_T = \tfrac{1}{2} \sigma C_L x^2 \, dx\\), so

\\[C_T = \frac{\sigma a_0}{2} (\theta_t - \lambda) \int_0^1 x \, dx = \frac{\sigma a_0}{4} (\theta_t - \lambda) \quad \Longrightarrow \quad \theta_t = \frac{4 C_T}{\sigma a_0} + \lambda\\]

With \\(a_0 = 5.7\\) per radian,

\\[\theta_t = \frac{4 \times 0.0172}{0.172 \times 5.7} + 0.093 = 0.070 + 0.093 = 0.163 \text{ rad} \approx 9.3°\\]

More than half of the tip pitch goes into lining the blade up with the downward inflow, and the rest makes lift. The lift coefficient along the blade is then \\(C_L(x) = 5.7 \times 0.070 / x = 0.40 / x\\),
which is 0.40 at the tip, 0.53 at 75% radius, 0.80 at half radius, and 1.33 at 30% radius. That last
value is above what a low Reynolds number airfoil can hold without stalling, so in practice the inner
part of the blade would be twisted less than the ideal and carry less load. The mean value of 0.6 I
used earlier is consistent with this, since
\\(6 C_T / \sigma = 6 \times 0.0172 / 0.172 = 0.6\\).

The viscosity comes in once you look at the flow over the blades.
The Reynolds number at 75% of the radius, roughly where most of the thrust comes from, is

\\[\text{Re} = \frac{V c}{\nu} = \frac{0.75 \times 159 \times 0.081}{7.1 \times 10^{-4}} = \frac{9.66}{7.1 \times 10^{-4}} \approx 13{,}600\\]

The same blade on Earth, at its Earth tip speed, would be at about
\\(0.75 \times 28.7 \times 0.081 / 1.5 \times 10^{-5} \approx 116{,}000\\). And even if you spun it as
fast on Earth as on Mars, Earth's Reynolds number would be far higher. Writing the blade speed as a
Mach number shows why:

\\[\text{Re} = \frac{M a \, c}{\nu}, \qquad \frac{a}{\nu} = \begin{cases} 340 / (1.5 \times 10^{-5}) \approx 2.3 \times 10^7 \text{ m}^{-1} & \text{Earth} \\ 227 / (7.1 \times 10^{-4}) \approx 3.2 \times 10^5 \text{ m}^{-1} & \text{Mars} \end{cases}\\]

At a fixed Mach number and a fixed chord, Martian blades see a Reynolds number about
\\(2.3 \times 10^7 / 3.2 \times 10^5 \approx 70\\) times lower than on Earth. The tip Mach number is
already capped by compressibility, so the only way to raise the Reynolds number is a longer chord,
which means more solidity and heavier blades. A Mars rotor is stuck at low Reynolds number.

At low Reynolds number the boundary layer on the blade stays laminar over most of the chord, and
you can estimate its thickness from a balance of terms in the momentum equation. Inside the boundary
layer, the inertia of the flow, \\(u \, \partial u / \partial x\\), is comparable to the viscous
force, \\(\nu \, \partial^2 u / \partial y^2\\). If the outer speed is \\(U\\), the distance from the
leading edge is \\(x\\), and the thickness is \\(\delta\\), the two terms scale as

\\[\frac{U^2}{x} \sim \frac{\nu U}{\delta^2} \quad \Longrightarrow \quad \delta \sim \sqrt{\frac{\nu x}{U}} = \frac{x}{\sqrt{\text{Re}_x}}\\]

The exact solution for a flat plate, due to Blasius, puts a constant of about 5 in front, so
\\(\delta \approx 5 x / \sqrt{\text{Re}_x}\\). At the trailing edge,

\\[\delta \approx \frac{5 c}{\sqrt{\text{Re}}} = \frac{5 \times 0.081}{\sqrt{13{,}600}} = \frac{0.405}{116.6} \approx 3.5 \text{ mm}\\]

more than 4% of the chord. The wall shear stress scales as \\(\tau_w \sim \mu U / \delta\\), and
dividing by \\(\tfrac{1}{2} \rho U^2\\) gives a skin friction coefficient that scales as
\\(1 / \sqrt{\text{Re}}\\). With the exact constant from the Blasius solution, averaged over the plate,

\\[C_f = \frac{1.328}{\sqrt{\text{Re}}} = \frac{1.328}{116.6} \approx 0.0114\\]

per side. So even a perfectly thin blade with fully attached flow has a drag coefficient of at least
\\(2 C_f \approx 0.023\\) from friction alone. Real airfoils at this Reynolds number usually do worse.
A laminar boundary layer can't handle much adverse pressure gradient, so on the upper surface it
tends to separate, sometimes reattaching as a turbulent layer a little further back, which leaves a
laminar separation bubble that adds drag and makes the lift hard to predict. Thick, rounded airfoils
designed for high Reynolds numbers lose much of their advantage here. Thin cambered plates with sharp
leading edges often do as well or better, because a sharp leading edge fixes where the flow separates.
I'll take a profile drag coefficient \\(c_d = 0.03\\), a little above the friction floor.

Now the power. Each blade section also has drag \\(dD = \tfrac{1}{2} \rho (\Omega r)^2 c \, c_d \, dr\\),
and the motor has to supply the torque that drag creates. The power is the angular velocity times the
torque, so the profile power is

\\[P_0 = N \int_0^R \Omega r \, dD = \frac{N \rho \Omega^3 c \, c_d}{2} \int_0^R r^3 \, dr = \frac{N \rho \Omega^3 c \, c_d R^4}{8}\\]

Writing \\(N c = \sigma \pi R\\) and \\(\Omega R = V_{\text{tip}}\\),

\\[P_0 = \frac{\sigma \pi R \, \rho \, \Omega^3 R^3 \, c_d}{8} = \frac{\sigma c_d}{8} \rho \, (\pi R^2) (\Omega R)^3 = \frac{\sigma c_d}{8} \rho A V_{\text{tip}}^3\\]

Substituting the solidity needed to hover, \\(\sigma = 6 W / (\rho A V_{\text{tip}}^2 C_L)\\),

\\[P_0 = \frac{c_d}{8} \cdot \frac{6 W}{\rho A V_{\text{tip}}^2 C_L} \cdot \rho A V_{\text{tip}}^3 = \frac{3}{4} \, W \, V_{\text{tip}} \, \frac{c_d}{C_L}\\]

When the blades are sized to carry the load, the density cancels, and the profile power depends only on
the weight, the tip speed, and the drag-to-lift ratio of the airfoil. That says two things about Mars.
Profile power is proportional to tip speed, and Mars needs a high tip speed. And it's proportional to
\\(c_d / C_L\\), which is exactly what the low Reynolds number makes worse. For the design here,

\\[P_0 = 0.75 \times 7.42 \times 159 \times \frac{0.03}{0.6} = 0.75 \times 7.42 \times 159 \times 0.05 \approx 44 \text{ W}\\]

The induced power is never quite the ideal value either. Non-uniform inflow, losses at the blade tips,
swirl in the wake, and interference between two stacked rotors all add to it, and a lumped correction
factor \\(\kappa \approx 1.2\\) is a reasonable estimate. The total hover power is

\\[P = \kappa \frac{W^{3/2}}{\sqrt{2 \rho A}} + \frac{3}{4} W V_{\text{tip}} \frac{c_d}{C_L} = 1.2 \times 109 + 44 \approx 131 + 44 = 175 \text{ W}\\]

or about 88 W per kilogram of vehicle. The ratio of ideal power to actual power is
\\(109 / 175 \approx 0.62\\), so about 62% of the power ends up doing the minimum work that momentum
theory says is necessary, and the rest goes to losses.

If you run the same calculation for the same drone on Earth, with the same blades at the lower Earth
tip speed, that ratio comes out exactly the same, 0.62. The reason is that both terms in the power
scale the same way. Dividing the profile power by the induced power,

\\[\frac{P_0}{P_{\text{ind}}} = \frac{\tfrac{3}{4} W V_{\text{tip}} c_d / C_L}{\kappa W v_h} = \frac{3}{4 \kappa} \cdot \frac{c_d}{C_L} \cdot \frac{V_{\text{tip}}}{v_h}\\]

and the ratio of tip speed to induced velocity, using \\(V_{\text{tip}}^2 = 6 W / (\rho A \sigma C_L)\\)
and \\(v_h^2 = W / (2 \rho A)\\), is

\\[\frac{V_{\text{tip}}}{v_h} = \sqrt{\frac{6 W / (\rho A \sigma C_L)}{W / (2 \rho A)}} = \sqrt{\frac{12}{\sigma C_L}} = \sqrt{\frac{12}{0.172 \times 0.6}} \approx 10.8\\]

which doesn't involve the density or the gravity at all. Plugging in,
\\(P_0 / P_{\text{ind}} = (3 / 4.8) \times 0.05 \times 10.8 \approx 0.34\\), which matches
\\(44 / 131\\). So in this idealized picture, the fraction of power lost is the same on both planets. Mars doubles the power through momentum theory and raises \\(c_d / C_L\\) through the Reynolds number, and the second effect adds real losses. With \\(C_L = 0.8\\) and \\(c_d = 0.03\\), the profile power drops to 33 W and the total to 164 W. An airfoil that loses some lift and picks up drag to a separation bubble, with \\(C_L = 0.5\\) and \\(c_d = 0.04\\), needs 71 W of profile power and 202 W in total, 15% more than the baseline. Every watt has to come from a battery that also has to be
lifted.

To get a sense of what that means for flight time, suppose half a kilogram of the vehicle is battery,
with a specific energy of 150 Wh/kg. That's 75 Wh, and at 175 W the drone could hover for

\\[t = \frac{75 \text{ Wh}}{175 \text{ W}} \approx 0.43 \text{ h} \approx 26 \text{ minutes}\\]

before the battery is empty, ignoring reserves and the effect of cold on the battery. The battery numbers are my assumptions, so this only shows the scale.

Forward flight adds a new constraint, and again it comes from the speed of sound. When the drone moves
forward at speed \\(V\\), a blade on the advancing side sees air at \\(V_{\text{tip}} + V\\) at its tip,
and a blade on the retreating side sees \\(V_{\text{tip}} - V\\). The ratio \\(\mu = V / V_{\text{tip}}\\)
is the advance ratio, and the advancing tip Mach number is

\\[M_{\text{adv}} = \frac{V_{\text{tip}} + V}{a} = M_{\text{tip}} (1 + \mu)\\]

If I set a conservative limit of Mach 0.8 for the advancing tip, with the hover tip Mach number at
0.7, then

\\[1 + \mu \leq \frac{0.8}{0.7} \quad \Longrightarrow \quad \mu \leq 0.143, \qquad V \leq 0.143 \times 159 \approx 22.7 \text{ m/s}\\]

which is about 82 km/h. The Martian rotor needs a high tip Mach number just to hover, so most of the
margin to the speed of sound is used up before the drone moves at all.

The power in forward flight has three parts, which I'll derive one at a time. The induced power changes
because the rotor now works on air coming at it from the front, as well as air it pulls through itself.
Glauert's extension of momentum theory treats the mass flow as \\(\rho A\\) times the total velocity at
the disk, which in level flight with a small rotor tilt is about \\(\sqrt{V^2 + v_i^2}\\). The wake
velocity is still \\(2 v_i\\) by the same argument as before, so

\\[T = 2 \rho A v_i \sqrt{V^2 + v_i^2}\\]

Dividing by the hover relation \\(T = 2 \rho A v_h^2\\) and writing \\(\hat{v} = v_i / v_h\\) and
\\(\hat{V} = V / v_h\\),

\\[\hat{v} \sqrt{\hat{V}^2 + \hat{v}^2} = 1 \quad \Longrightarrow \quad \hat{v}^2 (\hat{V}^2 + \hat{v}^2) = 1 \quad \Longrightarrow \quad \hat{v}^4 + \hat{V}^2 \hat{v}^2 - 1 = 0\\]

This is a quadratic in \\(\hat{v}^2\\), and taking the positive root,

\\[\hat{v}^2 = \frac{-\hat{V}^2 + \sqrt{\hat{V}^4 + 4}}{2}\\]

As a worked example, at \\(V = 16\\) m/s, \\(\hat{V} = 16 / 14.7 \approx 1.086\\), so
\\(\hat{V}^2 \approx 1.180\\) and \\(\hat{V}^4 \approx 1.393\\). Then

\\[\hat{v}^2 = \frac{-1.180 + \sqrt{1.393 + 4}}{2} = \frac{-1.180 + 2.322}{2} \approx 0.571, \qquad \hat{v} \approx 0.756, \qquad v_i \approx 0.756 \times 14.7 \approx 11.1 \text{ m/s}\\]

At high speed, \\(\hat{V}^4\\) dominates the square root, and expanding
\\(\sqrt{\hat{V}^4 + 4} \approx \hat{V}^2 + 2 / \hat{V}^2\\) gives \\(\hat{v}^2 \approx 1 / \hat{V}^2\\), so
\\(v_i \approx v_h^2 / V\\). The induced power falls off as \\(1 / V\\), because the rotor is working on
a bigger stream of air.

The profile power rises a little with speed. A blade element at radius \\(r\\) and azimuth \\(\psi\\)
sees a tangential velocity \\(\Omega r + V \sin \psi\\), and its drag goes as the square of that. The
power has two pieces. One is the torque the motor supplies, which is \\(\Omega r\\) times the drag. The
other is the work done against the backward component of the drag as the vehicle moves forward, which
is \\(V \sin \psi\\) times the drag. Adding them, the power from each element is proportional to
\\((\Omega r + V \sin \psi)^3\\). Writing \\(x = r / R\\) and dividing through by \\(V_{\text{tip}}^3\\),
the ratio to the hover value is

\\[\frac{P_0(\mu)}{P_0(0)} = \frac{\int_0^1 \overline{(x + \mu \sin \psi)^3} \, dx}{\int_0^1 x^3 \, dx}\\]

where the bar means an average over \\(\psi\\) from 0 to \\(2 \pi\\). Expanding the cube,

\\[(x + \mu \sin \psi)^3 = x^3 + 3 x^2 \mu \sin \psi + 3 x \mu^2 \sin^2 \psi + \mu^3 \sin^3 \psi\\]

and using \\(\overline{\sin \psi} = 0\\), \\(\overline{\sin^2 \psi} = 1/2\\), and
\\(\overline{\sin^3 \psi} = 0\\), the average is \\(x^3 + \tfrac{3}{2} \mu^2 x\\). Integrating,

\\[\frac{P_0(\mu)}{P_0(0)} = \frac{1/4 + \tfrac{3}{2} \mu^2 \cdot \tfrac{1}{2}}{1/4} = \frac{1/4 + 3 \mu^2 / 4}{1/4} = 1 + 3 \mu^2\\]

At \\(\mu = 0.143\\), that's \\(1 + 3 \times 0.0204 \approx 1.06\\), a 6% increase. The third part is the
parasite power to push the body through the air, \\(P_p = \tfrac{1}{2} \rho f V^3\\), where \\(f\\) is
the equivalent flat plate area of the fuselage, landing gear, and everything else that isn't the rotor.
For a compact body, \\(f = 0.02\\) m² is reasonable. At 22 m/s,
\\(P_p = 0.5 \times 0.0151 \times 0.02 \times 22^3 \approx 1.6\\) W. Putting the three together,

\\[P(V) = \kappa W v_i(V) + P_0 (1 + 3 \mu^2) + \frac{1}{2} \rho f V^3\\]

| Speed | Induced velocity | Induced power | Profile power | Parasite power | Total |
|---|---|---|---|---|---|
| 0 m/s | 14.7 m/s | 131 W | 44 W | 0 W | 175 W |
| 8 m/s | 13.7 m/s | 122 W | 45 W | 0.1 W | 167 W |
| 16 m/s | 11.1 m/s | 99 W | 46 W | 0.6 W | 145 W |
| 22 m/s | 9.1 m/s | 81 W | 47 W | 1.6 W | 130 W |

The parasite power barely registers, because it carries a factor of \\(\rho\\) and the speeds are low.
On Earth, the curve of power against speed is shaped like a bucket: induced power falls, parasite power
rises, and there's a minimum somewhere in between. On Mars, the bottom of the bucket is out of reach.
To find where it would be, use the high-speed form \\(v_i \approx v_h^2 / V\\) and differentiate:

\\[P(V) \approx \frac{\kappa W v_h^2}{V} + P_0 \left( 1 + \frac{3 V^2}{V_{\text{tip}}^2} \right) + \frac{1}{2} \rho f V^3 \quad \Longrightarrow \quad \frac{dP}{dV} = -\frac{\kappa W v_h^2}{V^2} + \frac{6 P_0 V}{V_{\text{tip}}^2} + \frac{3}{2} \rho f V^2\\]

With \\(\kappa W v_h^2 = 1.2 \times 7.42 \times 14.7^2 \approx 1{,}930\\) W·m/s, \\(P_0 = 44\\) W, and
\\(V_{\text{tip}} = 159\\) m/s, the three terms at \\(V = 40\\) m/s are

\\[\frac{dP}{dV} \approx -\frac{1930}{1600} + \frac{6 \times 44 \times 40}{25{,}280} + 1.5 \times 0.0151 \times 0.02 \times 1600 = -1.21 + 0.42 + 0.73 \approx -0.06\\]

which is close to zero, so the minimum power speed is about 40 m/s, nearly twice what the advancing tip
allows. The drone always wants to fly as fast as compressibility lets it, both for range and for
endurance, and cruising at the limit uses about \\(130 / 175 \approx 74\%\\) of the hover power. With the
same 75 Wh battery, that's about 35 minutes of flight and a range of roughly
\\(0.58 \text{ h} \times 82 \text{ km/h} \approx 47\\) km. Hovering takes more power than flying at any speed up to the limit.

The blades' motion causes a more subtle problem. Helicopter blades flap: they rise and fall slightly as
they go around, which is how a rotor evens out the lift between the advancing and retreating sides.
Model a blade as hinged at the root, with flap angle \\(\beta\\) and moment of inertia \\(I_b\\) about the
hinge. For a blade of mass \\(m_b\\) spread evenly along its length,

\\[I_b = \int_0^R \frac{m_b}{R} r^2 \, dr = \frac{m_b R^2}{3}\\]

When the blade is tilted up by \\(\beta\\), centrifugal force pulls it back toward the plane of rotation.
A mass element \\(dm\\) at radius \\(r\\) feels an outward force \\(dm \, \Omega^2 r\\), which has a lever
arm of \\(r \sin \beta \approx r \beta\\) about the hinge, so the restoring moment is
\\(\Omega^2 \beta \int r^2 \, dm = I_b \Omega^2 \beta\\). That acts like a spring.

The aerodynamics adds damping. When the blade flaps upward at rate \\(\dot{\beta}\\), a section at radius
\\(r\\) moves up at \\(r \dot{\beta}\\), which reduces its angle of attack by
\\(r \dot{\beta} / (\Omega r) = \dot{\beta} / \Omega\\). With lift slope \\(a_0\\), the change in lift on
that section is

\\[dL = \frac{1}{2} \rho (\Omega r)^2 c \, a_0 \left( -\frac{\dot{\beta}}{\Omega} \right) dr = -\frac{1}{2} \rho \Omega c \, a_0 \dot{\beta} \, r^2 \, dr\\]

and the moment about the hinge is

\\[M = \int_0^R r \, dL = -\frac{1}{2} \rho \Omega c \, a_0 \dot{\beta} \int_0^R r^3 \, dr = -\frac{\rho c \, a_0 \Omega R^4}{8} \dot{\beta}\\]

So the equation for the flap angle is

\\[I_b \ddot{\beta} + \frac{\rho c \, a_0 \Omega R^4}{8} \dot{\beta} + I_b \Omega^2 \beta = M_{\text{forcing}}\\]

Changing to the azimuth \\(\psi = \Omega t\\) as the time variable, so that \\(\dot{\beta} = \Omega \beta'\\)
and \\(\ddot{\beta} = \Omega^2 \beta''\\), and dividing by \\(I_b \Omega^2\\),

\\[\beta'' + \frac{\gamma}{8} \beta' + \beta = \frac{M_{\text{forcing}}}{I_b \Omega^2}, \qquad \gamma = \frac{\rho \, a_0 \, c \, R^4}{I_b}\\]

The dimensionless group \\(\gamma\\) is the Lock number, the ratio of aerodynamic to inertial forces on
the blade. Comparing with the standard damped oscillator \\(\beta'' + 2 \zeta \beta' + \beta = 0\\), the
damping ratio is \\(\zeta = \gamma / 16\\). For the design here, suppose each blade has a mass of 50 g,
so \\(I_b = 0.05 \times 0.36 / 3 = 0.006\\) kg m². Then

\\[\gamma = \frac{0.0151 \times 5.7 \times 0.081 \times 0.6^4}{0.006} = \frac{0.0151 \times 5.7 \times 0.081 \times 0.1296}{0.006} \approx 0.15, \qquad \zeta = \frac{0.15}{16} \approx 0.0094\\]

The unforced solution is \\(\beta \propto e^{-\zeta \psi} \cos(\sqrt{1 - \zeta^2} \, \psi)\\), so a disturbance
decays by a factor of \\(e\\) after the azimuth has advanced by \\(1 / \zeta\\) radians, which is
\\(1 / (2 \pi \zeta)\\) revolutions. On Mars that's \\(1 / (2 \pi \times 0.0094) \approx 17\\) revolutions.
On Earth, the same blade has \\(\gamma \approx 12\\) and \\(\zeta \approx 0.76\\), and a disturbance dies out in
about a fifth of a revolution. On Mars, any disturbance from a gust or a control input keeps the blades
flapping for many revolutions. The density appears directly in the Lock number, so sizing can't fix
this. The practical answer is to make the blades very stiff and rely on their structure to control their motion, since the air can't, which in turn makes the blades heavier.

Close to the ground, the rotor gets some extra thrust, and the method of images gives a way to estimate
how much. Seen from a distance, the rotor's wake looks like a point source blowing air out at the volume
rate \\(Q = A v_i\\). The ground at distance \\(z\\) below the rotor can't have flow through it, and that
condition is satisfied by placing an identical image source at distance \\(z\\) below the ground. The
velocity at distance \\(d\\) from a point source comes from spreading the volume flow over a sphere,
\\(Q / (4 \pi d^2)\\). The image is \\(2 z\\) away from the real rotor, so at the rotor it pushes air
upward at

\\[\delta v = \frac{A v_i}{4 \pi (2 z)^2} = \frac{\pi R^2 v_i}{16 \pi z^2} = v_i \left( \frac{R}{4 z} \right)^2\\]

which reduces the effective induced velocity to \\(v_i (1 - (R / 4 z)^2)\\). At fixed power \\(P = T v_i\\),
a lower induced velocity means more thrust, and

\\[\frac{T_{\text{ground}}}{T_{\text{free}}} = \frac{1}{1 - (R / 4 z)^2}\\]

At one rotor radius above the ground, \\(R / 4 z = 0.25\\) and the gain is
\\(1 / (1 - 0.0625) \approx 1.07\\), or 7%. At half a radius, \\(R / 4 z = 0.5\\) and the gain is
\\(1 / 0.75 \approx 1.33\\). At two radii it's under 2%. The estimate is only reliable above about half a
radius, where the point source picture is reasonable, but it shows why takeoff and landing are the
easiest parts of a flight.

Near the ground, the wake also hits dust, and Mars has a lot of fine dust. Momentum theory gives a clean
way to compare with Earth. The far wake moves at \\(w = 2 v_i\\), so its dynamic pressure is

\\[q_{\text{wake}} = \frac{1}{2} \rho w^2 = \frac{1}{2} \rho (2 v_i)^2 = 2 \rho v_i^2 = \frac{T}{A}\\]

which is just the thrust per unit disk area, the disk loading, and the density has dropped out. For our
drone on Mars, \\(T / A = 7.42 / 1.131 \approx 6.6\\) Pa, even though the wake itself moves at about
\\(2 \times 14.7 \approx 29\\) m/s. The same drone on Earth would have \\(19.6 / 1.131 \approx 17.3\\) Pa. The
ratio is \\(6.6 / 17.3 = 0.38\\), the ratio of the gravities. Now, the stress needed to start moving a
grain of sand or dust, in the classic picture where the shear stress from the flow has to overcome the
grain's weight, scales as

\\[\tau_{\text{threshold}} \sim \rho_p \, g \, d\\]

where \\(\rho_p\\) is the grain density and \\(d\\) is its diameter. That also scales with gravity, so the
ratio of wake stress to threshold stress is the same on both planets for the same vehicle. To first
order, a Mars drone stirs up dust about as easily as the same drone would over the same soil on Earth.
This leaves out a lot, especially the cohesion between very fine
dust particles, which doesn't scale with gravity and makes small particles harder to lift than this
picture suggests.

Last, consider altitude. From the scale height, the density at altitude \\(h\\) is \\(\rho_0 e^{-h / H_s}\\). The
induced power scales as \\(\rho^{-1/2}\\), so it grows as \\(e^{h / (2 H_s)}\\), and at fixed tip speed the
solidity needed to carry the load scales as \\(1 / \rho\\), so it grows as \\(e^{h / H_s}\\). At 4 km, for
example, \\(e^{-4 / 10.7} \approx 0.69\\), so the density is \\(0.0151 \times 0.69 \approx 0.0104\\) kg/m³,
the induced power goes up by \\(e^{4 / 21.4} \approx 1.21\\), and the solidity goes up by
\\(e^{4 / 10.7} \approx 1.45\\). So at 4 km the induced power rises from 131 W to 158 W and the solidity from 0.17 to 0.25. At 1 km and 2 km, the induced power is 137 W and 144 W and the solidity 0.19 and 0.21.

The power penalty is modest, but the solidity grows quickly, and the blades can only get so wide. Since
the tip speed is already at its limit, the only other option in thinner air is to raise \\(C_L\\), and
eventually the blades stall. So a Mars drone hits a ceiling sooner than the power numbers alone would
suggest. Mars's surface spans tens of kilometers in elevation, so the drone has to be designed for the
air where it will fly.

There's a lot I've left out. I used a single atmospheric state, but the pressure on Mars changes by tens
of percent over a year as CO₂ freezes onto the polar caps and comes back, and the temperature changes by
tens of degrees between day and night, so the density varies a lot. I treated two coaxial rotors as one
disk with a correction factor, which hides the real interference between them. The lift and drag coefficients are representative values that I chose, and at Reynolds numbers this low the real values depend strongly on the exact shape. I haven't touched the thermal problem, which is a separate and difficult problem, since batteries and electronics have to survive nights that drop below 200 K.
And I've ignored gusts, which have a bigger effect than usual on a vehicle whose blades barely damp their own
motion.

The two things I'd like to work through next are what the best blade section looks like at a Reynolds number of ten thousand, and the control problem that comes from lightly damped flapping.
