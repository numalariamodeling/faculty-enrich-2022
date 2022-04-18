---
title: "EMOD How Tos"
author: "Anne Stahlfeld & Tobias Holden"
date: "4/18/2022"
output: 
  blogdown::html_page:
    toc: true
---

{{< toc >}}

### Create a demographics file

The demographics file specifies characteristics of the population in a simulation. This includes things like the population size, distribution of age/gender, immunity, biting/mortality risks, and more.

At least one demographics file is required for every simulation unless you set the parameter **Enable_Demographics_Builtin** to 1 (one) in the configuration file. This setting does not represent a real location and is generally only used for testing and validating code.

#### Create CSV

Create a file "my_node.csv" with the following columns:

**Required**

- nodeid (unique # 1-n, for n nodes)
- population (starting population size)

*Optional*

- Village (clearer labels than nodeID)
- Other node-specific variables (if any)

Example "my_node.csv":

| nodeid      | population  |  Village |
| ----------- | ----------- | -------- |
| 1      | 1400       | "Obom" |


#### Run generate_demographics()


```python
import json
from dtk.tools.demographics.DemographicsGeneratorConcern import WorldBankBirthRateConcern, EquilibriumAgeDistributionConcern, DefaultIndividualAttributesConcern
from dtk.tools.demographics.DemographicsGenerator import DemographicsGenerator

##### Example for Ghana #####
#############################


def generate_demographics(demo_df, demo_fname, days_spread=1) :
  # Get WorldBank birth rate estimate
  # UPDATE country and birthrate_year
  br_concern = WorldBankBirthRateConcern(country="Ghana", birthrate_year=2016)

  chain = [
        DefaultIndividualAttributesConcern(),
        br_concern,
        EquilibriumAgeDistributionConcern(default_birth_rate=br_concern.default_birth_rate),
    ]

    current: Dict[str, 
                  Union[List[Dict[str,Union[Union[int, Dict[Any, Any]], Any]]], 
                        Dict[Any, Any], 
                        Dict[str, Union[str, int]]]] = DemographicsGenerator.from_dataframe(demo_df,
                                                   population_column_name='population',
                                                   nodeid_column_name='nodeid',
                                                   node_id_from_lat_long=False,
                                                   concerns=chain,
                                                   load_other_columns_as_attributes=True,
                                                   include_columns=['Village'])  # Add any "optional" columns

    with open(demo_fname, 'w') as fout :
      json.dump(current, fout, sort_keys=True,indent=4, separators=(',', ': '))

    
    
df = pd.read_csv(".../my_node.csv")    
demo_fname = ".../FILENAME_demographics.json"

generate_demographics(df, demo_fname)
```

#### Reading a Demographics File

The resulting demographics file is a JSON file organized into 4 main sections:

1. Metadata
2. NodeProperties
3. Defaults
    - Parameters applied to all nodes in the simulation
4. Nodes
    - Allows node-specific parameters
    - Duplicated parameters override values in 'Defaults'


```python
# Structure of Demographics File for a simulation with 1 node
  {
     "Metadata": {
          "DateCreated": "dateTime",
          "Tool": "scriptUsedToGenerate",
          "Author": "author",
          "IdReference": "Gridded world grump2.5arcmin",
          "NodeCount": "1"
     },
     "NodeProperties": [
          {...}
     ],
     "Defaults": {
          "NodeAttributes": {
            ...
            "BirthRateSource": "World Bank",
            "CountryBirthRate": 31.047,
            "World Bank Year": "2016",
            ...
          },
          "IndividualAttributes": {...},
          "IndividualProperties": {...}
     },
     "Nodes": [{
          "NodeID": 1,
          "NodeAttributes": {
            "BirthRate": 0.1190,
            "InitialPopulation": 1400,
            "Village": "Obom"
          },
          "IndividualAttributes": {...},
          "IndividualProperties": {...}
     }]
  }
```

### Create multi-node simulations

#### Generate demographics

To run simultaneous simulations in multiple nodes, create an input file "my_nodes.csv" with one row for each node.

Ex. "my_nodes.csv"

| nodeid | population | Village |
|--------|------------|---------|
| 1      | 1400       |"Obom"|
| 2      | 2255       |"Kofi Kwei"|
| 3      | 1800       |"Village 3"|

Then, run 


```python
generate_demographics(df="/my_nodes.csv")
```

#### Node-specific parameters

Sometimes we want to vary properties between nodes based on prior knowledge. Imagine we know the proportion of "high-risk" individuals in each node and want to use this designation to target them for an intervention.

First, we would add a column to our input file representing the high-risk proportion in each node.

| nodeid | population | Village | high_risk |
|--------|------------|---------|-----------|
| 1 | 1400 | "Obom" | 0.05|
| 2 | 2255 | "Kofi Kwei" | 0.10|
| 3 | 1800 | "Village 3" | 0.50|

Then, when we can assign the "high_risk" property to individuals in each node with the probability listed in the table, by adding the following code to the end of the generate_demographics() function definition, before writing the .json file.


```python
...
for n in list(range(current['Metadata']['NodeCount'])):
   current['Nodes'][n]['IndividualProperties'] = [{"Property":"high_risk",
   "Values": ['yes','no'],
   "Initial_Distribution": [high_risk, (1-high_risk)]}]
   

with open(demo_fname, 'w') as fout :
  json.dump(current, fout, sort_keys=True,indent=4, separators=(',', ': '))
```

We can see this reflected in the demographics file:


```python
{
     "Metadata": {
          "DateCreated": "dateTime",
          "Tool": "scriptUsedToGenerate",
          "Author": "author",
          "IdReference": "Gridded world grump2.5arcmin",
          "NodeCount": "1"
     },
     "NodeProperties": [
          {...}
     ],
     "Defaults": {
         ...
     },
     "Nodes": [{
          "NodeID": 1,
          "NodeAttributes": {
            "BirthRate": 0.1190,
            "InitialPopulation": 1400,
            "Village": "Obom"
          },
          "IndividualAttributes": {...},
          "IndividualProperties": [
                {
                    "Initial_Distribution": [
                        0.05,
                        0.95
                    ],
                    "Property": "high_risk",
                    "Values": [
                        "yes",
                        "no"
                    ]
                }]},
                
     {
          "NodeID": 2,
          "NodeAttributes": {
            "BirthRate": 0.1690,
            "InitialPopulation": 2255,
            "Village": "Kofi Kwei"
          },
          "IndividualAttributes": {...},
          "IndividualProperties": [
                {
                    "Initial_Distribution": [
                        0.10,
                        0.90
                    ],
                    "Property": "high_risk",
                    "Values": [
                        "yes",
                        "no"
                    ]
                }]},
     {
          "NodeID": 3,
          "NodeAttributes": {
            "BirthRate": 0.2190,
            "InitialPopulation": 1800,
            "Village": "Village 3"
          },
          "IndividualAttributes": {...},
          "IndividualProperties": [
                {
                    "Initial_Distribution": [
                        0.50,
                        0.50
                    ],
                    "Property": "high_risk",
                    "Values": [
                        "yes",
                        "no"
                    ]
                }]}
  }
```


### Set up migration between nodes

#### Vector Migration

#### Human Migration

### Set the number of seeds

You will need to set the number of seeds to tell EMOD how many simulations you want to run. You can use this later to set a "Run_Number" parameter in the model builder setup that will force EMOD to iterate through the range of values to reach the length of numseeds, in this case 0-9.


```python
numseeds = 10 
```

### Update config parameters

You may need to update a variety of configuration parameters for your simulations. These parameters can be explored more in depth in the [EMOD config documentation](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration.html). Broadly, configuration parameters can be used to set up certain things in these categories: drugs and treatments, enable/disable features, general disease, geography and the environment, immunity, incubation, infectivity and transmission, input files, larval habitat, migration, mortality and survival, output settings, parasite dynamics, population dynamics, sampling, scalars and multipliers, simulation setup, symptoms and diagnosis, vector control, and vector life cycle. Generally, we create a setup_simulation() function that contains the configuration update function for the config_builder (cb). For parameters that won't often change you can hard code them directly into this function, while it may be beneficial to call others as a variable, such as sim_years, that can be set when the function itself is called later. This can be done inline in the code or within the model builder. 

A number of key parameters are featured in the code block below. Of particular note are how to change the **demographics** (Demographics_Filenames) and **simulation duration** (Simulation_Duration).


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder

def setup_simulation(cb, sim_years=2):
    cb.update_params({'Demographics_Filenames': ['my_demographics.json'],
                      'Vector_Species_Names': [],
                      'Enable_Vital_Dynamics': 1,
                      'Enable_Birth': 1,
                      'Disable_IP_Whitelist': 1,
                      'Simulation_Duration': sim_years*365,
                      'Maternal_Antibodies_Type': 'CONSTANT_INITIAL_IMMUNITY',
                      'Incubation_Period_Distribution': 'CONSTANT_DISTRIBUTION'd,
                      'Birth_Rate_Dependence': 'FIXED_BIRTH_RATE',
                      'Climate_Model': 'CLIMATE_CONSTANT',
                      "Parasite_Smear_Sensitivity": 0.02,
                      'logLevel_JsonConfigurable': 'ERROR',
                      })
                      
cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
setup_simulation(cb,7)
```

#### Enable births and deaths

Vital dynamics can be specified in the same way as general config parameters. They can be included within the same setup_simulation() function but are separated here for ease of access. In order to enable either births or deaths, **Enable_Vital_Dynamics** must be set to 1. Births and deaths can then be modified as needed ("enable" parameters set to 0 = false, 1 = true). Birth rates can be specified by **Birth_Rate_Dependence** to be dependent on a number of factors:  
- "NONE"
- "FIXED_BIRTH_RATE"
- "POPULATION_DEP_RATE"
- "DEMOGRAPHIC_DEP_RATE"
- "INDIVIDUAL_PREGNANCIES"
- "INDIVIDUAL_PREGNANCIES_BY_AGE_AND_YEAR"

Likewise, **Death_Rate_Dependence** determines individuals likelihood of dying from natural, non-disease causes when **Enable_Natural_Mortality**=1, and can be set to 
- "NOT_INITIALIZED" 
- "NONDISEASE_MORTALITY_BY_AGE_AND_GENDER"
- "NONDISEASE_MORTALITY_BY_YEAR_AND_AGE_FOR_EACH_GENDER"

Detailed descriptions of dependencies can be found [here](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration-population.html).


```python
def setup_simulation(cb, sim_years=2):
    cb.update_params({'Enable_Vital_Dynamics': 1,
                      'Enable_Birth': 1,
                      'Birth_Rate_Dependence' : 'FIXED_BIRTH_RATE'
                      'Enable_Disease_Mortality': 1,
                      'Enable_Natural_Mortality': 1,
                      'Death_Rate_Dependence':'NOT_INITIALIZED',
                      'Base_Mortality': 0.002
                      })
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
                          ind_property_restrictions=[{'Property_name': "Value"}],
                          repetitions=cycles, tsteps_btwn_repetitions=30,
                          adherent_drug_configs=[adherent_drug_configs],
                          target_group={'agemin': 0.25, 'agemax': 5},
                          receiving_drugs_event_name='Received_SMC')
```

### Setting up the experiment manager

The experiment manager serves as a mechanism to actually run simulations using the model and config builders. The respective builders, "builder" and "cb", are set up earlier in this list.


```python
from simtools.SetupParser import SetupParser
from simtools.ExperimentManager.ExperimentManagerFactory import ExperimentManagerFactory

Exp_name = 'my_experiment_name'

run_sim_args = {
    'exp_name': Exp_name,
    'config_builder': cb,
    'exp_builder' : builder
}

if __name__ == "__main__":
    SetupParser.init()
    exp_manager = ExperimentManagerFactory.init()
    exp_manager.run_simulations(**run_sim_args)
```

