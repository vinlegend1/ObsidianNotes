## Summary of Rectifiers
---

|                                                  | # Diodes | $V_m$       | PRV       | $V_{rms}$      | $V_{dc}$                              | $V_{r(rms)}$ | percent ripple |
| ------------------------------------------------ | -------- | ----------- | --------- | -------------- | ------------------------------------- | ------------ | -------------- |
| Half-Wave Rectifier                              | 1        | $V_s$ @peak | $V_s$     | $V_m$/2        | $V_m/\pi$ or $0.318 V_m$              | $0.386 V_m$  | 121%           |
| Full-Bridge Rectifier                            | 4        | $V_s$ @peak | $V_s-V_T$ | $V_m/\sqrt{2}$ | $2 V_m/\pi$ or $0.637 V_m$            | $0.308 V_m$  | 48%            |
| Full-Wave Center-Tapped Transformer              | 2        | $V_s/2$     | $V_s-V_T$ |                |                                       |              |                |
| $V_m = V_s - 2 V_T$ for **Full-Bridge**          |          |             |           |                | **Relationship**                      |              |                |
| **Ripple Factor:** $r = \frac{V_{rrms}}{V_{dc}}$ |          |             |           |                | $V_{rms}^2 = V_{r(rms)}^2 + V_{dc}^2$ |              |                |

---
![[Pasted image 20241023145248.png]]
- Half-wave Rectifier is basically a simple clipping circuit
	- The peak voltage of the output is the same as the peak voltage of the input
	- $$V_m = V_{s(peak)}$$
---
- Full-wave Rectifiers
	- Full-Bridge (Regular Transformer, 4 diodes required)
	- 2-diode Center-Tapped Transformer Setup
- Effects of Practical Diodes and Turn-on Voltage
	- Turn-on Voltage $V_T$ must be considered during KVL
---
* RMS Values
	* Peak is sqrt(2) times bigger than RMS dealing with Sine/Cos Waves
	* For Half-Wave: $V_{rms} = V_m / 2$
	* RMS and Average DC Voltage are Different (e.g. Half Wave): $V_{dc} = V_m / \pi$
	* When dealing with RMS values, there's both a DC and AC Component
	* $$V_{rms}^2 = V_{r(rms)}^2 + V_{dc}^2$$
	* V_rrms is like the AC component (or ripple) of the rectifier
---
**Center-Tapped Transformers**
![[Pasted image 20241023151553.png]]
* notice the sign polarities in V_s1 and V_s2, they are opposite
* meaning the diodes are gonna be mutually exclusive. One or the other is forward biased while the other is reverse biased

## Diode Specification
---
helps us choose the right diode
- Peak Reverse Voltage (PRV)
	- maximum allowable instantaneous reverse voltage without damaging the diode
	- Half-wave rectifiers require that PRV is at least equal to $V_s$
	- ![[Pasted image 20241023190856.png]]
	- In general, use KVL to solve for the required voltage
	- ![[Pasted image 20241023185213.png]]
	- $$PRV = I_L R_L + V_s / 2 = V_m + V_s / 2 = V_s - V_T$$
	- Full-Bridge: PRV = V_s - V_T
	- ![[Pasted image 20241023191002.png]]
- Average Forward Current (I_FMAX)
- Forward Power Dissipation (P_DMAX)
