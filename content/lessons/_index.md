---
date: "2022-04-21T15:09:23-05:00"
title: Overview
weight: 1
---

## Technical curriculum overview

**Week 1: Overview of EMOD**

- Install PyCharm, `dtk-tools`, and `dtk-tools malaria` (Obj 2b)
- Basic git
- Clone [example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples)
- Learn development history of EMOD (Obj 2a)
- Learn each part of EMOD software ecosystem and how they interact (Obj 2a)
- Learn basic EMOD simulation workflow and run a barebones `ExampleSim` (Obj 2a, 2c)
  
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

**Week 4: Analyzers and plotters**
- Walkthrough examples of analyzers (Obj 2a, 2c)
    + Single simulation
    + Multi-simulation experiment
    + Saving to csv
    + Plotting in analyzer
- Write or adapt a customized analyzer to extract data from own research project (Obj 2d, 2g)
- Plot analyzers' output using R or Python (Obj 2d, 2e, 2g)

**Week 5**

*no technical curriculum, prepping Aims and slide decks*
- Continue to refine own research projects

**Week 6: Serialization**

- Understand the concept and rationale of burn-in and serialization (Obj 2a, 2c)
- Walkthrough example burnin and pickup code
- Perform the following for own research project (Obj 2c):
    + Serialization of simulation: create burnin
    + Picking up a serialized population and run it forward

**Week 7: Sweeping and calibration**

- Understand the concept and needs for sweeping and calibration to prevalence data (Obj 2f)
- Perform the following for own research project (Obj 2f):
    + Sweep through a range of values for a parameter
    + Determine the best value for the parameter by calculating likelihood over observed data

**Week 8: Individual properties**

- Understand the concept and use case for individual properties (Obj 2a)
- Perform the following for own research project (Optional; Obj 2c)
    + Enable individual properties
    + Implement an intervention based on individual properties
    + Implement reporting based on individual properties

**Week 9: Infusing simulations with real data**

- Overview of within-host model and calibration process for EMOD
- Vector species bionomics and refining vector control interventions
- Roundtable: Using DHS
    + DHS overview
    + Pulling DHS data through API
- Continue to refine own research projects

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

**Week 12: Advanced EMOD: Spatial modeling in EMOD**

- How to create multi-node simulations
- How to set up human and/or vector movement between nodes
- Custom reports useful for multi-node simulations
- Continue to refine own research projects

**Week 13: Advanced EMOD: gene drive and reactive interventions**

- Reactive interventions and surveillance in EMOD
- Guest lecture: vector genetics and modeling gene drives in EMOD
- Roundtable: Current limitations and future of EMOD and agent-based modeling of malaria
- Continue to refine own research projects

**Week 14: HPC**

- Using Quest
- Roundtable: Technical difficulties and challenges to run EMOD experiments from home institution
    + How can NUMM help before or after return?

**Week 15**

*no technical curriculum, prepping Aims and slide decks*
- Continue to refine own research projects

**Week 16**

*no technical curriculum, final week presentations*