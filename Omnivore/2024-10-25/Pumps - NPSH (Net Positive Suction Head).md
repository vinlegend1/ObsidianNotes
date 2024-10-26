---
id: b0c7e632-7f5c-45b1-88af-e34f8fe48e9b
---

# Pumps - NPSH (Net Positive Suction Head)

## Links
[Read on Omnivore](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50)
[Read Original](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d_634.html)

 #fluid_mechanics

## Highlights

> Low pressure at the suction side of a pump may cause the fluid to start boiling with
> 
> * reduced efficiency
> * cavitation
> * damage [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#52d8f3d2-fe9b-4385-a3a6-cc034fb8134b)  ^52d8f3d2

> Boiling starts when the pressure in the liquid is reduced to the vapor pressure of the fluid at the actual temperature.
> 
> ![Pumps - cavitation and bubble formation](https://proxy-prod.omnivore-image-cache.app/504x427,spy5geCuNQWX3hqIBJoIPeleu82SLYXLGiVOqJHiQ3j8/https://www.engineeringtoolbox.com/docs/documents/634/pump_cavitation.png "Pumps - cavitation and bubble formation") [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#d6dafd1e-f7d3-4e6b-a9d5-7ca59ac8f2da)  ^d6dafd1e

> Based on the [Energy Equation](https://www.engineeringtoolbox.com/mechanical-energy-equation-d%5F614.html "The energy equation") \- the **suction head** in the fluid close to the impeller\*) can be expressed as the sum of the **static** and **velocity head** [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#c0c37c28-5149-4cff-aefb-ec0c131908b4)  ^c0c37c28

> We can not measure the suction head "close to the impeller". In practice we can measure the head at the pump suction flange. Be aware that - depending of the design of the pump - the contribution to the NPSH value from the suction flange to the impeller can be substantial. [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#efb7b887-6356-414e-8f53-b963b4101e15)  ^efb7b887

what does this mean?

> The NPSHr, called as the Net Suction Head as required by the pump in order to prevent cavitation for safe and reliable operation of the pump.
> 
> The required NPSHr for a particular pump is in general determined experimentally by the **pump manufacturer** and a part of the documentation of the pump. [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#9dba0f7f-375d-4e87-9f89-772e5488ecec)  ^9dba0f7f

> The available NPSHa  of the system should always exceeded the required NPSHr of the pump to avoid vaporization and cavitation of the impellers eye. The available NPSHa should in general be significant higher than the required NPSHr to avoid that head loss in the suction pipe and in the pump casing, local velocity accelerations and pressure decreases, start boiling the fluid on the impellers surface. [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#0f3c9195-bc9b-4782-b849-52dc92aad207)  ^0f3c9195

> Note that required NPSHr increases with the square of capacity. [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#08b13521-db6f-4007-8e2b-cdf3a2e9b1b5)  ^08b13521

> Note that the NPSH specifications provided by manufacturers in general are for use with **cold water**. [⤴️](https://omnivore.app/me/pumps-npsh-net-positive-suction-head-192c28c1d50#13c2f7f6-3803-40f0-8854-2c799a577b63)  ^13c2f7f6


## Content
##  An introduction to pumps and the Net Positive Suction Head (NPSH).

==Low pressure at the suction side of a pump may cause the fluid to start boiling with==

* ==reduced efficiency==
* ==cavitation==
* ==damage==

of the pump as a result. ==Boiling starts when the pressure in the liquid is reduced to the vapor pressure of the fluid at the actual temperature.==

![Pumps - cavitation and bubble formation](https://proxy-prod.omnivore-image-cache.app/504x427,spy5geCuNQWX3hqIBJoIPeleu82SLYXLGiVOqJHiQ3j8/https://www.engineeringtoolbox.com/docs/documents/634/pump_cavitation.png "Pumps - cavitation and bubble formation")

To characterize the potential for boiling and cavitation the difference between

* the total head on the suction side of the pump - close to the impeller, and
* the liquid vapor pressure at the actual temperature

can be used.

### Suction Head

==Based on the== ==[Energy Equation](https://www.engineeringtoolbox.com/mechanical-energy-equation-d%5F614.html "The energy equation")== ==- the== **==suction head==** ==in the fluid close to the impeller====*)== ==can be expressed as the sum of the== **==static==** ==and== **==velocity head==**:

> _hs \= ps / γliquid \+ vs2 / 2 g (1)_
> 
> _where_
> 
> _hs \= suction [head](https://www.engineeringtoolbox.com/static-pressure-head-d%5F610.html "Head vs. pressure") close to the impeller (m, in)_
> 
> _ps \= static pressure in the fluid close to the impeller (Pa (N/m2), psi (lb/in2))_
> 
> _γliquid \= [specific weight](https://www.engineeringtoolbox.com/density-specific-weight-gravity-d%5F290.html "Specific weight") of the liquid (N/m3, lb/ft3)_
> 
> _vs \= velocity of fluid (m/s, in/s)_
> 
> _g = [acceleration of gravity](https://www.engineeringtoolbox.com/accelaration-gravity-d%5F340.html "Acceleration of gravity") (9.81 m/s2, 386.1 in/s2)_ 

\*) ==We can not measure the suction head "close to the impeller". In practice we can measure the head at the pump suction flange. Be aware that - depending of the design of the pump - the contribution to the NPSH value from the suction flange to the impeller can be substantial.==

* [Velocity - Dynamic Pressure vs. Head](https://www.engineeringtoolbox.com/velocity-head-d%5F916.html "Velocity - Dynamic Pressure vs. Head")

### Liquids Vapor Head

The **liquids vapor head** at the actual temperature can be expressed as:

> _hv \= pv / γvapor (2)_
> 
> _where_
> 
> _hv \= vapor head (m, in)_
> 
> _pv \= vapor pressure (m, in)_
> 
> _γvapor \= [specific weight](https://www.engineeringtoolbox.com/density-specific-weight-gravity-d%5F290.html "Specific weight") of the vapor (N/m3, lb/ft3)_

**Note!** The vapor pressure in a fluid depends on the temperature. [Water](https://www.engineeringtoolbox.com/water-thermal-properties-d%5F162.html "water thermal properties"), our most common fluid, starts boiling at _20 oC_ if the absolute pressure is _2.3 kN/m2_. For an absolute pressure of _47.5 kN/m2_ the water starts boiling at _80 oC_. At an absolute pressure of _101.3 kN/m2_ (normal atmosphere) the boiling starts at _100 oC_. 

The Net Positive Suction Head - NPSH - can be defined as

* the difference between the Suction Head, and
* the Liquids Vapor Head

and can be expressed as

> _NPSH = hs \- hv (3)_
> 
> _or, by combining (1) and (2)_
> 
> _NPSH = ps / γ + vs2 / 2 g - pv / γ (3b)_
> 
> _where_ 
> 
> _NPSH = Net Positive Suction Head (m, in)_

### Available NPSH - NPSHa or NPSHA  

The Net Positive Suction Head available from the application to the suction side of a pump is often named NPSHa. The NPSHa can be estimated during the design and the construction of the system, or determined experimentally by testing the actual physical system.

![Pump - NPSH - Net Positive Suction Head](https://proxy-prod.omnivore-image-cache.app/484x252,s08FJhb1VWYwr_TORC2swER0Axv0xlUwfagORvnJtJ1E/https://www.engineeringtoolbox.com/docs/documents/634/pump_npsh.png)

The available NPSHa can be estimated with the [Energy Equation](https://www.engineeringtoolbox.com/mechanical-energy-equation-d%5F614.html "Energy equation").

For a common application - where the pump lifts a fluid from an open tank at one level to an other, the energy or head at the surface of the tank is the same as the energy or head before the pump impeller and can be expressed as:

> _h0 \= hs \+ hl (4)_
> 
> _where_
> 
> _h0 \= [head](https://www.engineeringtoolbox.com/static-pressure-head-d%5F610.html "Head vs. pressure") at surface (m, in)_
> 
> _hs \= head before the impeller (m, in)_
> 
> _hl \= head loss from the surface to impeller - [major and minor loss](https://www.engineeringtoolbox.com/total-pressure-loss-ducts-pipes-d%5F625.html "Major and minor pressure loss") in the suction pipe (m, in)_

In an open tank the head at the surface can be expressed as:

> _h0 \= p0 / γ =patm / γ (4b)_

For a closed pressurized tank the absolute static pressure inside the tank must be used.

The head before the impeller can be expressed as:

> _hs \= ps / γ + vs2 / 2 g + he (4c)_
> 
> _where_

> _he \= elevation from surface to pump - positive if pump is above the tank, negative if the pump is below the tank (m, in)_

Transforming (4) with (4b) and (4c):

> _patm / γ = ps / γ + vs2 / 2 g + he\+ hl (4d)_

The head available before the impeller can be expressed as:

> _ps / γ + vs2 / 2 g = patm / γ - he\- hl (4e)_

or as the available NPSHa:

> _NPSHa \= patm / γ - he \- hl \- pv / γ (4f)_
> 
> _where_ 
> 
> _NPSHa \= Available Net Positive Suction Head (m, in)_

#### Available NPSHa \- the Pump is above the Tank

If the pump is positioned above the tank, the elevation _\- he \-_ is positive and the [NPSHa](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "NPSH - Net Positive Suction Head") decreases when the elevation of the pump increases (lifting the pump).

At some level the NPSHa will be reduced to zero and the fluid will start to evaporate.

#### Available NPSHa \- the Pump is below the Tank

If the pump is positioned below the tank, the elevation _\- he \-_ is negative and the NPSHa increases when the elevation of the pump decreases (lowering the pump).

It's always possible to increase the NPSHa by lowering the pump (as long as the [major and minor head loss](https://www.engineeringtoolbox.com/total-pressure-loss-ducts-pipes-d%5F625.html "Pressure loss in ducts") due to a longer pipe don't increase it more). **Note!** It is important - and common - to lower a pump when pumping a fluid close to evaporation temperature.

### Required NPSH - NPSHr or NPSHR  

==The NPSH====r====, called as the Net Suction Head as required by the pump in order to prevent cavitation for safe and reliable operation of the pump.==

==The required NPSH====r== ==for a particular pump is in general determined experimentally by the== **==pump manufacturer==** ==and a part of the documentation of the pump.==

![Pump efficiency - BEP - Best Efficiency Point](https://proxy-prod.omnivore-image-cache.app/410x214,skpmNM8sTn5D8TPmlRYQq7Xw_Hb1s8P3sg_Kd_ks6MDk/https://www.engineeringtoolbox.com/docs/documents/635/pump_system_curve.png "Pump efficiency - BEP - Best Efficiency Point")

==The available NPSH====a==  ==of the system should always exceeded the required NPSH====r== ==of the pump to avoid vaporization and cavitation of the impellers eye. The available NPSH====a== ==should in general be significant higher than the required NPSH====r== ==to avoid that head loss in the suction pipe and in the pump casing, local velocity accelerations and pressure decreases, start boiling the fluid on the impellers surface.==

==Note that required NPSH====r== ==increases with the square of capacity.==

Pumps with double-suction impellers has lower NPSHr than pumps with single-suction impellers. A pump with a double-suction impeller is considered hydraulically balanced but is susceptible to an uneven flow on both sides with improper pipe-work.

### Example - Pumping Water from an Open Tank

When elevating a pump located above a tank (lifting the pump) - the fluid starts to evaporate at the suction side of the pump at what is the maximum elevation for the actual temperature of the pumping fluid.

At the maximum elevation _NPSHa_ is zero. The maximum elevation can therefore be expressed by modifying (4f) to:

> _NPSHa \= patm / γ - he \- hl \- pv / γ_ 
> 
> _\= 0_

For an optimal theoretical condition we neglect major and minor head loss. The elevation head can then be expressed as:

> _he \= patm / γ - pv / γ (5)_

The maximum elevation - or suction head - for an open tank depends on the [atmospheric pressure](https://www.engineeringtoolbox.com/standard-atmosphere-d%5F604.html "STP - Standard atmosphere") \- which in general can be regarded as constant, and the vapor pressure of the fluid - which in general vary with temperature, especially for [water](https://www.engineeringtoolbox.com/water-thermal-properties-d%5F162.html "Water - thermal properties").

The absolute vapor pressure of [water at temperature](https://www.engineeringtoolbox.com/water-thermal-properties-d%5F162.html "Water - thermal properties") _20 oC_ is _2.3 kN/m2_. The maximum theoretical elevation of a pump when pumping water at _20 oC_ is therefore:

> _he \= (101.33 kN/m2) / (9.80 kN/m3) - (2.3 kN/m2) / (9.80 kN/m3)_
> 
> _\= 10.1 m_

Due to head loss in the suction pipe and the local conditions inside the pump - the theoretical maximum elevation normally is significantly decreased.

Maximum theoretical elevation of a pump above an open tank at different water temperatures are indicated below.

### Suction Head and Reduction in Suction Lift for Water and Temperature

Suction head for water - or max. elevation of a pump above a water surface - is affected by the temperature of the water - as indicated below:

__Suction Head and Reduction in Suction Lift for Water and Temperature__
| Temperature | abs Vapor Pressure | Reduction in Suction Lift | [Suction Head](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "NPSH - Net Positive Suction Head") |
| ----------- | ------------------ | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| _(oC)_      | _(kN/m2, kPa)_     | _(m)_                     | _(m)_                                                                                                                             |
| 0           | 0.6                | 0                         | 10.3                                                                                                                              |
| 5           | 0.9                | 0                         | 10.2                                                                                                                              |
| 10          | 1.2                | 0                         | 10.2                                                                                                                              |
| 15          | 1.7                | 0                         | 10.2                                                                                                                              |
| 20          | 2.3                | 0.1                       | 10.1                                                                                                                              |
| 25          | 3.2                | 0.2                       | 10.0                                                                                                                              |
| 30          | 4.3                | 0.3                       | 9.9                                                                                                                               |
| 35          | 5.6                | 0.4                       | 9.8                                                                                                                               |
| 40          | 7.7                | 0.7                       | 9.5                                                                                                                               |
| 45          | 9.6                | 0.8                       | 9.4                                                                                                                               |
| 50          | 12.5               | 1.1                       | 9.1                                                                                                                               |
| 55          | 15.7               | 1.5                       | 8.7                                                                                                                               |
| 60          | 20                 | 1.9                       | 8.3                                                                                                                               |
| 65          | 25                 | 2.3                       | 7.8                                                                                                                               |
| 70          | 32.1               | 3.1                       | 7.1                                                                                                                               |
| 75          | 38.6               | 3.8                       | 6.4                                                                                                                               |
| 80          | 47.5               | 4.7                       | 5.5                                                                                                                               |
| 85          | 57.8               | 5.8                       | 4.4                                                                                                                               |
| 90          | 70                 | 7.0                       | 3.2                                                                                                                               |
| 95          | 84.5               | 8.5                       | 1.7                                                                                                                               |
| 100         | 101.33             | 10.2                      | 0                                                                                                                                 |

**Note!** \- as indicated in the table above hot water can be difficult to pump. To limit cavitation in a hot water system 

* locate the pump in lowest possible position
* if possible - increase the static pressure in system
* oversize and simplify the piping on the suction side of the pump to limit friction and dynamic losses

### Reduction in Suction Lift for Water vs. Altitude

__Reduction in Suction Lift for Water vs. Altitude__
| Altitude_(m)_ | Reduction in Suction Lift _(m)_ |
| ------------- | ------------------------------- |
| 0             | 0                               |
| 250           | 0.30                            |
| 500           | 0.60                            |
| 750           | 0.89                            |
| 1000          | 1.16                            |
| 1250          | 1.44                            |
| 1500          | 1.71                            |
| 1750          | 1.97                            |
| 2000          | 2.22                            |
| 2250          | 2.47                            |
| 2500          | 2.71                            |

### Pumping Hydrocarbons

==Note that the NPSH specifications provided by manufacturers in general are for use with== **==cold water==**==.== For hydrocarbons these values must be lowered to account for vapor release properties of complex organic liquids.

__Suction Heads Pumping Hydrocarbons__
| Fluid          | Temperature_(oC)_ | abs Vapor Pressure _(kPa)_ |
| -------------- | ----------------- | -------------------------- |
| Ethanol        | 20                | 5.9                        |
| 65             | 58.2              |                            |
| Methyl Acetate | 20                | 22.8                       |
| 55             | 93.9              |                            |

The head developed by a pump is independent of liquid and a performance curve for water can be used for Newtonian liquids like gasoline, diesel or similar. Note that the [required power to the pump](https://www.engineeringtoolbox.com/pumps-power-d%5F505.html "Pump power") depends on the liquid density and must be recalculated.

### NPSH and Liquids with Dissolved Gas

NPSH calculations might be modified if there is significant amount of dissolved gas in a liquid. The gas saturation pressure is often much higher than a liquid's vapor pressure.

* [Solubility of Gases in Water](https://www.engineeringtoolbox.com/gases-solubility-water-d%5F1148.html "Gases - solubility in water")

##  Related Topics

* ### [ Pumps](https://www.engineeringtoolbox.com/pumps-t%5F34.html "pumps centrifugal water pressure air chemical well vacuum liquid oil hydraulic sewage submersible bilge fuel ")  
 Design of pumping systems and pipelines. With centrifugal pumps, displacement pumps, cavitation, fluid viscosity, head and pressure, power consumption and more.

##  Related Documents

* ### [ Centrifugal Pumps](https://www.engineeringtoolbox.com/centrifugal-pumps-d%5F54.html "centrifugal pumps tutorial head pressure capacity")  
 An introduction to Centrifugal Pumps.
* ### [ Condensate Pumping](https://www.engineeringtoolbox.com/condensate-pumping-d%5F280.html "steam condensate pumps pumping")  
 High temperatures and danger of impeller cavitation is the major challenge for condensate pumping in steam systems.
* ### [ Mechanical Energy Equation vs. Bernoulli Equation](https://www.engineeringtoolbox.com/mechanical-energy-equation-d%5F614.html "mechanical energy equations bernoulli")  
 The Mechanical Energy Equation compared to the Extended Bernoulli Equation.
* ### [ Positive Displacement Pumps](https://www.engineeringtoolbox.com/positive-displacement-pumps-d%5F414.html "positive displacement pumps rotary lobe gear peristaltic vane diaphragm")  
 Introduction tutorial to positive displacement pumps basic operating principles.
* ### [ Pressure to Head Unit Converter](https://www.engineeringtoolbox.com/pressure-head-converter-d%5F406.html "pressure head converter")  
 Pressure vs. head units - like _lb/in2, atm, inches mercury, bars, Pa_ and more.
* ### [ Pumping Water - Required Horsepower](https://www.engineeringtoolbox.com/pumping-water-horsepower-d%5F753.html "pump horsepower water")  
 Horsepower required to pump water.
* ### [ Pumps - Affinity Laws](https://www.engineeringtoolbox.com/affinity-laws-d%5F408.html "affinity laws pump fan volume capacity flow pressure power")  
 Turbo machines affinity laws can be used to calculate volume capacity, head or power consumption in centrifugal pumps when changing speed or wheel diameters.
* ### [ Pumps - Classifications](https://www.engineeringtoolbox.com/classification-pumps-d%5F55.html "pumps types centrifugal positive displacement")  
 Centrifugal pumps vs. positive displacement pumps.
* ### [ Pumps - Suction Head vs. Altitude](https://www.engineeringtoolbox.com/suction-head-altitude-d%5F1343.html "suction head altitude lift elevation ")  
 The suction head of a water pump is affected by its operating altitude.
* ### [ Pumps - Suction Specific Speed](https://www.engineeringtoolbox.com/suction-speed-pumps-d%5F638.html "suction specific speed pumps efficiency")  
 Suction Specific Speed can be used to determine stable and reliable operations for pumps with max efficiency without cavitation.
* ### [ Static Pressure vs. Head](https://www.engineeringtoolbox.com/static-pressure-head-d%5F610.html "pressure head static fluid liquid")  
 Static pressure vs. pressure head in fluids.
* ### [ Viscous Liquids - Max. Suction Flow Velocities](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-viscous-fluid-d%5F231.html "Viscous fluids suction pumps velocity")  
 Recommended max. pump suction flow velocity for viscous fluids.
* ### [ Water - Boiling Points at Higher Pressures](https://www.engineeringtoolbox.com/boiling-point-water-d%5F926.html "boiling point water pressure")  
 Online calculator, figures and tables showing boiling points of water at pressures ranging from 14.7 to 3200 psia (1 to 220 bara). Temperature given as °C, °F, K and °R.
* ### [ Water - Max. Suction Flow Velocities vs. Pipe Size](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-water-d%5F228.html "Water flow velocity suction pump")  
 Recommended max. water flow velocities on suction sides of pumps.

## About the ToolBox

We appreciate any comments and tips on how to make The Engineering ToolBox a better information source. Please contact us by email

* [editor.engineeringtoolbox@gmail.com](mailto:editor.engineeringtoolbox@gmail.com?subject=https%3A//www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html%20%3A%20Pumps%20-%20NPSH%20%28Net%20Positive%20Suction%20Head%29)

if You find any faults, inaccuracies, or otherwise unacceptable information.

The content in The Engineering ToolBox is [copyrighted](https://www.engineeringtoolbox.com/COPYRIGHTS-NO-WARRANTY-LIABILITY-d%5F2231.html) but can be used with [NO WARRANTY or LIABILITY](https://www.engineeringtoolbox.com/COPYRIGHTS-NO-WARRANTY-LIABILITY-d%5F2231.html). Important information should always be double checked with alternative sources. All applicable national and local regulations and practices concerning this aspects must be strictly followed and adhered to.

##  Privacy Policy

 We don't collect information from our users. More about 

* [ the Engineering ToolBox Privacy Policy](https://www.engineeringtoolbox.com/privacy-policy-d%5F2228.html)

We use a third-party to provide monetization technologies for our site. You can review their privacy and cookie policy [here](https://www.snigel.com/privacy-policy/).

 You can change your privacy settings by clicking the following button: .

##  Citation

 This page can be cited as

* The Engineering ToolBox (2004). _Pumps - NPSH (Net Positive Suction Head)_. \[online\] Available at: https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d\_634.html \[Accessed Day Month Year\].

 Modify the access date according your visit.