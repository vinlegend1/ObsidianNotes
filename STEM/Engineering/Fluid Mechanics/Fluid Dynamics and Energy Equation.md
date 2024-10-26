---
title: Fluid Dynamics
date: 2024-02-29T09:26:30+08:00
tags:
  - engineering
  - fluid_mechanics
---

## Bernoulli Equation and Fluid Moving on a Streamline

Don't bother with the derivation:
$$\frac{p}{\gamma} + \frac{V^2}{2g} + z = \text{constant} = \text{total head} = H$$

total head is sorta the same thing as total energy

$$\frac{p}{\gamma} + z = \text{static head} = \text{piezometric head}$$

velocity head is just the velocity part

### Head Loss

The head loss is given by:

$$h_L = \frac{2 \bar{\tau}_0 L}{\gamma r}$$

$$h_L = f \frac{L}{D} \frac{V^2}{2g}$$

$$h_L = K \frac{V^2}{2g}$$

K = loss coefficient

f = friction factor

L = pipe length; D = pipe diameter

note that you can think of head loss as the total energy difference if it were an "ideal" fluid vs a "real" fluid
so if it were ideal, then the headloss for it will be 0 and you just use your Bernoulli

### Units of Head

Note: kN/m^2 / kN/m^3 => m (oh yeah makes sense because of z)

### Water Power Formula

$$P = \text{energy rate} = \gamma Q h_p$$

### Conservation of Energy (Head)

sum of energy in = sum of energy out
sum of head in = sum of head out

$$H_A + h_p = H_B + h_L$$

### Continuity Equation (Specific case of Mass Conservation)

$$Q_1 = Q_2$$
$$A_1 v_1 = A_2 v_2$$

## Force or Rotational Vortex

A fluid may be made to rotate as a solid body if there is an application of external torque. A common example is the rotation of liquid within a centrifugal pump or of gas in a centrifugal compressor.

![centrifugal pump](/fluid-dynamics/01-centrifugal-pump.jpg)
![centrifugal pump](/fluid-dynamics/02-centrifugal-pump.jpg)
![centrifugal compressor](/fluid-dynamics/03-centrifugal-compressor.png)

Closed Vessels:

$$\frac{\Delta P}{\gamma} = \frac{\omega^2}{2g} (r_2^2 - r_1^2)$$

Open Vessels:

$$\Delta z = \frac{\omega^2}{2g} (r_2^2 - r_1^2)$$

if z_1 or p_1 were assumed to be 0 @ r_1 = 0, then the equations above simplify. You can combine the two equations to get a more general equation

## Free or Irrotational Vortex

In the free vortex there is no expenditure of energy whatever from an outside source,
and the fluid rotates by virtue of some rotation previously imparted to it.
Some examples are the following:

1. A *whirlpool* in a river,
2. The rotary flow that often arises in a shallow vessel when liquid flows out through
a hole in the bottom (i.e., water empties from a bathtub).
3. Flow in a turbine case as the water approaches the guide vanes

![Free vs Forced Vortex](/fluid-dynamics/04-free-force-vortex.png)
