---
title: "EMOD How-Tos"
author: "Anne Stahlfeld"
date: "4/14/2022"
summary: "This module discusses general how-tos for EMOD. It primarily focuses on how to change various parameters using python."
#output: html_document
---

###How to set the number of seeds
You will need to set the number of seeds to tell EMOD how many simulations you want to run. You can use this later to set a "Run_Number" parameter in the model builder setup.
```{python}
numseeds = 10 
```

###Sample Model Builder
The following model builder implements some of the changes discussed above.
```{python}
builder = ModBuilder.from_list([[ModFn(smc_intervention, day=start_days,cycles = smc_cycles, coverage_level=smc_coverage,
                                       adherence=adherence, num_groups = 100),
                                 ModFn(set_EIR, EIRscale_factor=eir_scale_factor),
                                 ModFn(scale_cmax, cmax_scale_factor=cmax_scale_factor),
                                 ModFn(scale_krate, krate_scale_factor_AQ=krate_scale_factor_AQ,
                                 krate_sp=kmaxSP,num_groups = 100),
                                 ModFn(scale_c50, c50_AQ=1, c50_SP=c50SP, num_groups=100),
                                 ModFn(DTKConfigBuilder.set_param, 'Run_Number', x),
                                 ModFn(DTKConfigBuilder.set_param, 'Sample_Number', r),
                                 ModFn(sim_expl,pyr=pyr)
                                 ]
                                #for r, row in top_param_df[:num_params_to_run].iterrows()
                                for r in [1] 
                                for kmaxSP in [1.579]
                                for c50SP in [10.826]
                                for eir_scale_factor in [2]
                                for cmax_scale_factor in [1]
                                for smc_cycles in [1]
                                for krate_scale_factor_AQ in [1]
                                for smc_coverage in [0.9]
                                for adherence in [1]
                                for start_days in [213]
                                for x in range(numseeds)
                                #for pyro in np.linspace(0.1,20000,10)
                                for pyr in [15000]
                                ])
```

