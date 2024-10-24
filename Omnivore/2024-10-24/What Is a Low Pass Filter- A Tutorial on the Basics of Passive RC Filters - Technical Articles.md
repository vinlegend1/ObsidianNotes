---
id: f135d8e4-f9c1-4e35-b018-9dfc52785f2f
---

# What Is a Low Pass Filter? A Tutorial on the Basics of Passive RC Filters - Technical Articles
#Omnivore

[Read on Omnivore](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4)
[Read Original](https://www.allaboutcircuits.com/technical-articles/low-pass-filter-tutorial-basics-passive-RC-filter/)

## Highlights

> ![](https://proxy-prod.omnivore-image-cache.app/0x0,srkcLaSHnin-ORFhpNfeCnyGMoFO8XzGbTu-IlwWVueg/https://www.allaboutcircuits.com/uploads/articles/RC_low-pass_filter_12.png) [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#d4feaa67-da1a-4ba8-aa4c-68e25da552a2)  ^d4feaa67

> To create a passive low-pass filter, we need to combine a resistive element with a reactive element. In other words, we need a circuit that consists of a resistor and either a capacitor or an inductor. In theory, the resistor-inductor (RL) low-pass topology is equivalent, in terms of filtering ability, to the resistor-capacitor (RC) low-pass topology. In practice, though, the resistor-capacitor version is much more common, and consequently the rest of this article will focus on the RC low-pass filter. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#ac606c62-017f-4bf3-a337-95ab0b877f99)  ^ac606c62

> As you can see in the diagram, an RC low-pass response is created by placing a resistor in series with the signal path and a capacitor in parallel with the load. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#ca77ef29-cdb6-47b3-ad53-642c4f639328)  ^ca77ef29

> To determine the details of a filter’s frequency response, we need to mathematically analyze the relationship between resistance (R) and capacitance (C), and we can also manipulate these values in order to design a filter that meets precise specifications. The cutoff frequency (fC) of an RC low-pass filter is calculated as follows:
> 
> ![](https://proxy-prod.omnivore-image-cache.app/0x0,smju7z9VpKWT06e174Lvv_sqoXkKutOWeyHwMcOheURM/https://www.allaboutcircuits.com/uploads/articles/RC_low-pass_filter_formula_9.jpg) [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#34658197-8b2e-48de-a115-80c9eaa49eaa)  ^34658197

> We’ll try a cutoff frequency of 100 kHz, and later in the article we’ll more carefully analyze the effect of this filter on the two frequency components.
> 
> ![](https://proxy-prod.omnivore-image-cache.app/0x0,skTNZZL7QHkUw4EB0uvXg21yqFHT2PCgEPkMoPzr86Cs/https://www.allaboutcircuits.com/uploads/articles/RC_low-pass_filter_formula_8.jpg)
> 
> Thus, a 160 Ω resistor combined with a 10 nF capacitor will give us a filter that closely approximates the desired frequency response. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#7363aa92-8b9e-4906-b4b3-3661850d4edb)  ^7363aa92

> Thus far we have assumed that an RC low-pass filter consists of one resistor and one capacitor. This configuration is a **first-order filter**. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#2f0231d5-76a3-4585-8ee9-7b43424f5ca8)  ^2f0231d5

> All electrical signals contain a mixture of desired frequency components and undesired frequency components. The undesired frequency components are typically caused by noise and interference, and in some situations they will negatively affect the performance of the system. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#aa829817-296a-4896-9ea0-7d514cd6944d)  ^aa829817

> A low-pass filter is designed to pass low-frequency components and block high-frequency components. [⤴️](https://omnivore.app/me/what-is-a-low-pass-filter-a-tutorial-on-the-basics-of-passive-rc-192bdb195a4#ed8ed910-da83-4ec5-8150-1030542af639)  ^ed8ed910

