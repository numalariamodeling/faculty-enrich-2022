---
title: "EMOD How To's"
author: "Anne Stahlfeld & Tobias Holden"
date: "4/18/2022"
output: 
  blogdown::html_page:
    toc: true
---


![figure](/images/07_full_process.png)

{{< toc >}}

## Create a demographics file 

![figure](/images/01_highlighted.png)

The demographics file specifies characteristics of the population in a simulation. This includes things like the population size, distribution of age/gender, immunity, biting/mortality risks, and more.

At least one demographics file is required for every simulation unless you set the parameter **Enable_Demographics_Builtin** to 1 (one) in the configuration file. This setting does not represent a real location and is generally only used for testing and validating code.

<details><summary><span style="color: blue";">1. Create CSV</span></summary>
<p>

Create a file "my_node.csv" with the following columns:

**Required**

- nodeid (unique # 1-n, for n nodes)
- population (starting population size)

*Optional*

- Village (clearer labels than nodeID)
- Other node-specific variables (if any)

Example "my_node.csv":

| nodeid      | population  |  Village | lat     |  lon    |
| ----------- | ----------- | -------- |-------- |-------- |
| 1           | 1400        | "Obom"   | 5.760759  | -0.4473415  |

</p>
</details>

<details><summary><span style="color: blue";">2. Run generate_demographics()</span></summary>
<p>


```python
import os
import pandas as pd
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

    
    
df = pd.read_csv(os.path.join(<PATHTOFILE>,"my_node.csv"))  # Modify PATHTOFILE
demo_fname = os.path.join(<INPUTPATH>,"FILENAME_demographics.json") # Modify INPUTPATH and FILENAME

generate_demographics(df, demo_fname)
```

</p>
</details>

<details><summary><span style="color: blue";">3. Reading your Demographics File</span></summary>
<p>

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

</p>
</details>

## Create multi-node simulations

![figure](/images/01_highlighted.png)

<details><summary><span style="color: blue;">1. Generating demographics</span></summary>
<p>

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

</p>
</details>

<details><summary><span style="color: blue;">2. Setting Node-Specific Inputs</span></summary>
<p>

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

</p>
</details>

## Set up migration between nodes

Multi-node simulations allow for the possibility that humans or vectors will move between nodes.
EMOD allows 6 migration types for humans (Local, Regional, Sea, Air, Family, and campaign) and two 
for vectors (Local and Regional). Other than the campaign type, all other migration types are set via 
input files and scaling factors in config.json, and these migration rates will remain the same throughout
the simulation: we will call these "ongoing migration" to distinguish from the "forced migration" that is 
set via campaigns.

Multiple migration modes can be used for each agent type (human or vector) simultaneously. For example, we 
can set up a simulation using Local, Sea, and campaign migration for humans and Local and Regional migration 
for vectors.

### Ongoing Migration

![figure](/images/01_highlighted.png)

See [EMOD documentation on migration parameters](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration-migration.html) 
for full parameter list and specifications.

In ongoing migration, heterogeneity in migration can be specified by setting *Enable_Migration_Heterogeneity* to 1 in
config.json and setting migration rate distribution parameters in demographics.json. However, this heterogeneity 
is age-independent, and ongoing migration does not allow the user to specify age-dependent migration patterns.

Local, Regional, Sea, and Air migration types are not implemented differently in EMOD. Their names are
for human-readability and interpretation. We often use Local migration to model short-distance, short-duration 
trips and Regional to model longer-distance, longer-duration trips, but this is not required.

To set up ongoing migration:

1. Create CSV
2. Convert to BIN
3. Update Configuration Parameters

<details><summary><span style="color: blue;">Human Migration example: Local migration</span></summary>
<p>

To specify migration rates, create a file "local_migration.csv" with columns specifying the origin node, destination 
node, and migration rate (1/trip duration). In the example below, people from Node 1 visit node 2 for 5 days (or vice 
versa), and people from Node 3 visit node 2 for 3 days (or vice versa). There is no local human movement between between 
Node 1 and Node 3.

|      |      |    |
|------|------|----|
|Node 1|Node 2| 0.2|
|Node 2|Node 1| 0.2|
|Node 2|Node 3| 0.33|
|Node 3|Node 2| 0.33|

To convert the .csv to a .bin file EMOD can understand, we also require the demographics file:


```python
demo_fname = "inputs_path/my_demographics_file.json"
with open(demo_fname) as fin:
    demo = json.loads(fin.read())
id_reference = demo['Metadata']['IdReference']

convert_txt_to_bin('local_migration.csv',
                   'local_migration.bin',
                    id_reference=id_reference)
```

To connect that migration file a simulation, we need to change some configuration parameters. 


```python
cb.update_params({
        # Migration
        'Migration_Model': 'FIXED_RATE_MIGRATION', # turn on human migration
        'Migration_Pattern': 'SINGLE_ROUND_TRIPS', # human trips are round trips (see documentation for other options)

        'Enable_Local_Migration': 1,               # turn on Local human migration
        'Local_Migration_Roundtrip_Duration': 5,   # Local trips last 5 days
        'Local_Migration_Roundtrip_Probability': 1,# traveler returns home in 100% of Local trips 
        'x_Local_Migration': 0.02,                 # Scale factor used to fix the average # of trips per person, per year.
        'Local_Migration_Filename': 'local_migration.bin' # path to migration file
    })
```

Setting up Regional, Sea, and Air migration is analogous, using Regional, Sea, and Air parameters and creating a 
corresponding .bin file.

</p>
</details>

<details><summary><span style="color: blue;">Vector Migration: Local example</span></summary>
<p>

The setup for vector movement between nodes is very similar to that for humans, but with different parameters. 
The table below is an example of a .csv file that can be used to generate a "vector_migration_local.bin" 
specifying equivalent vector migration between 2 adjacent nodes.

|      |      |      |
|------|------|------|
|Node 1|Node 2| 1.0  |
|Node 2|Node 1| 1.0  |

With the following updates to configuration parameters:


```python
cb.update_params({
        'Enable_Vector_Migration': 1,
        'Enable_Vector_Migration_Local': 1,        
        'x_Vector_Migration_Local': 0.01,  # scale factor to fix average # of trips per vector, per day
        'Vector_Migration_Filename_Local': 'Vector_Local_Migration.bin',
        
        ### Modifying Equation parameters
        #   These must be specified, even if not used. 
        #   The default (no modification) parameters are below
        'Vector_Migration_Modifier_Equation': 'LINEAR',
        'Vector_Migration_Food_Modifier': 0, 
        'Vector_Migration_Habitat_Modifier': 0,
        'Vector_Migration_Stay_Put_Modifier': 0
    })
```

The Modifier parameters allow the user to force vectors to fly preferentially toward blood meals, toward brreding sites, 
or stay in their current location.

</p>
</details>

<p>
</p>

### Forced Migration

![figure](/images/04_highlighted.png)

You may want to incorporate migration that is different from the normal migration patterns described above: for example, 
to specify a certain demographic group to move at a certain time of year.

<details><summary><span style="color: blue;">Forcing a Single Migration Event</span></summary>
<p>

Make sure the configuration parameters are set to allow migration:


```python
cb.update_params({
        # Migration
        'Migration_Model': 'FIXED_RATE_MIGRATION',   
        'Migration_Pattern': 'SINGLE_ROUND_TRIPS'
  })
```

To add the migration events to the simulated campaign use add_migration_event(). 


```python
from dtk.interventions.migrate_to import add_migration_event

# How long after the "start" date should people wait to begin the trip? 
# Here: Trips are staggered evenly over 7 days.
duration_before_leaving = {"Duration_Before_Leaving_Distribution": "UNIFORM_DISTRIBUTION",
                           "Duration_Before_Leaving_Min": 1,
                           "Duration_Before_Leaving_Max": 7}

# How long should the trip last? 
# Here: Each trip lasts exactly 30 days.
duration_at_node = {"Duration_At_Node_Distribution": "CONSTANT_DISTRIBUTION",
                    "Duration_At_Node_Constant": 30}

add_migration_event(cb,
                    start_day=100,    # simulation day on which to start this migration
                    nodesfrom=[2],    # list of node IDs for origin(s)
                    nodeto=1,         # destination node
                    coverage=0.6,     # probability a targeted individual will migrate
                    duration_before_leaving=duration_before_leaving   # time to remain home before trip,
                    duration_at_node=duration_at_node,                # time to spend at destination node,
                    repetitions=1     # For a single event, set repetitions=1
                    )
```

More detail on specifying distributions for waiting/away times can be found in the [EMOD documentation - here](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-campaign-individual-migrateindividuals.html).

</p>
</details>

<details><summary><span style="color: blue;">Simulating Periodic Migration</span></summary>
<p>

For routine or seasonal migration events that repeat during a simulation, specify the number of repetitions and 
time-interval within add_migration_event():


```python
add_migration_event(cb,
                    ...
                    repititions=4,
                    tsteps_btwn=365 # annual event
                    )
```

</p>
</details>

<details><summary><span style="color: blue;">Simulating Permanent Moves</span></summary>
<p>

EMOD also has a few other parameters built-in to the migrate_individuals campaign class:

- *DontAllowDuplicates* (default is False)
    - TRUE = While waiting to leave or during a trip, another migration event can't be initialized for a given individual. 
- *IsMoving* (default is False)
    - TRUE = a migration event changes individuals "home node" that they are considered a resident of (for other node-based interventions) **nodefrom** --> **nodeto**, even if they are on a short round-trip.

<span style="color: red;">**Note:**</span> the *IsMoving* parameter doesn't have anything to do with trip duration or a permanent relocation. These migration events are all round-trips. If you want to simulate a permanent move:

- set duration_at_node in add_migration_event() to a length of time that extends past the end of your simulation.
- Make sure *DontAllowDuplicates* in MigrateIndividuals() is FALSE if you want them to be eligible for future migration events after they change residence. 
- Set *IsMoving* in MigrateIndividuals() to True.

</p>
</details>

<p>
</p>

### Monitoring migration

![figure](/images/03_highlighted.png)

<details><summary><span style="color: blue;">Tracking and counting human migrations</span></summary>
<p>

The [ReportHumanMigrationTracking](https://docs.idmod.org/projects/emod-malaria/en/latest/software-report-human-migration.html) 
custom report is available to report all human migration events, including time of migration, individual ID, origin node 
ID, destination node ID, individual's home node ID, age, infection status, and the type of migration. This report is 
very useful for counting the number of migration events to ensure the desired flux of movement is correctly specified.


```python
from dtk.utils.reports.CustomReport import add_human_migration_tracking_report

add_human_migration_tracking_report(cb)
```

</p>
</details>

<details><summary><span style="color: blue;">Tracking vector migrations</span></summary>
<p>

The [ReportVectorMigration](https://docs.idmod.org/projects/emod-malaria/en/latest/software-report-vector-migration.html) 
custom report is available to report on vector migration. Note that while EMOD can model vector migration using either 
individual vectors or the vector cohort model, the ReportVectorMigration only outputs interpretable values when using the 
individual vector model. Modeling migration with cohort or individual vectors is equivalent, so we sometimes debug and 
verify migration using the individual model (**TRACK_ALL_VECTORS**), then go back to using the (faster) cohort model 
(**VECTOR_COMPARTMENTS_NUMBER**).


```python
from dtk.utils.reports.VectorReport import add_vector_migration_report

cb.update_params({'Vector_Sampling_Type': 'TRACK_ALL_VECTORS',   # tell EMOD to use individual vectors
                  })

add_vector_migration_report(cb)
```

</p>
</details>



## Create climate files

![figure](/images/01_highlighted.png)

Once we have generated a demographics file describing the nodes for a simulation, you can also construct climate files 


```python
from dtk.tools.climate.ClimateGenerator import ClimateGenerator

def generate_climate(demo_fname, input_file_name) :

    if not SetupParser.initialized:
        SetupParser.init('HPC')

    cg = ClimateGenerator(demographics_file_path=demo_fname, work_order_path='./wo.json',
                          climate_files_output_path=os.path.join(inputs_path, input_file_name),
                          climate_project='IDM-<COUNTRY>', # Modify COUNTRY
                          start_year='<YEAR>', num_years='1') # Modify YEAR
    cg.generate_climate_files()
    
# Point to existing demographics file    
inputs_path = os.path.join(<PROJECTPATH>, 'simulation_inputs') # Modify PROJECTPATH
input_file_name = 'FILENAME' # Modify FILENAME
demo_fname = os.path.join(inputs_path, 'demographics', '%s_demographics.json' % input_file_name)

# Generate climate files from selected project
generate_climate(demo_fname, input_file_name)

# Rename climate files and metadata
for tag in ['air_temperature', 'rainfall', 'relative_humidity'] :
            os.replace(os.path.join(inputs_path, input_file_name,'Burkina Faso_30arcsec_%s_daily.bin' % tag),
                       os.path.join(inputs_path, 'climate', '%s_%s_daily.bin' % (input_file_name, tag)))
            os.replace(os.path.join(inputs_path, input_file_name, 'Burkina Faso_30arcsec_%s_daily.bin.json' % tag),
                       os.path.join(inputs_path, 'climate', '%s_%s_daily.bin.json' % (input_file_name, tag)))
```

After completing these steps, there should be climate files for air_temperature, rainfall, and relative_humidity in your inputs folder. To reference these when running a simulation, update the configuration parameters:


```python
cb.update_params({"Air_Temperature_Filename": os.path.join('climate', '%s_air_temperature_daily.bin' % input_file_name),
                  "Land_Temperature_Filename": os.path.join('climate', '%s_air_temperature_daily.bin' % input_file_name),
                  "Rainfall_Filename": os.path.join('climate', '%s_rainfall_daily.bin' % input_file_name),
                  "Relative_Humidity_Filename": os.path.join('climate', '%s_relative_humidity_daily.bin' % input_file_name)
                  })
```

## Create a model

![figure](/images/02_highlighted.png)

EMOD configuration scripts begin by creating a configbuilder object (the **cb**), then adding and changing it. The *cb* is 
created by loading default parameters:


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from simtools.ModBuilder import ModBuilder, ModFn

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
```

One can also specify different simulation types such as **VECTOR_SIM** to simulate just the vector model without the 
malaria within-host model, or other simulation types listed [here](https://docs.idmod.org/projects/emod-malaria/en/latest/glossary.html?highlight=Sim_Type#term-simulation-type).


## Set up mosquito species

![figure](/images/02_highlighted.png)

EMOD allows us to specify the distribution of mosquito species in the simulation, and to specify life cycle, larval habitat, and transmission parameters for each species. 

<details><summary><span style="color: blue";">Single Vector Species</span></summary>
<p>

The example below would populate the model with 100% gambiae mosquitoes.


```python
from dtk.vector.species import set_species
set_species(cb, ['gambiae'])
```

The following default parameters appear in the config file for A. gambiae. Some of the default parameters vary between different vector species:


```python
{
    "Vector_Species_Params": [{

    "Name": "gambiae",
    "Larval_Habitat_Types": {
        "TEMPORARY_RAINFALL": 8e8,
        "CONSTANT": 8e7
    },
    "Aquatic_Arrhenius_1": 84200000000,
    "Aquatic_Arrhenius_2": 8328,
    "Aquatic_Mortality_Rate": 0.1,
    "Immature_Duration": 2,
    "Male_Life_Expectancy": 10,
    "Adult_Life_Expectancy": 20,
    "Days_Between_Feeds": 3,
    "Anthropophily": 0.85,  # species- and site-specific feeding parameters
    "Indoor_Feeding_Fraction": 0.95,
    "Egg_Batch_Size": 100,
    "Vector_Sugar_Feeding_Frequency": "VECTOR_SUGAR_FEEDING_NONE",
    "Acquire_Modifier": 0.2,
    # VECTOR_SIM uses a factor here for human-to-mosquito infectiousness, while 
    # MALARIA_SIM explicitly models gametocytes
    "Infected_Arrhenius_1": 117000000000,
    "Infected_Arrhenius_2": 8336,
    "Infected_Egg_Batch_Factor": 0.8,
    "Infectious_Human_Feed_Mortality_Factor": 1.5,
    "Nighttime_Feeding_Fraction": 1,
    "Transmission_Rate": 0.9  # Based on late-2013 calibration of PfPR vs EIR favoring 1.0 to 0.5
    }]
}
```

</p> </details>

<details><summary><span style="color: blue";">Multiple Vector Species</span></summary>
<p>

We can also include a mix of vector species, adding multiple vector populations with species-specific parameters.


```python
from dtk.vector.species import set_species
set_species(cb, ['gambiae','arabiensis'])
```

For each species listed in Vector_Species_Params, a “VectorPopulation” object will be added to the simulation at each node. Each species will be defined by parameters in the simulation configuration file for the vector ecology and behavior of the species. This allows for a mechanistic description of vector abundances and behavior through the effects of climate and weather on different preferred larval habitats.


```python
{
    "Vector_Species_Params": [{

    "Name": "gambiae",
    "Larval_Habitat_Types": {
        "TEMPORARY_RAINFALL": 8e8,
        "CONSTANT": 8e7
    },
    "Aquatic_Arrhenius_1": 84200000000,
    "Aquatic_Arrhenius_2": 8328,
    "Aquatic_Mortality_Rate": 0.1,
    "Immature_Duration": 2,
    "Male_Life_Expectancy": 10,
    "Adult_Life_Expectancy": 20,
    "Days_Between_Feeds": 3,
    "Anthropophily": 0.85,  # species- and site-specific feeding parameters
    "Indoor_Feeding_Fraction": 0.95,
    "Egg_Batch_Size": 100,
    "Vector_Sugar_Feeding_Frequency": "VECTOR_SUGAR_FEEDING_NONE",
    "Acquire_Modifier": 0.2,
    # VECTOR_SIM uses a factor here for human-to-mosquito infectiousness, while 
    # MALARIA_SIM explicitly models gametocytes
    "Infected_Arrhenius_1": 117000000000,
    "Infected_Arrhenius_2": 8336,
    "Infected_Egg_Batch_Factor": 0.8,
    "Infectious_Human_Feed_Mortality_Factor": 1.5,
    "Nighttime_Feeding_Fraction": 1,
    "Transmission_Rate": 0.9  # Based on late-2013 calibration of PfPR vs EIR favoring 1.0 to 0.5
    },
    {

    "Name": "arabiensis",
    "Larval_Habitat_Types": {
        "TEMPORARY_RAINFALL": 8e8,
        "CONSTANT": 8e7
    },
    "Aquatic_Arrhenius_1": 84200000000,
    "Aquatic_Arrhenius_2": 8328,
    "Aquatic_Mortality_Rate": 0.1,
    "Immature_Duration": 2,
    "Male_Life_Expectancy": 10,
    "Adult_Life_Expectancy": 20,
    "Days_Between_Feeds": 3,
    "Anthropophily": 0.85,  # species- and site-specific feeding parameters
    "Indoor_Feeding_Fraction": 0.5,
    "Egg_Batch_Size": 100,
    "Vector_Sugar_Feeding_Frequency": "VECTOR_SUGAR_FEEDING_NONE",
    "Acquire_Modifier": 0.2,
    # VECTOR_SIM uses a factor here for human-to-mosquito infectiousness, while 
    # MALARIA_SIM explicitly models gametocytes
    "Infected_Arrhenius_1": 117000000000,
    "Infected_Arrhenius_2": 8336,
    "Infected_Egg_Batch_Factor": 0.8,
    "Infectious_Human_Feed_Mortality_Factor": 1.5,
    "Nighttime_Feeding_Fraction": 1,
    "Transmission_Rate": 0.9  # Based on late-2013 calibration of PfPR vs EIR favoring 1.0 to 0.5
    }]
}
```


</p> </details>

<details><summary><span style="color: blue";">Modify vector species parameters</span></summary>
<p>

To change vector species parameters from defaults, use the update_species_param() function.


```python
from dtk.vector.species import update_species_param

# Example: Decrease the 'Transmission_Rate' of A. arabiensis from 0.9 (default) to 0.75.
update_species_param(cb, 
                     species="arabiensis", 
                     parameter="Transmission_Rate", 
                     value=0.75, 
                     overwrite=False # If True, replaces any previous stored values
                     )
```

</p> </details>

<details><summary><span style="color: blue";">Modify species habitat parameters</span></summary>
<p>

The larval habitat parameters for each vector species can also be modified.


```python
from dtk.vector.species import update_species_param

# Example: Add brackish swamp habitat availability for A. arabiensis only. 

new_habitats = {"arabiensis": {"BRACKISH_SWAMP": 1.7e9, "Max_Larval_Capacity": 30000000.0}}

for species, habitat in new_habitats.items():
    update_species_param(cb, species,
                         parameter="Larval_Habitat_Types", 
                         value= habitat, 
                         overwrite=False # Does not delete previous habitat types
                         )
```

</p> </details>

## Update config parameters

![figure](/images/02_highlighted.png)

You may need to update a variety of configuration parameters for your simulations. These parameters can be explored more 
in depth in the [EMOD config documentation](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration.html). 
Broadly, configuration parameters can be used to set up certain things in these categories: drugs and treatments, 
enable/disable features, general disease, geography and the environment, immunity, incubation, infectivity and 
transmission, input files, larval habitat, migration, mortality and survival, output settings, parasite dynamics, 
population dynamics, sampling, scalars and multipliers, simulation setup, symptoms and diagnosis, vector control, and 
vector life cycle. Generally, we create a setup_simulation() function that contains the configuration update function 
for the config_builder (cb). For parameters that won't often change you can hard code them directly into this function, 
while it may be beneficial to call others as a variable, such as sim_years, that can be set when the function itself is 
called later. This can be done inline in the code or within the model builder. 

In this example, we show how to change the **demographics** (Demographics_Filenames) and **simulation duration** 
(Simulation_Duration) parameters. Simulation duration is set in days, and in this example is set to last 7 years 
(7 yrs * 365 days/yr).


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
                      
cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
cb.update_params({'Demographics_Filenames': ['my_demographics.json'],
                  'Simulation_Duration': 7*365,
                  })
```

<details><summary><span style="color: blue";">Enable Births and Deaths</span></summary>
<p>

Vital dynamics can be specified in the same way as general config parameters. To enable either births or deaths, 
**Enable_Vital_Dynamics** must be set to 1. Births and deaths can then be modified as needed ("enable" parameters set 
to 0 = false, 1 = true). Birth rates can be specified by **Birth_Rate_Dependence** to be dependent on a number of 
factors:  
- "NONE"
- "FIXED_BIRTH_RATE"
- "POPULATION_DEP_RATE"
- "DEMOGRAPHIC_DEP_RATE"
- "INDIVIDUAL_PREGNANCIES"
- "INDIVIDUAL_PREGNANCIES_BY_AGE_AND_YEAR"

Likewise, **Death_Rate_Dependence** determines individuals likelihood of dying from natural, non-disease causes 
when **Enable_Natural_Mortality**=1, and can be set to 
- "NOT_INITIALIZED" 
- "NONDISEASE_MORTALITY_BY_AGE_AND_GENDER"
- "NONDISEASE_MORTALITY_BY_YEAR_AND_AGE_FOR_EACH_GENDER"

Detailed descriptions of dependencies can be found [here](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-configuration-population.html).

In this example, we have a fixed birth rate (number of infants born each year is independent of modeled population), age- 
and gender-specific overall mortality rates (defined in demographics file), and no malaria mortality.


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
                      
cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
cb.update_params({'Enable_Vital_Dynamics': 1,
                  'Enable_Birth': 1,
                  'Birth_Rate_Dependence' : 'FIXED_BIRTH_RATE'
                  'Enable_Natural_Mortality': 1,
                  'Death_Rate_Dependence': 'NONDISEASE_MORTALITY_BY_AGE_AND_GENDER',
                  'Enable_Disease_Mortality': 0,
                  })
```
</p>
</details>

<details><summary><span style="color: blue";">Configure Log-Levels</span></summary>
<p>

Log-levels dictate which logging messages will be included in the standard output for simulations. It can be set for all 
files in the Eradication executable using **logLevel_default** or for specific files where more information is desired 
with **logLevel_<file_name>**. There are five logging levels:

    1. ERROR: only errors logged
    2. WARNING: warning and errors logged
    3. INFO: default; informational messages, warnings, and errors logged
    4. DEBUG: debug information, informational messages, warnings, and errors logged. May require a special version of the executable (--TestSugar enabled)
    5. VALID: validation information, debug information, informational messages, warnings, and errors logged. Requires special build.

Lower levels (DEBUG and VALID) should be used sparingly but can be useful to understand if certain things, such as 
immunity variation, are working as expected.

In this example, we set the default reporting level of all modules to **WARNING**, then set the 
logLevel_SusceptibilityMalaria module to **DEBUG**.

```python
cb.update_params({'logLevel_default' : 'WARNING',
                  'logLevel_SusceptibilityMalaria': 'DEBUG' #file-specific example
})
```
</p>
</details>

## Add summary reports

![figure](/images/03_highlighted.png)

The [MalariaSummaryReport](https://docs.idmod.org/projects/emod-malaria/en/latest/software-report-malaria-summary.html) 
is a useful output that reports infection data (prevalence, clinical incidence, parasitemia, infectivity) by age group 
and aggregated over a user-defined time interval such as years or months.

In this example, simulation data is reported starting at day 365, with a monthly aggregation, in 3 age bins, and only for 
individuals with the IndividualProperty of SMCAccess with value High.


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from malaria.reports.MalariaReport import add_summary_report

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

add_summary_report(cb, start=365, interval=30,
                   age_bins=[0.25,5,100], ipfilter='SMCAccess:High',
                   description='Monthly_HighAccess')
```


## Set forced EIR 

![figure](/images/04_highlighted.png)

For simulations without mosquitoes, a forced EIR can be used to impose infectious bites. EIR timeseries data are 
typically recreated from previous literature sources that provide monthly EIR levels, input here as a monthly_site_EIR 
list. This can then be converted to a daily EIR using the `monthly_to_daily_EIR` helper function and summed to calculate 
the annual EIR for the site. The [add_InputEIR](https://github.com/InstituteforDiseaseModeling/dtk-tools/blob/master/dtk/interventions/input_EIR.py)
 function is called and given the calculated daily EIR to apply to the simulations. It can be scaled using a 
 scaling_factor to apply the same scale factor to all EIR timepoints.


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from dtk.interventions.input_EIR import add_InputEIR, monthly_to_daily_EIR

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

monthly_site_EIR = [15.99, 5.41, 2.23, 10.33, 7.44, 11.77, 79.40, 85.80, 118.59, 82.97, 46.62, 33.49]
daily_EIR = monthly_to_daily_EIR(monthly_site_EIR)
EIRscale_factor = 1

add_InputEIR(cb, start_day=0, EIR_type='DAILY', dailyEIRs=daily_EIR, scaling_factor=EIRscale_factor)
```

## Add larvicides

![figure](/images/04_highlighted.png)

Functions for adding attractive targeted sugar baits, topical repellents, outdoor residual spray, eave tubes, and 
larvicides are found [here](https://github.com/InstituteforDiseaseModeling/dtk-tools/blob/master/dtk/vector/species.py#L375).

To add larvicides: 


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from dtk.interventions.novel_vector_control import add_larvicides

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

add_larvicides(cb, start_day=0, 
               habitat_target='CONSTANT',   # habitat type to target
               killing_initial=0.6,         # initial killing efficacy of the insecticide
               killing_decay=150            # decay time constant, in days, for exponential decay of larvicide efficacy
               )
```


## Add insecticide resistance

![figure](/images/04_highlighted.png)

Modeling insecticide resistance involves 3 steps:
1. Set up vector genetics
2. Relate genotypes to phenotype: define insecticide response of resistance genotypes
3. Add intervention that distributes insecticide (vector control)

**Setting up vector genetics**


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from dtk.vector.species import set_species_param, set_species

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

set_species(cb, ['gambiae'])
set_species_param(cb, 'gambiae', 'Genes', [
    {
        "Alleles": {
            "a0": 0.8,
            "a1": 0.19999999999999996,
            "a2": 0.0
        },
        "Mutations": {
            "a0:a1": 0,
            "a0:a2": 0,
            "a1:a0": 0,
            "a1:a2": 0,
            "a2:a0": 0,
            "a2:a1": 0
        }
    }
])
```

Reporting on vector genetics (abundance of alleles and/or genotypes) is requested like so:


```python
from dtk.utils.reports import BaseVectorGeneticsReport

cb.add_reports(BaseVectorGeneticsReport(type='ReportVectorGenetics',
                                        species='gambiae',
                                        gender='VECTOR_FEMALE',
                                        include_vector_state_columns=0,
                                        stratify_by='ALLELE_FREQ'))
```

**Relating genotype to phenotype**

The `Insecticides` config param is a list of dictionaries, one per insecticide. For each insecticide, genotype-specific 
modifications of killing, blocking, repelling, and larval killing can be set.


```python
cb.update_params({
    "Insecticides": [
        {
            "Name": "pyrethroid",
            "Resistances": [
                {
                    "Allele_Combinations": [
                        [
                            "a1",
                            "a1"
                        ]
                    ],
                    "Blocking_Modifier": 1.0,
                    "Killing_Modifier": 0.05,
                    "Larval_Killing_Modifier": 0,
                    "Repelling_Modifier": 0,
                    "Species": "gambiae"
                }
            ]
        },
        {
            "Name": "carbamate",
            "Resistances": [
                {
                    "Allele_Combinations": [
                        [   # for this specification, any allele in combination with a2 will have these modifiers
                            "a2",
                            "*"
                        ]
                    ],
                    "Blocking_Modifier": 1.0,
                    "Killing_Modifier": 0.25,
                    "Larval_Killing_Modifier": 0,
                    "Repelling_Modifier": 0,
                    "Species": "gambiae"
                }
            ]
        },
        {
            "Name": "pyrethroid-PBO",
            "Resistances": [
                {
                    "Allele_Combinations": [
                        [
                            "a1",
                            "a1"
                        ]
                    ],
                    "Blocking_Modifier": 1.0,
                    "Killing_Modifier": 0.5,
                    "Larval_Killing_Modifier": 0,
                    "Repelling_Modifier": 0,
                    "Species": "gambiae"
                }
            ]
        }
    ]
})
```

**Add vector control***

Specify the insecticide when adding vector control:


```python
add_ITN(cb, start=0, coverage_by_ages=[{'min': 0, 'max': 100, 'coverage': 0.6}],
        insecticide='pyrethroid')
add_ITN(cb, start=100, coverage_by_ages=[{'min': 0, 'max': 100, 'coverage': 0.6}],
        insecticide='pyrethroid-PBO')
add_ITN(cb, start=200, coverage_by_ages=[{'min': 0, 'max': 100, 'coverage': 0.6}],
        insecticide='carbamate')
```

## Add case management

![figure](/images/04_highlighted.png)

Case management is controlled in EMOD by an 
[add_health_seeking()](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/health_seeking.py) 
function within dtk-tools-malaria. This function is a node level intervention that allows you to target individuals on 
the node for malaria treatment through health seeking behavior. 

In this example, treatment is triggered by a new clinical case and codes for differences in case management coverage 
between individuals of age 0-5 yrs and 5-100yrs as set by the two trigger dictionaries' respective 'agemin' and 'agemax'. 
'Seek' dictates the proportion of people who will seek care with a new clinical case - it is usually set to 1 such that 
'coverage' is the true case management coverage level. 'Rate' represents how quickly the case will receive treatment. 
It is used to create an exponential distribution of the delay period. We usually set `rate = 1/3.` for clinical cases and 
`rate = 1/2.` for severe cases.

You can also specify which drugs are used for case management. The default is Artemether-Lumefantrine with age-based dosing. 

Additional parameters can be added to restrict case management to certain nodes, node properties, or individual 
properties. See [here](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/health_seeking.py) for more information.



```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from malaria.interventions.health_seeking import add_health_seeking

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

add_health_seeking(cb, start_day=0,
                   targets=[{'trigger': 'NewClinicalCase', 
                              'coverage': 0.7, 
                              'agemin': 0, 
                              'agemax': 5,
                              'seek': 1, 
                              'rate': 0.3},
                            {'trigger': 'NewClinicalCase', 
                               'coverage': 0.5, 
                               'agemin': 5, 
                               'agemax': 100,
                               'seek': 1, 
                               'rate': 0.3},
                            {'trigger': 'NewSevereCase', 
                               'coverage': 0.85, 
                               'agemin': 0, 
                               'agemax': 100,
                               'seek': 1, 
                               'rate': 0.5}],
                   drug=['Artemether', 'Lumefantrine'])
```

## Change drug adherence

![figure](/images/04_highlighted.png)

Adherence to drugs can be modified using [configure_adherent_drug()](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/adherent_drug.py). 
This allows you to detail doses (and drugs given), 
intervals between doses, actual adherence values, and more. More documentation on how to configure adherent drugs is 
[here](https://docs.idmod.org/projects/emod-malaria/en/latest/parameter-campaign-individual-adherentdrug.html).

Configuring adherence is not required. In the absence of specific configuration, adherence to the full treatment course 
is assumed to be 100%.


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from malaria.interventions.adherent_drug import configure_adherent_drug

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

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
    
adherent_drug_configs = smc_adherent_configuration(cb, adherence=0.7)
```

## Add drug campaigns

![figure](/images/04_highlighted.png)

Using add_drug_campaign() you can set different drug campaigns including MDA, MSAT, SMC, fMDA, MTAT, rfMSAT, and rfMDA. 
This function also includes the ability to set coverage levels, repetitions (such as SMC cycles) and the timesteps 
between them, diagnostics information for campaigns that include testing, target groups, and restrictions on who can 
receive drugs by node or individual properties. For more details on all possible specifications see 
[malaria_drug_campaigns.py](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/malaria_drug_campaigns.py) 
in dtk-tools-malaria. Node and individual properties are set in the demographics file and can be called upon here for 
things like low vs high access groups.

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


## Add diagnostic surveys

![figure](/images/04_highlighted.png)

Add description here


```python
from malaria.interventions.malaria_drug_campaigns import add_drug_campaign, add_diagnostic_survey
                              
def diagnostic_survey(cb, sim_day, thresh=10, dose=1):
    # day 0 positivity
    add_diagnostic_survey(cb, start_day=sim_day,
                          diagnostic_threshold=thresh,
                          trigger_condition_list=[f'Received_IPTi_{dose}'],
                          positive_diagnosis_configs=[{'class': 'BroadcastEvent',
                                                       'Broadcast_Event': 'Day_0_positive'}],
                          negative_diagnosis_configs=[{'class': 'BroadcastEvent',
                                                       'Broadcast_Event': 'Day_0_negative'}],
                          triggered_campaign_delay=0)
                              #additional arguments
                              #repetitions=2, #2, 73
                              #tsteps_btwn_repetitions=365, #365, 7
                              #target={'agemin': val[0], 'agemax': val[1]},
                              #diagnostic_type='PF_HRP2',
```


## Using the model builder to set up multi-simulation experiments

![figure](/images/05_highlighted.png)

We often want to run a series of simulations where most parameters are held constant but one or more are varied. To set 
this up in a `dtk-tools` script, we use a `builder` and the `ModBuilder` function with associated `ModFn`s.

All model parameters can be varied ("swept through") with the `ModBuilder`: config, campaign, and even custom report output 
parameters. A few example uses are shown below.

### Set the number of stochastic realizations (replicates) to run

The **Run_Number** config parameter sets the simulation's random seed. 
To run multiple stochastic realizations of the same simulation, vary **Run_Number** in the builder.
In this example, the builder creates 10 identical simulations except for the value of **Run_Number**, which ranges 
from 0-9.


```python
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from simtools.ModBuilder import ModBuilder, ModFn

expt_name = 'multi_seed_experiment'
numseeds = 10 

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
builder = ModBuilder.from_list([[ModFn(DTKConfigBuilder.set_param, 'Run_Number', x)
                                 ]
                                for x in range(numseeds)
                                ])

run_sim_args = {
    'exp_name': expt_name,
    'config_builder': cb,
    'exp_builder' : builder
}
```

### Sweeping through multiple config parameters

In this example, we sweep through 20 values of **x_Temporary_Larval_Habitat** and 10 **Run_Number**s for each habitat 
value, creating 200 simulations.


```python
import numpy as np
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from simtools.ModBuilder import ModBuilder, ModFn

expt_name = 'multi_habitat_and_seed_experiment'
numseeds = 10 

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')
builder = ModBuilder.from_list([[ModFn(DTKConfigBuilder.set_param, 'Run_Number', x),
                                 ModFn(DTKConfigBuilder.set_param, 'x_Temporary_Larval_Habitat, hab)
                                 ]
                                for x in range(numseeds)
                                for hab in np.logspace(-2, 2, 20)
                                ])

run_sim_args = {
    'exp_name': expt_name,
    'config_builder': cb,
    'exp_builder' : builder
}
```

### Setting up complex sweeps

The following model builder implements specific functions from the NU team's SMC work. Two wrapper functions, 
`smc_intervention` and `set_EIR`, have been defined since this user doesn't need to vary over every argument in 
`add_drug_campaign` or `add_InputEIR`. Note that these wrapper functions *must* return a dictionary for the `ModBuilder` 
to function correctly.


```python
import numpy as np
from dtk.utils.core.DTKConfigBuilder import DTKConfigBuilder
from simtools.ModBuilder import ModBuilder, ModFn
from malaria.interventions.malaria_drug_campaigns import add_drug_campaign
from dtk.interventions.input_EIR import add_InputEIR, monthly_to_daily_EIR

expt_name = 'SMC_EIR_sweep'
numseeds = 10 

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

def smc_intervention(cb, day, cycles, coverage_level) :

    add_drug_campaign(cb, campaign_type='SMC',
                      coverage=coverage_level, start_days=[365 + day],
                      repetitions=cycles, 
                      tsteps_btwn_repetitions=30,
                      target_group={'agemin': 0.25, 'agemax': 5},
                      receiving_drugs_event_name='Received_SMC')

    return { 'SMC_coverage' : coverage_level}

def set_EIR(cb, EIRscale_factor) :

    monthly_site_EIR = [15.99, 5.41, 2.23, 10.33, 7.44, 11.77, 79.40, 85.80, 118.59, 82.97, 46.62, 33.49]
    daily_EIR = monthly_to_daily_EIR(monthly_site_EIR)
    add_InputEIR(cb, start_day=0, EIR_type='DAILY', dailyEIRs=daily_EIR, scaling_factor=EIRscale_factor)

    return { 'EIR_scale_factor' : EIRscale_factor}

builder = ModBuilder.from_list([[ModFn(smc_intervention, day=213, cycles=4, coverage_level=smc_coverage),
                                 ModFn(set_EIR, EIRscale_factor=eir_scale_factor),
                                 ModFn(DTKConfigBuilder.set_param, 'Run_Number', x),
                                 ]
                                for smc_coverage in [0.5, 0.9]
                                for eir_scale_factor in [0.2, 2]
                                for x in range(numseeds)
                                ])

run_sim_args = {
    'exp_name': expt_name,
    'config_builder': cb,
    'exp_builder' : builder
}
```


## Setting up the experiment manager

![figure](/images/06_highlighted.png)

The experiment manager serves as a mechanism to actually run simulations using the model and config builders. The respective builders, "builder" and "cb", are set up earlier in this list.


```python
from simtools.SetupParser import SetupParser
from simtools.ExperimentManager.ExperimentManagerFactory import ExperimentManagerFactory

Exp_name = 'my_experiment_name'

cb = DTKConfigBuilder.from_defaults('MALARIA_SIM')

builder = ModBuilder.from_list(...) # builder is optional

run_sim_args = {
    'exp_name': Exp_name,
    'config_builder': cb,
    'exp_builder' : builder         # delete this line if not using a builder
}

if __name__ == "__main__":
    SetupParser.init()
    exp_manager = ExperimentManagerFactory.init()
    exp_manager.run_simulations(**run_sim_args)
```
