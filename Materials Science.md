- Hardness Tests
    - Hardness Tests
        - Mind Map by ChatGPT
            - ![](https://remnote-user-data.s3.amazonaws.com/AqY_pvYazC7Zrrwp7EqKzB97IWCNEWDIiJdFCH9-b2j3ZkEo9DJ9QTgiGe511Hzx--U72pP_7oL4LCMEHyT6big4SZR70sgnFgaQqy-hTbZHD8z-rK8fH-pCweP-pNRC.png) 
            - 
        - Indentation Hardness Test→A type of hardness test that measures the resistance of a material to permanent deformation. It includes the Rockwell and Vickers tests.
        - 
        - Rockwell Test↔Measures the depth of penetration of an indenter into a material under a large load (major load) followed by a smaller load (minor load).
            - Pros→Measures a wide range of materials, easy to perform, and provides reliable results.
            - Cons→Limited accuracy, sensitive to surface roughness and flatness.
        - Vickers Test↔Measures the depth of penetration of an indenter into a material under a constant load.
            - Pros→Measures a wide range of materials, provides more accurate results than the Rockwell test, and can be used for micro-hardness testing.
            - Cons→More time-consuming than the Rockwell test, requires a higher level of expertise, and is more expensive.
        - 
        - Rebound Hardness Test↔A type of hardness test that measures the hardness of a material by measuring the rebound of a mass after it impacts the material. It includes the Shore Durometer test.
        - 
        - Shore Durometer Test↔Measures the hardness of rubber, plastic, and other elastomers by measuring the depth of indentation made by a sharp-pointed indenter.
            - Pros→Non-destructive, portable, and easy to use.
            - Cons→Limited to measuring soft materials, can be affected by temperature, and provides only relative hardness measurements.
        - Brinell Hardness Test↔A type of hardness test that measures the hardness of a material by measuring the diameter of the indentation made by a hard steel or carbide ball under a known load.
            - Pros→Provides reliable results, measures a wide range of materials, and can be used for large components.
            - Cons→Limited to measuring materials with a minimum thickness, time-consuming, and requires a large load.
        - 
    - 
- Brittle Materials
    - Examples Of Brittle Materials
        - What are brittle materials?→Brittle materials are those which have little or no plastic deformation before they fail.
        - What are some examples of brittle materials used in industry?→Some examples of brittle materials used in industry include ceramic tiles, glass, cast iron, and concrete.
        - Why are brittle materials preferred in industry?→Brittle materials are preferred in industry because they are strong and rigid, making them suitable for applications that require stiffness and resistance to deformation. However, their lack of ductility and toughness can also make them more susceptible to fracture and failure under certain conditions, which must be carefully considered during design and use.
        - What are ceramic tiles?→Ceramic tiles are commonly used in bathrooms, kitchens, and other areas where water and moisture are present. They are preferred due to their high resistance to water and stains.
        - What are some applications of glass?→Glass is used in many applications, including windows, mirrors, lenses, and laboratory equipment. It is preferred due to its optical properties, transparency, and resistance to chemical attack.
        - What are some industrial applications of cast iron?→Cast iron is used in many industrial applications, including engine blocks, pipes, and cookware. It is preferred due to its strength and ability to withstand high temperatures.
        - What are some common uses of concrete?→Concrete is used in construction for buildings, roads, bridges, and other structures. It is preferred due to its durability, strength, and ability to withstand weathering and erosion.
        - 
- Fundamentals
    - Dislocations and Strengthening Mechanisms
        - Dislocations and Strengthening Mechanism
        - ???
        - Failure
            - Simple fracture↔separation of a body into two or more pieces in response to an imposed stress that is static
                - ductile fracture>>>
                    - preferred over brittle→brittle occurs suddenly and catastrophically without warning
                - brittle fracture→rapid crack propragation
            - fracture mechanisms>>>
                - ductile materials can exhibit brittle fracture
                - measured fracture strengths for most materials are significantly lower than those predicted by theoretical calculations based on atomic bonding energies
                    - discrepancy caused by presence of microscopic flaws
                    - flaws are often called stress raisers
                - a crack is assumed to be ellipsical and the maximum stress at the crack tip may be approximated by
                    - 

                      $$\sigma_m = 2 \sigma_0 \sqrt{\frac{q}{\rho_t}}$$

                      
                    - where $\rho_t$ is radius of curvatures
                - [Stress Concentration Factor](Engineering/Mechanics of Materials/Axial Load/Stress Concentration Factor.md)
            - fracture toughness
                - an expression has been developed that relates critical stress for crack propagation ($\sigma_c$) and crack length (a) as
                    - 

                      $$K_c = Y \sigma_c \sqrt{\pi a}$$

                      
                - K_c fracture toughness
                    - measure of a material's resistance to brittle fracture when a crack is present
                    - units of $MPa \sqrt{m}$ or $ksi \sqrt{in}$ 
                - Y dimensionless parameter
                    - depends on both crack and specimen sizes and geometries as wel as on the matter of load application
                - Fracture toughness testing
                    - Impact testing techniques
                        - Charpy and Izod ⇒ two standardized tests
                        - measure the impact energy
                    - Ductile-to-Brittle Transition
                        - determine whether a material experiences a ductile to brittle transition w/ decreasing temperature, and if so, the range of temperature over which it occurs
        - Fatigue
            - form of failure that occurs in structures subjected to dynamic and fluctuating stresses
            - [Fatigue.](Engineering/Mechanics of Materials/Mechanical Properties of Materials/Fatigue.md)
            - S-N curve>>>
                - as with other mechanical characteristics, the fatigure properties of materials can be determined from laboratory simulation tests
                - the higher the magnitude of stress, the smaller the number of cycles the material is capable of sustaining before failure
            - fatigue limit (endurance limit)>>>
                - limiting stress level below which fatigue will not occur
        - Factors that affect fatigue life>>>
            - mean stress
            - surface effects
            - design factors
            - surface treatments
        - case hardening>>>
            - a technique that enhances surface hardness and fatigue life for steel alloys
        - thermal fatigue>>>
            - normally induced at elevated temperatures by fluctuating thermal stresses
        - corrosion fatigue>>>
            - failure that occurs by the simultaneous action of a cyclic stress and chemical attack
        - [Creep.](Engineering/Mechanics of Materials/Mechanical Properties of Materials/Creep.md) 
    - Diffusion
        - Diffusion
        - many reactions and processes that are important in the treatment of materials rely on the transfer of mass either with a specific solid or from a liquid, a gas, or another solid phase
        - diffusion couple
            - copper and nickel
                - heated up and were sticked together, they diffuse
                - interdiffusion or impurity diffusion
            - There is a net drift or transport of atoms from high to low concentration regions
        - self-diffusion
            - exchanging positions of the same types
        - diffusion mechanisms
            - vacancy diffusion
            - interstitial diffusion
        - Fick's first law
            - time-dependent process
            - diffusion flux
            - 

              $$J = \frac{M}{At} [\frac{kg}{m^2 \cdot s}]$$

              
            - 

              $$J = -D \frac{dC}{dx}$$

              
                - similar to heat conduction equation
        - Fick's second law
            - non steady-state diffusion
            - the diffusion flux and the concentration gradient at some particular point in a solid vary with time
            - 

              $$\frac{dC}{dt} = \frac{d}{dx} (D \frac{dC}{dx})$$

              
        - Factors that Influence Diffusion
            - Diffusing Species
                - depends on the type of species
                - Ex. carbon self-diffusion vs. carbon-$\alpha$-iron @ 500 deg C
                    - 3x10^-21 vs 1.4x10^-12 m^2
            - Temperature
                - similar or analagous to rate constant
                - 

                  $$D = D_0 e^{-Qd/RT}$$

                  
                - 

                  $$k = Ae^{-E_a/RT}$$

                   
        - Diffusion in Semi-conducting Materials
            - solid-state diffusion application
                - fabrication of semiconductor integrated circuits
                - very precise concentrations of an impurity must be incorporated
                    - achieved by atomic diffusion (at least one way)
        - Other Diffusion Paths
            - may also occur along dislocations, grain boundaries, and external surfaces
                - sometimes called short-circuit diffusion paths
                - 

                  $$\frac{C_x-C_0}{C_s - C_0} = 1 - erf{\frac{x}{2\sqrt{Dt}}}$$

                  
                - solution to Fick's second law ‒ for constant surface composition
    - Imperfections in Solids
        - Imperfections in Solids
        - Point defects
            - vacancy, vacant lattice site
            - number of vacancies $N_v$ for a given quantity of material depends on and increases w/ temperature according to
                - 

                  $$N_v = Ne^{-\frac{Q_v}{kT}}$$

                  
                - where N = total # of atomic sites (atomes/m^3)
                - Q_v = energy required for the formation fo vacancy
                - k = Boltzmann's constant→$1.38 \times 10^{-23} J/atom \cdot K \text{ or }\\ 8.62 \times 10^{-5} eV/atom \cdot K$
            - self-interstitial
                - an atom from the crystal that is crowded into an interstitial site
                    - a small void space that under ordinary circumstances is not occupied
        - Impurities in solids
            - most metals are not highly pure, they are alloys
            - solute and solvent are terms usually used
            - substitutional and interstitial (two types of solid impurity point defects)
            - Hume-Rothery rules
                - determines degree solvent dissolves solute
                    1. Atomic size factor
                    2. crystal structure→must be the same for appreciable solid solubility 
                    3. electronegativity→the trigger $\Delta \chi$ intermetallic compund 
                    4. valencies→a metal has tendency to dissolbe another metal of higher valancy
        - Specification of composition
            - weight percent
            - atom percent
                - 

                  $$\rho_{ave} = \frac{100}{\frac{c_1}{\rho_1}+\frac{c_2}{\rho_2}} \text{ density }$$

                  
                - 

                  $$A_{ave} = \frac{C_1'A_1 + C_2'A_2}{100} \text{ atomic weight }$$

                  
        - dislocations>>>
            - linear defects
            - Burgers vector
        - Interfacial defects
            - boundaries>>>
                - external boundaries
                - grain boundaries
                - phase boundaries→exist in multiphase materials
                - twin boundaries→special type of grain boundary; mirror lattice symmetry
        - 
        - Bulk or volume defects
            - atomic vibration
            - can be thought of as a defect
            - at any instant of time, not all atoms vibrate at the same frequency and amplitude or w/ same energy
        - Microscopy
            - optical microscopy
                - light microschope is used to study microstructure
            - electron microscopy
                - when some structural elements are too fine or small to permit observation using optical microscopy
            - transmission electron microscopy→electron beam passes thru specimen
            - scanning electron microscopy→e-beam to surface, reflected electron displayed
            - scanning probe microscopy→topographical map (on an atomic scale)
        - Grain-sized determination
            - the mean intercept length $\bar{l}$, a measure of grain diameter may be determined
            - 

              $$\bar{l} = \frac{L_T}{PM}$$

              
            - often determined when the properties of polycrystalline and single-phase materials are under consideration
            - let G = grain-size #, n = ave. # g/in^2 at 100x magnification
                - 

                  $$n = 2^{G-1}$$

                  
                - 

                  $$n_M(\frac{M}{100})^2 = 2^{G-1}$$

                  
            - intercept length $\bar{l}$ to ASTM grain-sze number
                - 

                  $$G = -6.6457 log \bar{l} - 3.298 \text{ (} \bar{l} \text{ in mm) }$$

                  
                - 

                  $$G = -6.6353 log \bar{l} - 12.6 \text{ (} \bar{l} \text{ in in.) }$$

                  
    - Atomic Structure and Atomic Bonding
        - Atomic Structure and Interatomic Bonding
        - atomic number
        - electrons
        - energy quantization
        - quantum numbers
        - p.m.f of electron
        - electron configurations
        - periodic table
        - atomic bonding in solids
            - bonding forces and energies
            - <insert image from book>
        - Covalent Bonding
        - Bond Hybridization
        - Metallic Bonding
            - found in metals and alloys
            - valence electrons are not bound in any particular atom in the solid, more or less they're "free"
            - "sea of electrons"
            - the remaining nonvalence electron and atomic nuclei form ion cores
        - Secondary bonding (intermolecular forces)
            - van der Waals
            - induced dipole bonds
            - polar-induced
            - permanent dipole
        - Mixed bonding
            - most aren't purely one (ionic or covalent)
            - $%IC = [1 - e^{-\frac{(x_A - x_B)^2}{4}}] \times 100%$ $\%IC = [1 - e^{-\frac{(x_A-x_b)^2}{4}}]\times 100\%$
        - In general, these materials are associated with these types of bonds>>>
            - ionic↔ceramics
            - covalent↔polymers
            - metallic↔metals
            - IMF↔molecular solids
    - Classification of Materials
        - Classification of Materials
        - metals
        - ceramics→between metallica and nonmetallics; oxides, nitrides, carbides
        - polymers>>>
            - include familiar plastics and rubber materials
            - are organic based on C,H, O, N, Si, etc.
            - large molecular structures, often chainlike, often have backbone of carbon atoms
            - ex: polyethylene (PE), nylon, poly vinyl chloride (PVC), polycarbonate (PC), polystyrene (PS), silicone rubber
            - typically have low densities
            - generally dissimilar mechanical characteristics to those of the metallic and ceramic materials
        - composite>>>
            - composed of two (or more) individual materials (metals, ceramics, polymers)
            - to achieve a combination of properties
            - ex. fiberglass (small glass fibers are embedded within polymeric material)
                - glass (strong + stiff + brittle) + fiber (flexible) = fiberglass (strong, stiff, flexible, low density)
            - carbon fiber-reinforced polymer (CFRP) composite
        - Elastomers>>>
            - polymeric materials that display rubber-like behavior
        - Natural materials>>>
            - those that occur in nature
        - Foams>>>
            - typically polymeric materials that have porosites (contain a large volume fraction of small pores), which are often used for cushions and packagin
        - Advanced Materials>>>
            - Semiconductors
            - Biomaterials
                - must be biocompatible
                    - i.e. compatible w/ body tissues and fluids which they are in contact over acceptable time periods
            - Smart materials>>>
                - are able to sense changes in their environment and then respond to these changes in predetermined manners
            - Nanomaterials>>>
                - could be any material, but structure has to be on the order of nanometers ($10^{-9} m$) 
    - Structure of Crystalline Solids
        - Structure of Crystalline Solids
        - Crystalline material>>>
            - one in which the atoms are situated in a repeating or periodic array over large atomic distances
            - repetitive 3D pattern
            - the opposite is amorphous material
        - Unit cells
            - smallest repeat entities in crystal structures
            - FCC (face-centered cubic)
                - <insert image of FCC>
                - cube edge length: $a = 2R\sqrt{2}$ 
            - number of atoms per unit cell>>>
                - $N = N_i + N_f/2 + N_c/8$ 
            - coordination number>>>
                - the number of atoms or ions immediately surrounding a central aotm in a complex or crystal
                - ex. FCC coordination number = 12
            - atomic packaging factor (APF)
                - $APF = \frac{V_a}{V_T} = \frac{\text{volume of atoms in a unit cell}}{\text{total unit cell volume}}$ 
            - BCC (body-centered cubic)
                - cube edge length $a = \frac{4R}{\sqrt{3}}$
            - Hexagonal close-packed crystal (HCP)
        - Polymorphism and allotropy>>>
            - some metals and nonmetals may have more than one crystal structure
            - called allotropy when talking about elemental solids
        - Crystal Systems
            - unit cell geometry is completely defined in terms of 6 parameters
        - Crystallographic Points, Directions, and Planes
            - kinda like vectors
        - Crystallographic Directions
            - represented in a [] notation $[uvw]$
            - $\frac{x_2 - x_1}{a}, \frac{y_2 - y_1}{b}, \frac{z_2 - z_1}{c}$
        - Directions in Hexagonal Crystals
        - Crystallographic Planes
            - miller indices $(h, k, l)$
            - 

              $$h = \frac{na}{A}, k = \frac{nb}{B}, l = \frac{nc}{C}$$

               
        - Linear and Planar Densities
            - 

              $$LD = \# \text{ atoms on direction } \vec{v}/||\vec{v}||$$

              
        - Polycrystalline Crystals
            - most crystalline solids are composed of a collection of many small crystals or grains
            - grain boundary
                - exists some atomic mismatch within the region where two grains meet
        - Amsotropy
            - directionary of properties
            - associated with the variance of atomic or ionic spacing w/ crystallographic direction
            - isotropic
                - substances in which measured properties are independent of the direction of measurement
        - X-Ray Diffusion
            - determine crystal structures
            - Bragg's law
- Composites
- Ceramics
- Metals
    - Zinc Sulfide
        - Use case for zinc sulfide>>>
            - pigment for paints, plastics, and rubber
        - 
    - Chromium
        - Chromium Use Cases>>>
            - Stainless steel production: Chromium is a key component of stainless steel alloys, which are widely used in construction, automotive, and other industries. Chromium provides the steel with resistance to corrosion and rust, as well as enhanced hardness and durability.
            - Plating and coating: Chromium is used in plating and coating processes to improve the appearance, corrosion resistance, and wear resistance of metal parts and products.
            - Aerospace: Chromium is used in aerospace and aviation industries for its high strength and heat resistance properties. It is used in components such as turbine blades and exhaust nozzles.
            - Refractory materials: Chromium compounds are used in the manufacture of refractory materials such as bricks and crucibles, which can withstand high temperatures and harsh chemical environments.
            - Pigments and dyes: Chromium is used in the production of pigments and dyes, particularly for paints, inks, and textiles.
            - Wood preservation: Chromium compounds are used in wood preservation to protect wood from decay, insects, and other types of damage.
    - The Different Phases of Metals
        - Steels are one of the most widely used engineering materials>>>
            - alloy of carbon and iron
            - low carbon
            - medium carbon
            - high carbon
            - cast iron
        - What causes the change in mechanical properties?
            - a phase is a region of a material w/ uniform chemical and physical properties
        - Metal Alloys>>>
            - ferrous>>>
                - steels>>>
                    - 0.04-1.7 wt% carbon
                    - low alloy>>>
                        - low 0.04-0.3 wt% carbon
                        - med 0.3-0.7 wt% carbon
                        - high 0.7-1.7 wt% carbon
                        - iron alloy where carbon is the main alloy
                    - high alloy>>>
                        - tool
                        - stainless
                - cast irons→brittle 1.7-4.0% carbon
            - non-ferrous
            - 
        - Different phases of iron (Fe)
            - 3 different forms of iron depending on temperature
                - Ferrite ($\alpha -Fe$)↔BCC crystal structure, exists at < 910 deg C Magnetic 
                - Austenite ($\gamma - Fe$)↔FCC crystal structure, $910^\circ C < T < 1391^\circ C$ Non-magnetic
                - $\delta -Fe$↔BCC crystal structure, $1391^\circ C < T < 1536^\circ C$
            - Fe-C alloys
                - $\alpha$ and $\gamma$ are used to describe interstitial solid solutions of carbon in iron but are now not pure forms of iron
            - eutectic point>>>
                - corresponds to lowest melting
                - hypo-eutectic
                - hyper-eutectice
            - eutectoid point>>>
                - a point where a solid phase is in equilibrium w/ 2 other solid phases
            - cementite
            - pearline↔layers of ferrite and cementite
        - Metal Alloys, Phase Diagram, lever rule
            - periodic table gives us our basic metal building blocks
            - alloying a material w/ another allows us to {{change its properties}} 
            - Bronze↔an alloy of copper and tin
                - hard and resists corrosion↔perfect for statues
            - Brass is an alloy of Cu and Zn
                - softer and malleable↔musical instruments
            - Alloy>>>
                - binary
                - tertiary
            - Component←an element included in an alloy
            - Phase>>>
                - a region of material having uniform physical and chemical properties
                - what phase diagram
        - Ashby plots and performance indices
            - Ashby plot>>>
                - create a performance index
                - from fundamental equations / simplifications
                - create minimum baseline
            - example of Ashby plot>>>
                - Young's Modulus vs. density plot
            - consider factors like>>>
                - weight
                - stiffness
                - strength
                - cost
                - ease of manufacture
            - performance index unlikely to factor everything but {{good "filter"}} 
                - cost should be relative cost instead of absolute cost→so less volatility
        - Strengthening Material>>>
            - solid solution hard>>>
                - high impurity metals are almost always softer than ‒-
                - increasing impurities increases their tensile and yield shield
                - impurities cause lattice strains which impede dislocations
                - dislocation is a linear defect in a crystalline material
                - dislocation behavior affects mechanical properties
            - strain hardening
            - precipitation hardening
            - smaller substitution→tensile lattice strain
            - larger substitution→compressive lattice strain
        - Grain size
            - influence mechanical properties
            - mobility of dislocation
            - fine grain material stronger, harder
                - anneal at lower temperatures for finer grain
                    - 

                      $$\sigma_y = \sigma_i + \frac{k}{\sqrt{d}}$$

                      
            - strain hardening
                - metal becomes harder / stronger as it is plastically deformed
            - precipitation hardening
- Plastics
    - Polyvinyl Chloride
        - [www.bpf.co.uk/plastipedia/polymers/PVC.aspx](https://www.bpf.co.uk/plastipedia/polymers/PVC.aspx) 
        - 
- 
- Mechanical Properties of Metals
- --------------------- Portal ---------------------
    - Engineering
