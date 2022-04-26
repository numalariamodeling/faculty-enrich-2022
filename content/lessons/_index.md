---
date: "2022-04-21T15:09:23-05:00"
title: Overview
weight: 1
---

## Technical curriculum overview

**Week 1: Overview of EMOD**

- Install PyCharm, `dtk-tools`, and `dtk-tools malaria` (Obj 2b)
- Learn development history of EMOD (Obj 2a)
- Learn each part of EMOD software ecosystem and how they interact (Obj 2a)
- Learn basic EMOD simulation workflow and run a barebones `ExampleSim` (Obj 2a, 2c)
- Basic git
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-1-overview-of-emod-)_
  
**Week 2: Basic building blocks of EMOD**

- Identify the three building blocks to run an EMOD simulation (Obj 2c)
    + The basic inputs: Parameters, demographics and climates
    + The reports/outputs: JSON/CSV outputs
    + The interventions: dynamic creation of the campaign file
- Create demographics and climate files, and incorporate into simulations (Obj 2d)
    + Implement in own research project
    + Run barebones version of own research project
- Grab and plot outputs (Obj 2d)
    + InsetChart
    + MalariaSummaryReport
    + Implement in own research project
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-2-basic-building-blocks-of-emod-)_


**Week 3: Interventions in EMOD**

- Learn how major malaria interventions are implemented in EMOD (Obj 2a, 2c)
    + Case management for symptomatic malaria
    + Vector control: ITNs, IRS, and larvicides
    + Antimalarial drug campaigns
- Learn about event reporting in EMOD (Obj 2d)
    + Individual-level events
    + Aggregated events
- Run various interventions in the simulation and examine the output (Obj 2d, 2e)
    + Implement in own research project
- Learn about `ModBuilder` (Obj ??)
    + Design and implement `ModBuilder` for own research project
    + Run a multi-simulation experiment for own research project
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-3-interventions-in-emod-)_

**Week 4: Analyzers and plotters**
- Walkthrough examples of analyzers (Obj 2a, 2c)
    + Single simulation
    + Multi-simulation experiment
    + Saving to csv
    + Plotting in analyzer
- Write or adapt a customized analyzer to extract data from own research project (Obj 2d, 2g)
- Plot analyzers' output using R or Python (Obj 2d, 2e, 2g)
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-4-analyzers-and-plotters-)_

**Week 5**

*no technical curriculum, prepping Aims and slide decks*
- Continue to refine own research projects

**Week 6: Serialization**

- Understand the concept and rationale of burn-in and serialization (Obj 2a, 2c)
- Walkthrough example burnin and pickup code
- Perform the following for own research project (Obj 2c):
    + Serialization of simulation: create burnin
    + Picking up a serialized population and run it forward
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-6-serialization-)_

**Week 7: Sweeping and calibration**

- Understand the concept and needs for sweeping and calibration to prevalence data (Obj 2f)
- Perform the following for own research project (Obj 2f):
    + Sweep through a range of values for a parameter
    + Determine the best value for the parameter by calculating likelihood over observed data
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-7-sweeping-and-calibration-)_

**Week 8: Individual properties**

- Understand the concept and use case for individual properties (Obj 2a)
- Perform the following for own research project (Optional; Obj 2c)
    + Enable individual properties
    + Implement an intervention based on individual properties
    + Implement reporting based on individual properties
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-8-individual-properties)_

**Week 9: Infusing simulations with real data**

- Overview of within-host model and calibration process for EMOD
- Vector species bionomics and refining vector control interventions
- Roundtable: Using DHS
    + DHS overview
    + Pulling DHS data through API
- Continue to refine own research projects
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-9-infusing-simulations-with-real-data-)_

**Week 10**

*no technical curriculum, prepping Aims and slide decks*
- Continue to refine own research projects

**Week 11: Advanced EMOD: HBHI workflow as a complex example**

- Understand the context for HBHI modeling
- Nigeria example: manuscript and HBHI-Nigeria Shiny app (Obj 3a, 3b)
- Code walkthrough of HBHI analysis process
    + Identify familiar components from previous weeks
    + Identify new components: managing shared functions, reading intervention data from csv's, multi-analyzers
- Continue to refine own research projects
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-11-advanced-emod-hbhi-workflow-as-a-complex-example-)_

**Week 12: Advanced EMOD: Spatial modeling in EMOD**

- How to create multi-node simulations
- How to set up human and/or vector movement between nodes
- Custom reports useful for multi-node simulations
- Continue to refine own research projects
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-12-advanced-emod-spatial-modeling-in-emod-)_

**Week 13: Advanced EMOD: gene drive and reactive interventions**

- Reactive interventions and surveillance in EMOD
- Guest lecture: vector genetics and modeling gene drives in EMOD
- Roundtable: Current limitations and future of EMOD and agent-based modeling of malaria
- Continue to refine own research projects

**Week 14: HPC**

- Using Quest
- Roundtable: Technical difficulties and challenges to run EMOD experiments from home institution
    + How can NUMM help before or after return?
- _[=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-14-hpc-)_

**Week 15**

*no technical curriculum, prepping Aims and slide decks*
- Continue to refine own research projects

**Week 16**

*no technical curriculum, final week presentations*