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

**Week 2: Basic building blocks of EMOD**

- Identify the three building blocks to run an EMOD simulation (Obj 2c)
    + The basic inputs: Parameters, demographics and climates
    + The reports/outputs: JSON/CSV outputs
    + The interventions: (To be covered in following weeks)
- Create demographics and climate files, and incorporate into simulations (Obj 2d)
    +  Repeat this in own research projects
- Grab and plot outputs (Obj 2d)
    + InsetChart
    + MalariaSummaryReport

**Week 3: Interventions in EMOD**

- Learn how major malaria interventions are implemented in EMOD (Obj 2a, 2c)
    + Case management for symptomatic malaria
    + Vector control: ITNs, IRS, and larvicides
    + Drug campaigns
- Learn about how EMOD can report specific event occurs in the simulation (Obj 2d)
- Learn about `ModBuilder` (Obj ??)
- Run various interventions in the simulation and examine the output (Obj 2d, 2e)
    + Repeat this in own research projects

**Week 4: Analyzers and plotters**

- Walkthrough examples of analyzers (Obj 2a, 2c)
- Write a customized analyzer to extract data from own research project (Obj 2d, 2g)
- Plot analyzers' output using R or Python (Obj 2d, 2e, 2g)

**Week 5**

*no technical curriculum, prepping Aims and slide decks*

**Week 6: Serialization**

- Understand the concept and rationale of burn-in and serialization (Obj 2a, 2c)
- Perform the following for own research project or based on (Obj 2c):
    + Serialization of simulation ("Historical" HBHI)
    + Picking up a serialized population and run it forward (HBHI 2010 to 2019) 

**Week 7: Sweeping and calibration**

- Understand the concept and needs for sweeping and calibration (Obj 2f)
- Perform the following for own research project or based on an example (Obj 2f):
    + Sweep through a range of values for a parameter
    + Determine the best value for the parameter by calculating likelihood over observed data

**Week 8: Individual properties**

- Understand the concept and use case for individual properties (Obj 2a)
- Perform the following for own research project or based on an example (Optional; Obj 2c)
    + Enable individual properties
    + Implement an intervention based on individual properties

**Week 9: HBHI simulation**

- Code walkthrough of HBHI analysis process
- Learn more about HBHI analysis via interaction with HBHI-Nigeria Shiny app (Obj 3a, 3b)

**Week 10**

*no technical curriculum, prepping Aims and slide decks*

**Week 11: Infusing simulations with real data**

- Learn how `MALARIA_SIM` and interventions are parameterized, and understand the limitations of the parameterization process
- Roundtable: Working with DHS

**Week 12: Other EMOD applications**

- Demonstrate the use of EMOD in generic and spatial setting (Obj 2a)

**Week 13: Wildcard topic (based on feedback)**

- Roundtable: Current limitations and future of EMOD and agent-based modeling of malaria

**Week 14: Wildcard topic (based on feedback)**

- (Can potentially be about discussion on computing infrastructures, e.g., "public" super computer, EMOD on Linux)
- Roundtable: Technical difficulty and challenge to run EMOD experiments in home institute
    + What can NUMM help? Before or after return?

**Week 15**

*no technical curriculum, prepping Aims and slide decks*

**Week 16**

*no technical curriculum, final week presentations*