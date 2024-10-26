- Viscous Internal Flow
    - Re = 2300
        - critical or transition Reynolds number
        - Reynolds number below which the flow is laminar, above which the flow is turbulent
- Dimensional Analysis and Similarity
    - Similarity: All relevant dimensionless parameters have the same values for the model & the prototype.
    - Similarity generally includes three basic classifications in fluid mechanics:
        - (1) Geometric similarity
        - (2) Kinematic similarity
        - (3) Dynamic similarity
    - Buckingham Pi Theorem
        - Definitions:
            - n = the number of independent variables relevant to the problem
            - j’ = the number of independent dimensions found in the n variables
            - j = the reduction possible in the number of variables necessary to be
            - considered simultaneously
            - k = the number of independent pi terms that can be identified to describe the problem, k = n - j
        - Summary of Steps:
            - List and count the n variables involved in the problem.
            - List the dimensions of each variable using {MLTΘ} or {FLTΘ}. Count the number of basic dimensions ( j’) for the list of variables being considered.
            - Find j by initially assuming j = j’ and look for j repeating variables which do not form a pi product. If not successful, reduce j by 1 and repeat the process.
            - Select j scaling, repeating variables which do not form a pi product.
            - Form a pi term by adding one additional variable and form a power product.
            - Algebraically find the values of the exponents which make the product dimensionless. Repeat the process with each of the remaining variables.
            - Write the combination of dimensionless pi terms in functional form:
                - ![](https://remnote-user-data.s3.amazonaws.com/1wYjEHfZ1A7Q5D76-SYr-hPX9dG59ADMHPOWqtn0jaLBx4rzsoOvCAbjEQpVM3p7-hGaD2EFov44ucsGPG-awpHRlOmvS-E7KBIBwqtH9001Chm2T0b-_xXzh43oY37b.png)![](https://remnote-user-data.s3.amazonaws.com/TfnYoBmCj3pGQCIPQZMxlI0Agzti6L2ZF77JJ-_6K0Bb4hGLNH9nDrUFcDhPC-zUduu0IpwV0_p0jnkxrfDKxcYRVUZSyMCLAS2Pc8P44Hwq8gyJ5B9FzuAIoGjfMVw4.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/CDqO0NndAp9hqQL7X5PrVnYRCYaK-1yN_thf-Xg6CvrQKWXrcDPBp8OA85vhj7XQz2_g679gluClpG2WeuytkG2EjuzQtOnge9Fj3CRuYEke_O9UXAsKN5Td222v89TT.png) 
- Differential Relations for Fluid Particles
    - ![](https://remnote-user-data.s3.amazonaws.com/57G7w7Bfp4OS6wCRfQpoKkn-zFe2uhYTfbU76bJhyMbvJnrSwE_NoPapMJR5uk0lCGPalDJsLV2rPOoDZy9IE83wKF_EtPUkFdZji8AhTSG1K6yQ0K0Sw1N7h33Y_p14.png) 
- Control Volume Relations
    - Reynold's Transport Theorem
        - 

          $$B_{sys} = \int \beta dV$$

          
        - 

          $$B = \frac{dB}{dm}$$

          
        - 

          $$\frac{dB}{dt} = \frac{\partial}{\partial t} \int_{CV} \beta \rho dV + \int_{A_e} \beta_e \rho_e V_e dA - \int_{A_i} \beta_i \rho_i V_i dA$$

          
        - where B is any extensive property (could be mass, momentum, etc.)
    - ![](https://remnote-user-data.s3.amazonaws.com/-_Wt-0viQxRqtaD-CJs4yXWhCKURd5cphUJO9c2LB33RxzLnZ2XjSbxMPAaMwOA7QWM67rnrsgWN4tOIWyYySB44vdsVksK4ySPcmrkPWzI3nRlyESuelndpyLtTqM5v.png)![](https://remnote-user-data.s3.amazonaws.com/Obi4RxEv0-RjVyXAKVOYlytbg20u5kqe-qi4AsNFGuEPO4p3ulU9t6tVADuUiy332yr1wCpBhFcoswRyk6dnlSGncxAipAnaOf0HGBTJ6lPsagAKueNw-uM2A8nxMl6P.png)
    - ![](https://remnote-user-data.s3.amazonaws.com/pfILqW30M2tAFp5lCm71d8LgZG5qsmUrpuHW5iuQVOcBOAwxFfhuPT-MUbGYaoPVXGLJ1IUpnLESxQyUTxqBsDUC31VzGrp1R3LPBkzzbzb3jsUriq5mJlt1ROhvt5I_.png) 
- Hydrostatics on Curved Surfaces
- Hydrostatics
    - Hydrostatic Pressure
        - **Summary:** 
            - 1. Find area in contact with fluid.  
            - 2. Locate centroid of that area.  
            - 3. Find hydrostatic pressure Pcg at centroid, typically = $\gamma h_{cg}$( generally neglect $P_{atm}$).  
            - 4. Find force $F = P_{cg} A$.  
            - 5. Location will not be at c.g., but at a distance $y_{cp}$ below centroid. $y_{cp}$ is in the plane of the area.   
        - 

          $$\nabla p = \rho (\vec{g} - \vec{a})+\mu \nabla^2V$$

          
        - 

          $$F = P_{CG}A = \rho g h_{CG} A$$

          
        - ![](https://remnote-user-data.s3.amazonaws.com/QrLDUST0FNVhoZorvejrup7edVIJWaoUY1OnhkjCZ9wblVFjiQBomk47wRHbqmPNSNV7C3CaFTW1Ag49ggSY2rfo8X7gS43WP-081Bxd9G4z6PRpg7zQgSBTWWOr23Vq.png)
        - ![](https://remnote-user-data.s3.amazonaws.com/Ba0gc-F5Dq8JpwmmgOxRwFSdDjjCQBczJhgKWforejHCuDdziS7zAG2_Q4786Nn4LS_TjYWZgIUOXfuYEGq0yESrcyUlot66j4bdSf72LPHiPNke9ap0l32L-LnhuCs7.png) ![](https://remnote-user-data.s3.amazonaws.com/5yoWYXXgmMt5Xh6gYonFDdiiLBKeN6uAN27bDjubkdKxIPn6nluMW3yjc-wBl-0YTG4AQ-I6IQrP7ZJcdIKn5C55I9fIo13Reo79y1hWqBD79-e1xGFtOU4CNDtBRoKb.png)![](https://remnote-user-data.s3.amazonaws.com/IkLgcqwqPmt57pqat6h9QnJDTQgKAtDX6npwDVs9iS3iG2QzFp60vXu7qozh77awDfkb_EplcMGw8mjh65iOTgOeAGdCZJdeqsSQa6bU4Vi4i5gwjWNyzF802cH-Mw2Q.png) 
        - Derivation of Hydrostatic Pressure
            - ![](https://remnote-user-data.s3.amazonaws.com/GUGir9MQCjSFBrbkIyzWBqtnc75mv4b-l6kZGJcYSX9iIpE-bDV1r7yq_iLF0cO0GP0vpc0uqa0DtqjgI-MyoRaihkcN4S_qlDZDr6rzVPdrR4_RJfQM0WvmRZVJjRjh.png) 
        - Different Weight, but same Pressure difference
            - ![](https://remnote-user-data.s3.amazonaws.com/g4pa3PXz10RqwFrDe3DUBMhlzmgCGp8HgVBy0Tna1x3TuNm8sQ5WHxr3CJGgZgZPmHcl951ae27THH-LFd-FQPG95XBzrATEAn5D1VB4C4JAIkQKLt0eQn_G4WpDrs_9.png) 
        - Atmospheric Pressure>>>
            - similar situation to hydrostatics for liquids but this time with the air and basically from our "atmosphere" to sea level
        - Measuring atmospheric pressure
            - ![](https://remnote-user-data.s3.amazonaws.com/FPdulLX5NAEcfgSIACoM71rKoAjYIy7wYcZaViBFiej9D0ip3LqcJFNYcAHCJR24zgnVxoELACsni29vpRuqsTUtm1eBCZggcx1s6LRfWxJZWQeXiUy_-TzwVw7900Pw.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/XFJydnWHxDBD0R7C5go398vLfskLgIplpqghMKM1CU03dSwKmzQlHIAr64jb1RwQTDbLOK8wKonypfEmAtgfjYyL9GXTOuYWFoWQCZFMoVZj2NP_wH64c37QzuQGbYGq.png) 
            - How does this "happen" (there should be an image)?→the air inside the can is being sucked and therefore a pressure difference between outside and inside creating a net force crushing the can 
            - How do you survive scooba diving?→you breathe in pressurized air in a tank to counteract the pressure experienced from the depths of the ocean
        - Measuring pressure generated by something
            - ![](https://remnote-user-data.s3.amazonaws.com/XxVeaKfSDVnzPiSju0ge9umSkdhCFRmWhF9yPXYXtauJJzwC5GQxX-1h3xSoC5A3yQIf8a2slDTVD0AOVKYozH_BhommMGSMSeIqZORi89_vnqU1ismm-aOgadAA92gH.png) 
        - When you're underwater, it's not a problem to {{breathe air out (exhale)}}. However, the problem is when you {{expand your lungs}}... you have to counteract the higher pressure of the surrounding area.
    - Archimedes' Principle
        - buoyant force
        - archimedes' principle→the buoyant force on an emerged body has the same magnitude as the weight of the fluid which is displaced by the volume 
- Pascal's Principle
    - Pascal's Principle>>>
        - pressure applied to an enclosed fluid is transmitted undiminished to every point on the fluid and to the walls of the container
        - scalar and no direction
    - Pressure>>>
        - $\lim_{\Delta A->0}{\frac{\Delta F}{\Delta A}} = P$ 
    - ![](https://remnote-user-data.s3.amazonaws.com/YK0cWwfOk4xnc8g0SUWdNPQnI1UPbBnXm2eMXunSYQXbkWe13aVZoRIWvNcBLCeWQ9LtqVGPN4uLB7cJwqH2OsIs790hs5ZXzP-mfc7pFvBEQ0rMzXI_mdqiR8D4tLgY.png) 
    - Conservation of Energy in Hydraulic Jack
        - ![](https://remnote-user-data.s3.amazonaws.com/bUFEwNbDP0sHBAfvlkyAueRCFgWsAaQ3QC_q-ilxLx3PbKMibH2WU3JAB2rat5R8Jwr91knGCf9CyB1LP2yCJ2zmb4mhK7XFH7knXlGRI4kZOI2K4NZm2XJNq4FiNq8F.png) 
    - 
- Pressure Distribution in a Fluid
    - Pressure (or any other stress, for that matter) causes no net force on a fluid element unless it varies spatially.
    - ![](https://remnote-user-data.s3.amazonaws.com/f2B4mNx9PCIMayOAGeJEiEe4ODXxekV0fHZtllAcLMXAONmsU_cTY_y6EdrTFLpGpTfU6LZAsc1hAQRca9M51-JY8EgUau92FtclUyajp229PKRR-X0rXFej1-x54PjE.png) 
    - Viscous Stress
        - ![](https://remnote-user-data.s3.amazonaws.com/mcukjKP8nz3mLlI2pZS40dnBbC5J82o6skT444dTRhksgVMZ_SATiYHsI_HODYJ_S37L_mZ8r-EH_J5PM6Zf95oVqSb1knhD1ylAJLKN7wLoWViIq0KXihPGWICeAcRv.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/IYsP3FC80ro-cJoj0ntR6cZfCY8kqxWTQildIEfrVhb4iCFbNAAo48NivYMdEWOhfodSuIITDKkcqxOMzk44Z3rEz_gO79yh3cuAVx2_3_cbR4SZVgQQarjeasGpYinD.png) 
    - Cases>>>
        - **Case 1**: Flow at rest or at constant velocity: The acceleration and viscous terms vanish identically, and p depends only upon gravity and density. This is the hydrostatic condition. See Sec. 2.3.
        - **Case 2**: Rigid-body translation and rotation: The viscous term vanishes identically, and p depends only upon the term (g  a). See Sec. 2.9.
        - **Case 3**: Irrotational motion: The viscous term vanishes identically, and an exact integral called Bernoulli’s equation can be found for the pressure distri- bution. See Sec. 4.9.
        - **Case 4**: Arbitrary viscous motion: Nothing helpful happens, no general rules apply, but still the integration is quite straightforward. See Sec. 6.4.
    - Hydrostatic Pressure Distribution
        - ![](https://remnote-user-data.s3.amazonaws.com/bP7_h_eIxkuugyWaItYBcMsJ7NXhky58BQ6plDxTFoPhjUYinjIosGqriYjY7nu3rFSrlSKs16oqCrMKtCBd52DxuaoHPEJQ0go5wOl3HWJ03-Trq9U26llQdLhjOTiJ.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/wwm9htAwhkzk826pUnxY4datPg_mDqHPFd-BBoc7rICPcOD0qXyuaC4v8_1nUL1lmWlQhdV9PNu6NNY-VkD2TBmhhoEF2e6lTvryx7e1572o_nO5mDyPntu31wECAouL.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/1PdG98SB9i313nBAcw4vcxZDVsB1NxJW9Q0oDGxIcKmpeY6npJpUaKKtweKY-pHPSftuQmewUgjECGYnPaqyCe8jOoOHKZujCtDO7it_psKxjmgRjk-qPnXMaABR7SkL.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/b8M74xRup7jPIzRbp_P-hj7-XIlAu7yRKnGf5hfGzSmLESjH5tX1CH8p9sajCdNvkwyC5v-1g8tn6iROAl79IWlmM2bNWWlpxc4BfvT2FTUXa9qhmEfFozVOcq6L1Ymi.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/jJjJkQrEwkkO63SJGLSLMwCqGIYNPNhqnob4Xl27DD6k4Nw3At96zKiMFGENEkZRbyItG1gBWK9lQvCFXJxkYf-Ls4X-UrP2PnTPWbAuDEDnewG9LIpVQEoMczMXIMNh.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/Q50U1J7t1xfoflSmry02IPlMyljaVePXP49h3uofuDCjpe5OYkA_-x0ypJ0kcgCkQVOuS955uEs0avGw6XK5Oz9tFYbdurd9JepmufxfRUr_F4HG521E5rczxLwyoHTg.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/gIeyj2p8nxzwTam2ZCfjRwoCU0GMtibdLKLt8yjJJb8krSw5D4ICc-othqo_adxbbGQ58kedOFYTCif3RdiJN1RkNMCNWZYwk7Yu5LJUVbe5_eIyiZNPEZWavS7cjNij.png) 
- Intro to Fluid Mechanics
    - Study of fluids at rest, in motion, and the effects of fluids on boundaries.
    - Manometer Equation
        - 

          $$\Delta P = \rho \frac{g}{g_c}h$$

           
        - ![](https://remnote-user-data.s3.amazonaws.com/uNw9-l6LmkZh_XaJns_RPnznkJU642UvnRwx90GYOtzhlEB2_oX8GeH-JZQD2cy5L30NiL3X0x1sZUJof-pLrvofrkCslzWfC-LQrB3dNPVQS8-57BJ-9ypgQfb7YtfX.png) 
    - Volume Flow Rate
        - 

          $$Q = \int_{CS} \vec{v} \cdot \hat{n} dA = \int_{CS} v_N dA$$

          
        - 

          $$\dot{m} = \int_{CS} \rho v_N dA$$

          
        - specific weight $\gamma = \rho g$
        - specific gravity $SG = \rho/\rho_r$ 
    - Transport Properties
        - coefficient of viscosity
        - kinematic viscosity
        - Reynold's Number: $Re = \frac{\rho V L}{\mu}$ 
    - Dimensions and Units
        - ![](https://remnote-user-data.s3.amazonaws.com/HwGOL1i4IsN-b-Wvled4TsLFh3pX7rRCPx5441IV5kW5sn_4fp98MSEXV9oLOdsmhCDbhskmsjvcxac0qN5ICpL3TrATZ2_cpWCBj6wN7IlxS3MObl0TtRYGXiG0hOKk.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/f_43FTfVL_UJnenxx4UTuRlOf1YAN3Z6hdrUA9T9Q6s-So2rXofOurCfSNOib5tcP_-iI6PP2Mt1jRjfnrkqdaMv8S6N6Z-q3pyfTbUR34Oo3ZK4TrdXaRO_HY9--mKE.png) 
    - Fluid Mechanics Descriptions
        - There are two different points of view in analyzing problems in mechanics. The first view, appropriate to fluid mechanics, is concerned with the field of flow and is called the {{eulerian }}method of description. In the {{eulerian }}method we compute the pressure field p(x, y, z, t) of the flow pattern, not the pressure changes p(t) which a particle experiences as it moves through the field.
        - The second method, which follows an individual particle moving through the flow, is called the {{lagrangian }}description. The {{lagrangian }}approach, which is more appropriate to solid mechanics
        - 
    - ![](https://remnote-user-data.s3.amazonaws.com/OPBJtgFYWLLuU7vyBWgKAHB1p0GFhCsgOzJmzfH_CalOZAfMskHR5YnWcdymZJOeYlLmq9HeEsmx6Szu7ZWe6NYpd1EG4dTg5hVHRPjJSyvU-fpSZnXTtOr09P5XXY8B.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/pLWZHVdfSehGgg-Yu6sKYkGWcbTx_dkAeoSjpLCE19QCThgkQPW4CPHgc-trZ1NGpPnLzANXsdn2fNFbHq-dL5uiA1W5juglWNfm0c_M5OexLLOibXO6OPcJZDdxe9im.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/0Axw0yk8FS3BKrHYgpgk13AXAQT6N4c5IDsNvLz9L31OVPYTXP27hWF-TN81NK__HM0_wV21N7YU7PyUp9W3N_8a8RPN3Wwose04ZKvKzhCxmqKw-O4SSp7MdTiUD3BG.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/vWHzTp8a3e5ucm__FM0eoaQtpvqpvFz50cyAfnKcdPBeRLtAoidqnVXOAXKynoHdjtQLZeVF9V5tO4pET3yCnXJ6yxsoaarZ8I_26EmlZsshwPl8qjWynHTezoL1orNo.png) 
    - [Density](../undefined/undefined/undefined.md) 
        - [Densities of Some Common Substances](../undefined/undefined/undefined.md) 
        - [https://remnote-user-data.s3.amazonaws.com/sUFob9RwBdpfSFxPg6NGP0OMTgJjZ_BCiEh4Aeseoi9_UVs8NQc18XDqCADhQyXeSF1LtzaH6jJhz04o65Ior69LodPru9m8iat2q6jF4ok9AhvpMbAccucN5c1yADy_.png](../undefined/undefined/undefined.md) 
    - [You purchase a rectangular piece of metal that has dimensions 5.0 × 15.0 × 30.0 mm and mass 0.0158 kg. The seller tells you that the metal is gold. To check this, you compute the average density of the piece. What value do you get? Were you cheated?](../undefined/undefined/undefined.md) 
    - [A cube 5.0 cm on each side is made of a metal alloy. After you drill a cylindrical hole 2.0 cm in diameter all the way through and perpendicular to one face, you find that the cube weighs 6.30 N. (a) What is the density of this metal? (b) What did the cube weigh before you drilled the hole in it?](../undefined/undefined/undefined.md) 
    - [Pressure at Depth in a Fluid](../undefined/undefined/undefined.md) 
    - [Pascal’s Law](../undefined/undefined/undefined.md) 
        - [https://remnote-user-data.s3.amazonaws.com/VOlA2vK3-opvvUFJyt3lGlXFYwme9RAtZwYXJVJwBObb8K8Wf_IktS4e6fpRYuQokMNvWb1TmnKQ2Ub03afWp9H4HmcOS6kL5l4C5JwgNM5AJeaV79zb3rRJrY7qOoIr.png](../undefined/undefined/undefined.md) 
    - [• Ear Damage from Diving. If the force on the tympanic membrane (eardrum) increases by about 1.5 N above the force from atmospheric pressure, the membrane can be damaged. When you go scuba diving in the ocean, below what depth could damage to your eardrum start to occur? The eardrum is typically 8.2 mm in diameter.](../undefined/undefined/undefined.md) 
    - [Absolute Pressure and Gauge Pressure](../undefined/undefined/undefined.md) 
    - Archimedes' Principle
        - ![](https://remnote-user-data.s3.amazonaws.com/XZKfBoebpMS4Snf-ldPQeTRrsv7wa61Nzd15lqdhARhmepmORPHmS2wJWS_4YzOFIKmvDfmlUDzOOdpiLPh1Si9MB8WYI-q8hDum-eF5C1rDTW8PkROinYTHOvTxwjgP.png) 
    - [Fluid Flow](../undefined/undefined/undefined.md) 
    - [The Continuity Equation](../undefined/undefined/undefined.md) 
    - [Bernoulli's Equation](../undefined/undefined/undefined.md) 
    - [Viscosity](../undefined/undefined/undefined.md) 
    - [Fluid Flow](../undefined/undefined/undefined.md) 
    - [Turbulence](../undefined/undefined/undefined.md) 
        - [https://remnote-user-data.s3.amazonaws.com/mgGOYYSChOzxjgU4fxGZ-YCBleV6g4MlKhuE-7UDR5XzvyLccDy3MMsAt3mNo-I0yuxvu1W5TJq1plXpb6xUaATszOnbJxqOYrcKiC2AlgakY62b_1WXv54UroskxKIb.png](../undefined/undefined/undefined.md) 
