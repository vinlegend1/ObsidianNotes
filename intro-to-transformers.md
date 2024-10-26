+++
title = 'Intro to Transformers and Magnetic Circuits'
date = 2024-03-05T10:40:26+08:00
draft = false
+++

A transformer is a magnetically coupled circuit. A circuit in which the magnetic field produced by time-varying current in one circuit induces voltage in another

- primary winding
- secondary winding
- **no** electrical connection
- uses mutual induction to change the values of alternating currents and voltages

Two categories of transformers
- iron-core
    - core type
    - shell type
    - a special type of steel called transformer steel can be used
- air-core

has a very long life

power supplies have
- mini-transformers? or at least a similar technology
- with step-up step-down voltages

- double line -> iron
- no line -> air
- dashed-line -> Ferrite-core

## Ideal Model
- Iron-core transformers appear difficult to analyze.
    - winding resistance, core loss, leakage flux
    - how do we deal with them?
- we use an ideal model; minimal losses
- no load current $I_0$

$$E = -N d \phi / dt V$$

in an ideal transformer, the rate of change of flux is the same for both primary and secondary (voltage ratio)

$$\frac{V_1}{N_1} = \frac{V_2}{N_2}$$

$$\frac{e_p}{e_s} = \frac{N_p}{N_s}$$

If N2 is less than N1, then V2 is less than V1 (bottom less than up), it's a step-down transformer

it's a step-up transformer if it's the opposite

if the ratio or `a` is equal to one, then it's called unity

input power = output power (in an ideal case)
input power > output power (the usual case)
$$V_1 I_1 = V_2 I_2$$
- voltage, turn, current ratio

**Transformers are rated in terms of voltage and apparent power**
- that explains the Monumento station apparent power rating
- can deliver the rated power without heating up

$$I_0 = sqrt(I_C^2 + I_M^2)$$

total core losses 
$$V_1 I_0 cos \phi_0$$

## Conceptual Ideas
- mutual inductance is where one coil with changing magnetic field induces a voltage or e.m.f on the other coil or circuit
- a **ferromagnetic core** is used to maximize the use of magnetic fields
    - it directs the flow of magnetic field lines
    - a problem that arises here is it produces eddy currents
    - eddy currents can be minimized by using many thin laminated steel sheet (or have a laminated core)
- iron core transformers are used for low-frequency applications

![Iron Core Transformer](/electrical-engineering/001-iron-core-transformer.png)

- for high-frequency applications such as **radio circuits**, air-core and ferrite-core types are used

### Transformer Ratings
- Transformer Ratings are in terms of complex power (kVA)
- In ideal transformer, there are no core losses including:
    - No Eddy Current Loss
    - No Hysteresis Loss
- No-load thingo just means
    - considering a transformer with no load, and having no winding resistance and no leakage resistance
    - in a sense, we're doing this so that we know how much of current loss was there due to iron core loss (and not copper loss)
    - so I_c is just the core current loss while I_M is the magnetizing current that magnetizes the secondary circuit
- emf is the voltage developed between two terminals of a battery while voltage is more general (to loads)

### E.M.F. Induced by transformer

considering the magnetic flux as a sinuisoid or alternating quantity

$$emf = - N \frac{d \phi}{dt}$$
$$emf = N \phi_m \omega cos(\omega t)$$
$$emf = N \phi_m 2 \pi f cos(\omega t)$$

and to get the root mean square... you end up with
