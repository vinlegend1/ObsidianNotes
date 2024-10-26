- Sampling
    - Hypothesis Testing
        - Null and Alternate Hypotheses
        - **Null Hypothesis** $H_0$ - your “initial assumption”
            - when applied in a problem, it usually comes in the form: $H_0 : \mu = \mu_0$  where $\mu_0$ is any number depending on the problem
        - **Alternate Hypothesis** $H_0$ - your “alternate assumption” to the initial assumption
            - this dictates whether you should consider **one-tailed** or **two-tailed** (important for getting **significance level** and **critical regions**)
            - Examples:
                - $H_1 : \mu \neq \mu_0$ 
                - $H_1 : \mu < \mu_0$
                - $H_1 : \mu > \mu_0$
                - $H_1 : p \neq p_0$
                - $H_1 : p < p_0$
                - $H_1 : p > p_0$
        - 
        - Type 1 and Type 2 Errors
            - ![](https://remnote-user-data.s3.amazonaws.com/toLlnsRTULhSnbUJClcaji2_Bh4jO0-Dmdk1pxbnrd7j0LsgIbAYgiN_7XfqwO-aTqgd714P7I-R0I-LYPRiPVcLm9OtZ3kA_bNYZF6PX9apXROn_HgGak8zz20g5fte.png) 
        - Key Formulas
            - Single Sample
                - Single Mean>>>
                    - 

                      $$z = \frac{\bar{x} - \mu_0}{\sigma / \sqrt{n}}$$

                       
                - when to use: {{when population standard deviation }}$\sigma${{ is known or sample size is large (}}$n \geq 30)${{ }} 
            - 📢 Use the T-statistic if condition above is not met>>>
                - $t = \frac{\bar{x} - \mu_0}{s / \sqrt{n}}$ 
                - where $s$ is the sample standard deviation
            - Single Proportion>>>
                - 

                  $$z = \frac{\hat{p} - p_0}{\sqrt{\frac{p_0(1-p_0)}{n}}}$$

                   
        - Two Sample Mean and Proportion
        - ![](https://remnote-user-data.s3.amazonaws.com/vbie-UMTYca9z57kjkfY1Q-Io24Li3Swh6BAFbhslGSJ-TB5BJpuQb8p3mberoJKj3j6-JtRfC90vQRD0WlIiH1Gc-GGUM--HlymcgqONITb06KlPnvFULabmab91cPI.png) 
        - 
        - Paired Observation>>>
            - $H_0 : \mu_0 = d_0$
            - $t = \frac{\bar{d} - d_0}{s_d/\sqrt{n}}$
            - $df = n - 1$
        - Variance Testing
            - Chi-Squared (Single Sample Variance)>>>
                - 

                  $$\chi^2 = \frac{(n - 1) s^2}{\sigma_0^2}$$

                   
            - F-Distribution (Two Sample Variance)>>>
                - 

                  $$f = \frac{s_1^2}{s_2^2}$$

                   
        - 📢 Use Chi-Squared Distribution for statistic with degrees of freedom $v = n - 1$ 
    - Sampling
        - ![](https://remnote-user-data.s3.amazonaws.com/qxYJdr94aC5w0VreYYa9g5-JwHkhezInMV_Bal2K5bK6rA9Dih2pc9D49dg-V5lp5JGIYhaDF1SP5LjOit0OTYo5BT9bqT4tc5QuPP0awnBBOFiG7-VAgIkRM4NEsytT.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/vj6Xo_v2KJ_2p9BxDc-X9U4lADK6_q1XaDMGjfUsWZHPRN0_d1MCBjSDCdOzKdocBiWILnhLUwSObfl7eXtdcQpQvrN66Ksuoeq20jYajh8GJA7NTolC0v7V9poUYzjf.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/j9bx2luoDOKfdaXR6kQeG_nTmZ_2dPmQvB4ZMNsj3MJ4Ql4Jj82hAxo5gVe-cZZlIiIRFrTxv0KM1YrLEN30CTlUZqVgGGFikM9EkzYQFBsr0Ew1YH1tq19r6d3Iwrtp.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/KT2V90UolHBQzUkQkrUSRQjOVf3LEKkMUpS1WMqPXUih_DEYtkNAa1garVY3c8w2YZ7Nm1GurcCnlA_wP6O_MG4YknVU35c5zSe82hR27vKHKOTRWUy1OAR6gsdrkRSx.png) 
- Continuous Distributions
    - Normal Distribution
        - ![](https://remnote-user-data.s3.amazonaws.com/p1TSnh4lwl7RorAW99FfdWuhiuIpNNXiBhzcDFD00Fot5yww8bQ5rgGOTfB-6Oq4Uvyabv4p7yoDjBvKwyYxa2bcBNAhtqQetYOO68dgX56vd9LMtoOxEIDgo0fkrF69.png) 
    - Continuous Random Variable
        - ![](https://remnote-user-data.s3.amazonaws.com/GSdou64y8L7kIuXhmr-8BiXBcSQJzTDSUvYMjBRcoLe8NaLVCw8gSTJoCo-T9gB3okKpSXTz2yiZMvqwwVzGzO5XC2_ohKTJ0EStjswY9ma4vig-cpnEUwZWiTqYQPRb.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/lGb7iJPpO0DG4qdfIu1HVaIVc-BUihw8c8eLe9er8qL9jvR4i98bQ0ZjN4JkXcVek6B6Is4Mn7-VM6CbGKzgGTQWze-kva2X_zGwaYe9SiObvcbKbBmXKSURY8b1DE9m.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/k_dwspEjto4dhWhYb5YeIc1NQgX7U9f_LTQXHYYSIJl8OgqMFCjReprnlr-VHOmu5fk3oIG72EVz0Cl9grGu1WDhIr1DRWEiBxte-ocJHmiLeE_M6_DFeu2eHT1kKPj-.png) 
    - 
- Discrete Distributions
    - Poisson Distribution
        - ![](https://remnote-user-data.s3.amazonaws.com/giqryPo7NvzGQsy4G7JYFoNrT9nxa0nAnieH5QlYquZiugiqRqhVCZWvpXnR9UjJoCIbwXvc6tKBi1NhX024gQDwvahxYc5_fGwyPD2MS9v5DStgH80icI8C-rENpZVP.png) 
    - Hypergeometric Distribution
        - Hypergeometric
            - A random sample of {{size n is selected from N items.}} 
            - The N items may be subdivided into two groups, k of the items are classified as successes. Thus, N — k of the items are considered failures. The choice of successes is arbitrary.
            - Sampling is done {{without replacement}}.
            - ![](https://remnote-user-data.s3.amazonaws.com/ajNJ2omde-NKT7NrVhchng3x7ZqmkvZy4m9jp66LpJnjneloBzOKHkxL1j2l29_2kAtNvQN-LW2pihnoKatd76FA7fqdf_8pVPEQu4lqIJ0bYxVKqTp7YL5RI-A1hxNI.png) 
        - 
        - ![](https://remnote-user-data.s3.amazonaws.com/VpcgB7wzGa23L6AXc8I-yixLnlQsebAmX19FS-OhDAzUOZIOjfC2VZqLvUF2hUKGxs5qtG7DfVnhVHqG0zzpcoArSlVWMxaRt2640_V9mLeNEZeWi2DUi1AnlJdbA5K9.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/4tqFykDAY3JgSdYGEwz8rWzIDzlcikEBx4hNdHT4vAAxmUYpv9NeCDaXPgNYnDPEEIFbb_QcqIgtp8uaSKtHNNWbbvTk_nB9JE0Hkno9MafMoVraTHSEgQB77oLgxbQJ.png) 
        - 
    - Random Variable
        - Discrete vs Continuous Random Variable
            - ![](https://remnote-user-data.s3.amazonaws.com/rtQXgfTDqGU0H9mkScnwVFt4dD3GGO-TZc4IHO4kdVbu_geS3fJIdF5h5JUPh0YhtCq63ac_cUme-FURdGJCDqISzj9IDN9Ej_MXzOOv_YnVvULldVAySmo2vJhPnxhT.png) 
        - Expectation Value and Variance
            - ![](https://remnote-user-data.s3.amazonaws.com/A3SIRlxYzb9ZMArUhifujty4JHsDvNz_99rg8K6cRIym3ZHpqqr_cHbLpHZtFDI7W4xbprFI7E9tUgsvhhFXeqmlaUeNmwPyNClYH7u8GIWb5M8a7Vn3uqtVrF1CSmo-.png) 
            - ![](https://remnote-user-data.s3.amazonaws.com/OWrr8VpJpa1_JC6EPXcNJBg0U4-e8gavE2FKR6AoU_oJWIBWje-DXbeoP0RhnX2YszTcvqwJCPnE_ecgGHjxnPUqchK6cJhOeoiFawuVPVimYDkrU5SOTJ26yJSryn4K.png) 
            - ![](https://remnote-user-data.s3.amazonaws.com/HPf_acFe0yMlp0H0aYNPWEH72qh9s3QOb83RIFsYu42trSrsxOG87dohRjSYHHmN8lUOxMslCFtlB44zjxh_6ETph_pThKZP7PKC8FpIQEPmBtEPTCGUHNKn-cIYlug9.png) 
        - 
        - ![](https://remnote-user-data.s3.amazonaws.com/4fYK5aSp-At8Lr7DeJ2_Dw8o11-V7_vJK4E_sgn8XNXnaZdOKUP7JljhbUWmrIN9VLVn51xu9Jjx_92rchNkpAL5SevWdszPrtKAgA5inDljqfLJjCQtZcHB-DVH3SU8.png) 
    - Discrete Probability Distributions
        - 4 main types discussed in FNDSTAT>>>
            - Discrete Uniform Distribution
            - Binomial Distribution
            - Hypergeometric Distribution
            - Poisson Distribution
        - 
        - Discrete Uniform Distribution
            - self explanatory
        - Binomial Distribution>>>
            - The experiment consists of n repeated trials
            - For each trial, there are only two mutually exclusive outcomes, success and failure. The event that corresponds to success is chosen arbitrarily. Usually, it isdefined in accordance to what is required in the problem.
        - ![](https://remnote-user-data.s3.amazonaws.com/Y3wOZ-yZSUYttc4xbqK1cc4UkXW2ac-nF9cMTnwivyo79JfAtVDHG85mDu3XD5H1Itxtn-FDr95ken7Kfgpdxhr73NEbF4hYLBlmW7Tm1msLrQC70ht7mTefFWV8sm_A.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/RGW4qJw-92Mmtnm54LfalIxcOVy9aCUvfkTiO1jj5tjnQiB4yVmyIC8HuRI7wQJlyt2rMWPfYcJc75fRdg3LVsuEpmSNGM5BUnL0jSntJTZ9LbNp_WRSWPjz-KN8RU2l.png) 
        - ![](https://remnote-user-data.s3.amazonaws.com/6a13eS1M51Y7BSIuB1etnIL6IgiHDtqTu0Ggzki2mjd53CZBV95gXa4bxeHfjrXuIE9cB2emHP3wSCttBmhd3aTGFDGGrmMyx8HbXvPhhi-c-F8Wmdxgpfa2-LXsmebz.png) 
- Definitions and Introductions
    - Classical Probability and Counting Techniques
        - Counting Theory
            - Possibilities come from experiments, processes that lead to outcomes.
            - The set of all outcomes in an experiment is called a sample space.
            - The goal is to count all the possible outcomes in an experiment.
            - A set of techniques used for identifying the total number enumerating the elements of the sample space.
        - Multiplication Rule>>>
            - If a task can be performed in ways, and if for each of these, a second task can be performed in ways, and for each of the first two, a third task can be performed in ng ways, and so forth, then the sequence of k tasks can be performed in
        - Addition Rule>>>
            - Suppose a task A can be done in m ways, task B in n ways, and both can be accomplished in k different ways. Then, task A or B can be done in...
        - 
        - Permutations
            - ![](https://remnote-user-data.s3.amazonaws.com/45epJu3z-0Wegq84qwUdaOZaZ1r8D5DQy4rToydBok345sz8HUSR7ZfJ56e9Swh9CF6ZMqcrOcRxC-rfeFAQoWkrSQPOhXIOBik9gLTbnglS6ckG51rcGWCDPVboipOy.png) 
        - 
        - Combinations
            - ![](https://remnote-user-data.s3.amazonaws.com/rYss6AdxZ05ba1zKYkd4o1ZTlQ9Hj25RU6vZECbgq1sbjTOqg4qtATYy8JRwcW3NLLH0KiNWJZavUAIFOyCBf0edYICDHXTPZZlflQAQKdD5R4lrF4_InvhocPeFnP-4.png) 
        - 
        - Circular Permutations
            - 

              $$P_{nc} = (n-1)!$$

               
            -  
        - 
        - Permutations of Similar Objects
            - ![](https://remnote-user-data.s3.amazonaws.com/r4V2HyDkx2-EBwtmTk6iNpDuI6OGbNxdFdao0FMuM6WGXzZMdClJja4eaRu3nRkx0CO98Hnc-MCefhWqzagpGn8lsD19jZ2t57SyMw-wo2BcOpzYlTSQzAmymfhv67nn.png) 
            - examples:
                - ![](https://remnote-user-data.s3.amazonaws.com/W0Ms7nQ3wlWC1-knxIqWTDG4paLQCUKfnyoM6ILwbEfwlMdtAEAPb_yT-XDjY5qBprYCGBURt9XIfHVGh40wu73scSGWsYqs6x-a6dKAMppK9sL8wWlw9f-V11ULkgWM.png) 
        - Number of Ways to Group Objects
            - ![](https://remnote-user-data.s3.amazonaws.com/LCWpDih08fW51M1r38yFtIwOgxAqXt3mT2utPBrz5naumn6h9lShEYlGmws95G9lp5X9kKLaZWaHHHbDorrcSj_1vroFnHzGnqCLATXszdsvViE8d5gtBRqy1Jrfp79m.png) 
    - Introduction
        - Parameters vs. Statistics
            - Parameters↔numerical values that describe the characteristics of a population
                - examples: $\mu$ (mean); $\sigma$(standard deviation)
            - Statistics↔numerical values that describe the characteristics of a sample
                - examples: $\bar{x}$ (mean) s (standard deviation)
            - 
        - 
        - Measures of central tendency>>>
            - mean, median, mode
        - 
        - Measures of position>>>
            - quartiles
                - Are measures that divide a set of data points into four parts
                - Q1 (25%), Q2 (50%), Q3 (75%)
        - 
        - Measures of variance>>>
            - 

              $$\sigma^2 = \frac{\Sigma (x_i - \mu)^2}{N}$$

              b 

              $$s^2 = \frac{\Sigma (x_i - \bar{x})^2}{n - 1}$$

               
        - 
        - 
    - Skewness
        - skewness
            - When skewness is 0, {{median is also the mean}} 
            - Mean<median, skew is {{negative}} 
            - Mean>median, skew is {{positive}} 
        - kurtosis→how fat or thin the distribution is 
            - {{Leptokurtic }}- kurtosis measures are near the tails and is positive
            - {{Platykurtic }}- kurtosis measures are near center and is negative
            - {{Mesokurtic }}- kurtosis = 0
        - 
- Probability
    - ![](https://remnote-user-data.s3.amazonaws.com/uCdBA-NuqjatO7n1pSPyHe3TOpK_NOLnY0sfIl8AjVaCHYncAonM9sIeULgJaHwikE2lUWibezxDrmpeuUrhIxhIltdS9QJ4uNTiRKojdpM0TEwW8mXhiV-VIULPa4s-.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/MO2flYvyiRBt3FvTA7QICB0wudLnRA-apz7jMc5465XYd81d3bOaGIzGdEU9H7MNY2ZpraLVeqKhKpB57gnjjqEncl2hhwXlGp0m_6KQb8cHizVJbG4gSjT5XHJ9Ysmu.png) 
    - ![](https://remnote-user-data.s3.amazonaws.com/rvKPxZyfiApz5UStNw5HFoArjjgmCUy6ZrDr_oyR58Rhkk2UdpooocwDz5hO0XW8SFS0RVbhU23tmH0eQ6k1w7hde01eFADAFY_e0ghG7W_N2-T2yrUfs5eSC1C_yrqc.png) 
