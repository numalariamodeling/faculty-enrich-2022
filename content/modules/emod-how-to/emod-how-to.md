---
title: "EMOD How Tos"
author: "Anne Stahlfeld & Tobias Holden"
date: "4/18/2022"
output: 
  blogdown::html_page:
    toc: true
---

### Set the number of seeds

You will need to set the number of seeds to tell EMOD how many simulations you want to run. You can use this later to set a "Run_Number" parameter in the model builder setup that will force EMOD to iterate through the range of values to reach the length of numseeds, in this case 0-9.


```python
numseeds = 10 
```

### Update config parameters

You may need to update a variety of configuration parameters for your simulations. These parameters can be explored more in depth in the [EMOD config documentation](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration.html). Broadly, configuration parameters can be used to set up certain things in these categories: drugs and treatments, enable/disable features, general disease, geography and the environment, immunity, incubation, infectivity and transmission, input files, larval habitat, migration, mortality and survival, output settings, parasite dynamics, population dynamics, sampling, scalars and multipliers, simulation setup, symptoms and diagnosis, vector control, and vector life cycle. Generally, we create a setup_simulation() function that contains the configuration update function for the config_builder (cb). For parameters that won't often change you can hard code them directly into this function, while it may be beneficial to call others as a variable, such as sim_years, that can be set when the function itself is called later. This can be done inline in the code or within the model builder.


```python
def setup_simulation(cb, sim_years=2):
    cb.update_params({'Demographics_Filenames': ['under_5_demographics_with_SMC_access_and_IIV_iiflag.json'],
                      'Vector_Species_Names': [],
                      'Enable_Vital_Dynamics': 1,
                      'Enable_Births': 1,
                      'Disable_IP_Whitelist': 1,
                      'Simulation_Duration': sim_years*365,
                      'Maternal_Antibodies_Type': 'CONSTANT_INITIAL_IMMUNITY',
                      "Incubation_Period_Distribution": "CONSTANT_DISTRIBUTION",
                      'Birth_Rate_Dependence': 'FIXED_BIRTH_RATE',
                      'Climate_Model': 'CLIMATE_CONSTANT',
                      "Parasite_Smear_Sensitivity": 0.02,
                      'logLevel_JsonConfigurable': 'ERROR',
                      })
                      
cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
setup_simulation(cb,7)
```

### Create the model builder

The following model builder implements some of the changes discussed above as well as references other specific functions from the NU team's SMC work.


```python
from simtools.ModBuilder import ModBuilder, ModFn

builder = ModBuilder.from_list([[ModFn(smc_intervention, day=start_days, cycles=smc_cycles, coverage_level=smc_coverage),
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
                                for start_days in [213]
                                for x in range(numseeds)
                                #for pyro in np.linspace(0.1,20000,10)
                                for pyr in [15000]
                                ])
```

### Set Forced EIR 

For simulations that don't use vector data to establish transmission, a forced EIR can be used as a proxy. These data are typically recreated from previous literature sources that provide monthly EIR levels, input here as a monthly_site_EIR list. This can then be converted to a daily EIR using the monthly_to_daily_EIR helper function and summed to calculate the annual EIR for the site. The add_InputEIR function is called and given the calculated daily EIR to apply to the simulations. It can be scaled using a scaling_factor in order to create the best fit to outcomes data.


```python
from dtk.interventions.input_EIR import add_InputEIR
from helper_scripts.monthly_to_daily_EIR import monthly_to_daily_EIR

def set_EIR(cb, EIRscale_factor):
    monthly_site_EIR = [15.99, 5.41, 2.23, 10.33, 7.44, 11.77, 79.40, 85.80, 118.59, 82.97, 46.62, 33.49]
    
    daily_EIR = monthly_to_daily_EIR(monthly_site_EIR)
   
    add_InputEIR(cb, start_day=0, EIR_type='DAILY', dailyEIRs=daily_EIR, scaling_factor=EIRscale_factor)

    annual_eir = sum([x for x in daily_EIR])
  
    return {'EIR scale factor': EIRscale_factor}
```

### Change drug adherence

Adherence to drugs can be modified using configure_adherent_drug(). This allows you to detail doses (and drugs given), intervals between doses, actual adherence values, and more.


```python
from malaria.interventions.adherent_drug import configure_adherent_drug

def smc_adherent_configuration(cb, adherence):

    smc_adherent_config = configure_adherent_drug(cb,
                                                  doses=[["SulfadoxinePyrimethamine",'Amodiaquine'],
                                                         ['Amodiaquine'],
                                                         ['Amodiaquine']],
                                                  dose_interval=1,
                                                  non_adherence_options=['Stop'],
                                                  non_adherence_distribution=[1],
                                                  adherence_config={
                                                        "class": "WaningEffectMapCount",
                                                        "Initial_Effect": 1,
                                                        "Durability_Map": {
                                                            "Times": [
                                                                1.0,
                                                                2.0,
                                                                3.0
                                                            ],
                                                            "Values": [
                                                                1,
                                                                adherence,
                                                                adherence
                                                            ]
                                                        }
                                                    }
    )
    return smc_adherent_config
    
adherent_drug_configs = smc_adherent_configuration(cb, adherence)
```

### Adding drug campaigns

Using add_drug_campaign() you can set different drug campaigns including MDA, MSAT, SMC, fMDA, MTAT, rfMSAT, and rfMDA. This function also includes the ability to set coverage levels, repetitions (such as SMC cycles) and the timesteps between them, diagnostics information for campaigns that include testing, target groups, and restrictions on who can receive drugs by node or individual properties. For more details on all possible specifications see [malaria_drug_campaigns.py](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/malaria_drug_campaigns.py) in dtk-tools-malaria. Node and individual properties are set in the demographics file and can be called upon here for things like low vs high access groups.

This example details a simple SMC intervention. Its coverage level, number of cycles, and start day are specified in the model builder as they may change more regularly based on the purpose of different analyses. Timesteps between repetitions (if more than one cycle given) is set to 30 days as SMC is given on a monthly basis during peak season. The target group is also specified here to limit the age group to 0.25-5 year old children. This example dictates that it only applies to children classified as having "low" SMC access per the demographics file and uses adherent drug configurations as previously shown.


```python
from malaria.interventions.malaria_drug_campaigns import add_drug_campaign

def smc_intervention(cb, day, cycles, coverage_level):
    add_drug_campaign(cb, campaign_type='SMC',
                          coverage=coverage_level, start_days=[(sim_years - 1) * 365 + day],
                          ind_property_restrictions=[{'SMCAccess': "Low"}],
                          repetitions=cycles, tsteps_btwn_repetitions=30,
                          adherent_drug_configs=[adherent_drug_configs],
                          target_group={'agemin': 0.25, 'agemax': 5},
                          receiving_drugs_event_name='Received_SMC')
```