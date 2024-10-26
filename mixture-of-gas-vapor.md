+++
title = 'Mixture of Gas and Vapor'
date = 2024-02-29T14:26:30+08:00
draft = false
tags = ['engineering', 'thermodynamics']
+++

* Gas 
    - gaseous form at room conditions
    - we refer to gases as the "dry" part of the mixture

* vapor 
    - gaseous form of a liquid at room conditions
    - refer vapor as the moisture content of the mixture

## Measure of moisture content

moisture content measures the composition of the mixture

### absolute humidity (or humidity ratio or specific humidity)

$$w = \frac{m_v}{m_d}$$

Using Dalton's model and the ideal gas equation, we can derive:

$$w = \frac{M_v}{M_d} \frac{P_v}{P_{mix} - P_v}$$

units are kg vapor / kg dry gas

air-water mixture:

$$w = \frac{0.622 P_v}{P_{mix} - P_v}$$

*note* for atmospheric air, P_mix = atmoshperic pressure

can be considered in a component basis
PV = mRT of the mixture is just
PV = mRT of vapor + PV = mRT of dry gas

### relative humidity

this is just a ratio between partial pressure of vapor, and the saturation vapor pressure @ temperature of mixture

so it's a bit like quality x

$$\phi = \frac{P_v}{P_{sat}}$$
$$0 \leq \phi \leq 1$$

### degree of saturation

$$^{\circ}sn = \frac{\omega'}{\omega_{sat}}$$

### other ratios

* mass fraction of vapor
* mole fraction of vapor

### Properties of Mixtures of Gases and Vapor

this "study" or analysis is called psychometry

$$P_v + P_{dg} = P_{mix}$$

1. dry bulb temperature
- **thermodynamic temperature**

2. wet bulb temperature

dbt - wbt = wet bulb depression

If dbt = wbt; "no evaporated cooling" "saturated air"

thermometers read dry bulb temperature.
when there's evaporative cooling, thermometers read wet buble temperature

dbt > wbt unsaturated air

3. dew point temperature
- the temperature where droplets of water will form
- highest temperature which vapor condenses to liquid droplets as the gas-vapor mixture is cooled at **constant pressure**
- condensation happens when temperature goes below the due point
- if dbt =  dpt -> saturated air
- dbt > dpt -> unsaturated air

dbt > wbt > dpt unsaturated air
dbt = wbt = dpt saturated air

### Relationship between vapor pressure and temperature

Saturation pressure of the vapor
$$@dbt <-> P_{sat}$$

Saturation pressure at wet bulb temperature
$$wbt <-> P_{vw}$$

Saturation pressure at wet bulb temperature
$$dpt <-> P_v$$

### Enthalpy and Internal Energy

$$H_{mix} = H_{dg} + H_v$$
$$U_{mix} = U_{dg} + U_v$$
$$S_{mix} = S_{dg} + S_v$$

which one should be our reference mass?
- mass of vapor is variable that depends on dew point and other temps
- so we use dry gas mass as reference

$$u = u_{dg} + \omega u_v [kJ/kgda]$$
$$h = h_{dg} + \omega h_v [kJ/kgda]$$
$$s = s_{dg} + \omega s_v [kJ/kgda K]$$

$$h = 1.01 dbt + \omega (2501.3 + 1.86 tbd)$$
