---
id: efe2dea2-b021-4f29-abc1-b999f72ae964
---

# Pumps - Suction Specific Speed

## Links
[Read on Omnivore](https://omnivore.app/me/pumps-suction-specific-speed-192c2c55b70)
[Read Original](https://www.engineeringtoolbox.com/suction-speed-pumps-d_638.html)

 #fluid_mechanics


## Content
##  Suction Specific Speed can be used to determine stable and reliable operations for pumps with max efficiency without cavitation.

Suction Specific Speed - _Nss_ \- can be useful when evaluating the operating conditions on the suction side of pumps. Suction Specific Speed is used to determine what pump geometry - radial, mixed flow or axial - to select for a stable and reliable operation with max efficiency without cavitation. Nss can also be used to estimate safe operating ranges.

Suction Specific Speed is a dimensionless value and can be calculated as

> _Nss \= ω q1/2 / NPSHr3/4 (1)_
> 
> _where_
> 
> _Nss \= Suction Specific Speed_ 
> 
> _ω = pump shaft rotational speed (rpm)_
> 
> _q = flow rate capacity (m3/h, l/s, m3/min, US gpm, British gpm) - for the pump at [Best Efficiency Point (BEP)](https://www.engineeringtoolbox.com/best-efficiency-point-bep-d%5F311.html "BEP - Best Efficiency Point")_
> 
> _NPSHr \= [Required Net Positive Suction Head](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "Net Pump Suction Head NPSH") \- for the pump at [BEP](https://www.engineeringtoolbox.com/best-efficiency-point-bep-d%5F311.html "Pump - BEP - Best Efficiency Point") (m, ft)_

[ Suction Specific Speed](https://www.engineeringtoolbox.com/suction-speed-pumps-d%5F638.html "Specific suction speed") can be compared with [ Specific Speed](https://www.engineeringtoolbox.com/specific-speed-pump-fan-d%5F637.html "Specific speed") but instead of using the [total head](https://www.engineeringtoolbox.com/static-pressure-head-d%5F610.html "Head - pressure") for the pump the [Required Net Positive Suction Head (NPSHr)](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "NPSH - Net Positive Suction Head") is used.

_Nss_ have the same value for geometrically similar pumps. As a rule of thumb the Specific Suction Speed should be **below** _9000_ (calculated with _US gpm_) to avoid cavitation and unstable and unreliable operations. Empirical studies indicates that safe operating ranges from [Best Efficiency Points (BEP)](https://www.engineeringtoolbox.com/best-efficiency-point-bep-d%5F311.html "BEP -  Best Efficiency Point") are more narrow at higher Suction Specific Speeds.

### Specific Suction Speed - _N_ss \- Pump Calculator

This calculator can be used to calculate the Suction Specific Speed for a pump.

* [Make a Shortcut to this Calculator on Your Home Screen? ](https://www.engineeringtoolbox.com/shortcut-webpage-homescreen-desktop-d%5F2230.html)

**Note!** When comparing pumps and their documentation - be aware of the units used.

### Convert between Imperial units _(gpm)_ and Metric units _(m3/h, l/s)_

* _Nss (US gpm) = 1.63 Nss (metric l/s) = 0.86 Nss (metric m3/h)_
* _Nss (Metric l/s) = 0.614 Nss (US gpm)_
* _Nss (Metric l/s) = 0.67 Nss (British gpm)_

### Example - Actual NPSHa vs Required NPSHr

For a pump with flow _1000 gpm_, head _500 ft,_ speed _3000 rpm_  and a maximum acceptable Suction Specific Speed _9000_ \- the Required Net Positive Suction Head can be calculated by modifying _(1)_ as

_NPSHr \= (ω q1/2 / Nss)4/3_

 _\= ((3000 rpm) (1000 gpm)1/2 / (9000))4/3_

 _\=  23 ft_

The calculated Required Net Positive Suction Head - _NPSHr_ \- is the minimum head required for proper operation. It is common to add a safety margin when calculating the Actual Net Positive Suction Head - _NPSH_a \- for the installation.

_NPSHa \= NPSHr Sr (2)_

_where_ 

_Sr \= safety ratio_ 

For the example above we assume the safety ratio _Sr_ to be _1.5 (50%)_. Actual NPSHa with the safety margin can then be calculated to

_NPSHa \= (23 ft) (1.5)_

 _\= 34.5 ft_

Note that if the head at the suction side of the pump is lower than the required NPSHa \- the head can be increased by

* increasing the dimensions on the suction pipes
* shorten the suction pipes
* remove or reduce the number of components like valves or filters in the suction pipes
* increase the static pressure in the system
* lower the pump (increasing the static pressure in the pump)

For a pump the actual [Net Positive Suction Head](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "Net Positive Suction Head NPSHa") \- [NPSH](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "Net Positive Suction Head NPSHa")a \- available in the process line is determined to _20 ft_. For a pump with rotational speed _1750 rpm_ and flow rate _500 US gpm_ \- the operating Suction Specific Speed with this NPSHa can be estimated to

> _Nss \= (1750 rpm) (500 gpm)1/2 / (20 ft)3/4_
> 
> _\= 4138_

* well below the limit 9000 to avoid cavitation

We can use the same safety margin as in the example above and calculate _NPSHr_ by modifying (2) to

_NPSHr \= NPSHa / Sr_ 

 _\= (20 ft) / (1.5)_

 _\= 13.3 ft_

### Double Suction Type Pumps

For a double suction pump the flow at the inlet is divided by two. Using a double suction pump is one way of meeting system _NPSH_ and obtaining a higher head.

##  Related Topics

* ### [ Pumps](https://www.engineeringtoolbox.com/pumps-t%5F34.html "pumps centrifugal water pressure air chemical well vacuum liquid oil hydraulic sewage submersible bilge fuel ")  
 Design of pumping systems and pipelines. With centrifugal pumps, displacement pumps, cavitation, fluid viscosity, head and pressure, power consumption and more.

##  Related Documents

* ### [ BEP - the Best Efficiency Point of a Pump](https://www.engineeringtoolbox.com/best-efficiency-point-bep-d%5F311.html "best efficiency point bep pump design")  
 BEP is where the pump is most efficient.
* ### [ Boiling Fluids - Max Suction Flow Velocities](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-boiling-fluid-d%5F230.html "Boiling fluids flow velocity suction pumps")  
 Recommended max suction flow velocity when pumping boiling fluids.
* ### [ Cavitation](https://www.engineeringtoolbox.com/cavitation-d%5F407.html "cavitation pumps valves")  
 Cavitation occurs in fluid flow systems where the local static pressures are below the fluids vapor pressure.
* ### [ Centrifugal Pumps](https://www.engineeringtoolbox.com/centrifugal-pumps-d%5F54.html "centrifugal pumps tutorial head pressure capacity")  
 An introduction to Centrifugal Pumps.
* ### [ Efficiency in Pumps or Fans](https://www.engineeringtoolbox.com/pump-fan-efficiency-d%5F633.html "pump fan efficiency power shaft fluid ")  
 The overall pump and fan efficiency is the ratio power gained by the fluid to the shaft power supplied.
* ### [ Fuel Oil Pumps - Suction Capacities](https://www.engineeringtoolbox.com/fuel-oil-pumps-d%5F1022.html "fuel oil pumps capacity vacuum feet lift")  
 Single stage and double stage fuel oil pumps and their suction capacities.
* ### [ Light Oil Pumping - Flow Velocities](https://www.engineeringtoolbox.com/pump-delivery-flow-velocity-oil-d%5F233.html "Light oils flow velocity")  
 Recommended max. flow velocities on the delivery side when pumping light oils.
* ### [ Light Oil Suction - Flow Velocities](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-oil-d%5F229.html "Light oils flow velocity suction pumps ")  
 Recommended max. suction flow velocities when pumping light oils.
* ### [ Positive Displacement Pumps](https://www.engineeringtoolbox.com/positive-displacement-pumps-d%5F414.html "positive displacement pumps rotary lobe gear peristaltic vane diaphragm")  
 Introduction tutorial to positive displacement pumps basic operating principles.
* ### [ Power Gained by Fluid from Pump or Fan](https://www.engineeringtoolbox.com/pump-fan-power-d%5F632.html "power pump fan")  
 Calculate the power gained by fluid from an operating pump or fan.
* ### [ Pumps - Affinity Laws](https://www.engineeringtoolbox.com/affinity-laws-d%5F408.html "affinity laws pump fan volume capacity flow pressure power")  
 Turbo machines affinity laws can be used to calculate volume capacity, head or power consumption in centrifugal pumps when changing speed or wheel diameters.
* ### [ Pumps - NPSH (Net Positive Suction Head)](https://www.engineeringtoolbox.com/npsh-net-positive-suction-head-d%5F634.html "pump npsh net positive suction head cavitation efficiency damage pump impeller ")  
 An introduction to pumps and the Net Positive Suction Head (NPSH).
* ### [ Pumps - Specific Speed](https://www.engineeringtoolbox.com/specific-speed-pump-fan-d%5F637.html "specific speed pump fan")  
 Characterizing of impeller types in pumps in a unique and coherent manner.
* ### [ Pumps - Suction Head vs. Altitude](https://www.engineeringtoolbox.com/suction-head-altitude-d%5F1343.html "suction head altitude lift elevation ")  
 The suction head of a water pump is affected by its operating altitude.
* ### [ Pumps - Suction Specific Speed](https://www.engineeringtoolbox.com/suction-speed-pumps-d%5F638.html "suction specific speed pumps efficiency")  
 Suction Specific Speed can be used to determine stable and reliable operations for pumps with max efficiency without cavitation.
* ### [ Pumps - Suction Specific Speed](https://www.engineeringtoolbox.com/suction-speed-pumps-d%5F638.html "suction specific speed pumps efficiency")  
 Suction Specific Speed can be used to determine stable and reliable operations for pumps with max efficiency without cavitation.
* ### [ Turbo Machines - Specific Work done by Pumps, Compressors or Fans](https://www.engineeringtoolbox.com/specific-work-turbo-machines-d%5F629.html "specific work pumps fans compressors")  
 Calculate specific work done by pumps, fans, compressors or turbines.
* ### [ Viscous Liquids - Max. Suction Flow Velocities](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-viscous-fluid-d%5F231.html "Viscous fluids suction pumps velocity")  
 Recommended max. pump suction flow velocity for viscous fluids.
* ### [ Water - Max. Suction Flow Velocities vs. Pipe Size](https://www.engineeringtoolbox.com/pump-suction-flow-velocity-water-d%5F228.html "Water flow velocity suction pump")  
 Recommended max. water flow velocities on suction sides of pumps.

## About the ToolBox

We appreciate any comments and tips on how to make The Engineering ToolBox a better information source. Please contact us by email

* [editor.engineeringtoolbox@gmail.com](mailto:editor.engineeringtoolbox@gmail.com?subject=https%3A//www.engineeringtoolbox.com/suction-speed-pumps-d%5F638.html%20%3A%20Pumps%20-%20Suction%20Specific%20Speed)

if You find any faults, inaccuracies, or otherwise unacceptable information.

The content in The Engineering ToolBox is [copyrighted](https://www.engineeringtoolbox.com/COPYRIGHTS-NO-WARRANTY-LIABILITY-d%5F2231.html) but can be used with [NO WARRANTY or LIABILITY](https://www.engineeringtoolbox.com/COPYRIGHTS-NO-WARRANTY-LIABILITY-d%5F2231.html). Important information should always be double checked with alternative sources. All applicable national and local regulations and practices concerning this aspects must be strictly followed and adhered to.

##  Privacy Policy

 We don't collect information from our users. More about 

* [ the Engineering ToolBox Privacy Policy](https://www.engineeringtoolbox.com/privacy-policy-d%5F2228.html)

We use a third-party to provide monetization technologies for our site. You can review their privacy and cookie policy [here](https://www.snigel.com/privacy-policy/).

 You can change your privacy settings by clicking the following button: .

##  Citation

 This page can be cited as

* The Engineering ToolBox (2004). _Pumps - Suction Specific Speed_. \[online\] Available at: https://www.engineeringtoolbox.com/suction-speed-pumps-d\_638.html \[Accessed Day Month Year\].

 Modify the access date according your visit.