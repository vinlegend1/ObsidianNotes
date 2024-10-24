# Zener Diode
![[20241024 115732.png]]
![[20241024 114616.png]]
VOLTAGE REGULATOR It is possible to reduce the ripple voltage by increasing the value of the capacitor filter. However, as the value of the capacitance is increased, it becomes bulky. ~Aside form this, the diode’s peak current increases sharply. In order to obtain a constant DC voltage across the load, the output of the capacitor filter should be passed through a voltage regulator. The simplest type of regulator is shown in figure 6.41 that employs a Zener diode. Is —_ + Rs Iz l l Ir + . + Y, Vi Vl_ Ry o - Figure 6.41 Zener Diode Regulator Circuit

![[20241024 115057.png]]
ZENER DIODE Zener diode is a special type of diode that is well designed to operate in the reverse breakdown region, also known as the Zener region. The schematic symbol for a Zener diode is shown in figure 6.42 and its characteristic curve is shown in figure 6.43. %K A Figure 6.42 Schematic symbol of a Zener diode k
## Operation of Zener Diode
From figure 6.43, we can see that a Zener diode is like an ordinary silicon diode when operated in the forward bias region. It will start to conduct current when the voltage across it is approximately 0.7 V. In the reverse bias region, the Zener diode assumes an open-circuit state or “off" state wntil the reverse voltage actoss it is equal to the Zener breakdown voltage, Vz. When the Zener diode is operating in the Zener region, it s said to be in ifs “on” state wherein the voltage across it remains almost constant equal to Vz over a wide range of Zener current. Irg is the mininmum value of the reverse cument that must be maintained in order to keep the diode in the breakdown region for voltage regulation. Iy s the maximum value of the reverse current that may pass through the Zener diode.
![[20241024 115515.png]]
![[20241024 115411.png]]
## Equivalent Circuit of Zener
![[20241024 115645.png]]
## Cases
![[20241024 11254.png]]
- **Case 1**: Variable V_i, fixed R_L
	- The purpose of the Zener Diode is to output a constant voltage value of $V_z$
	- hence, it can function as a voltage regulator
	- $$I_{smax} = I_{zmax} + I_L$$
	-  $$I_{smin} = I_{zmin} + I_L$$
![[20241024 11603.png]]