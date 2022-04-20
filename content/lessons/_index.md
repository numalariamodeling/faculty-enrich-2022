---
date: "2018-11-28T15:14:39+10:00"
title: Overview
weight: 1
---

## Technical curriculum overview

**Week 1: Overview of EMOD**

- Install PyCharm, `dtk-tools`, and `dtk-tools malaria` (Obj 2b)
- Learn EMOD history (Obj 2a)
- Learn each part of EMOD software ecosystem and how they interact (Obj 2a)
- Learn basic EMOD simulation workflow and run a barebones `ExampleSim` (Obj 2c)

**Week 2: Basic building blocks of EMOD**

- Identify the three building blocks to run an EMOD simulation (Obj 2c)
    + The basic inputs: Parameters, demographics and climates
    + The reports/outputs: JSON/CSV outputs
    + The interventions: (To be covered in following weeks)
- Create demographics and climate files, incorporate into simulation (Obj 2d)
- Grab and plot outputs (Obj 2d)
    + InsetChart
    + MalariaSummaryReport

**Week 3: Interventions in EMOD**

- Learn how major malaria interventions are implemented in EMOD (Obj 2a, 2c)
    + Case management for symptomatic malaria
    + Vector control: ITNs, IRS, and larvicides
    + Drug campaigns
- Event reporting (Obj 2d)
- Run various interventions in the simulation and examine the output (Obj 2c, 2e)

**Week 4: Build and run experiments**

- Learn how to run multiple simulations within an experiment (Obj 2a, 2c)
- Learn how to run factorial experiment (Obj 2c)

**Week 5**

*no technical curriculum, prepping Aims and slide decks*

**Week 6: HBHI simulation**

- Run HBHI "historical" simulations *independently* (Obj 2c, Obj 2d)
- Learn more about HBHI analysis via interaction with HBHI-Nigeria Shiny app (Obj 3a, 3b)
- Roundtable: Working with DHS

**Week 7: Analyzers and plotters**

- Walkthrough examples of analyzers (based on "historical" simulations) (Obj 2a, 2c)
- Write an analyzer to extract data from "historical simulation" (Obj 2d, 2g)
- Plot analyzers' output using R or Python (Obj 2d, 2e, 2g)

**Week 8: Serialization**

- Understand the concept and rationale of burn-in and serialization (Obj 2a, 2c)
- Perform the following (Obj 2c):
    + Serialization of simulation ("Historical" HBHI)
    + Picking up a serialized population and run it forward (HBHI 2010 to 2019) 

**Week 9: Sweeping and calibration**

- Understand the concept and needs for sweeping and calibration (Obj 2f)
- Perform the following (Obj 2f):
    + Sweep through a range of habitat multipliers
    + Determine the best habitat multiplier by calculating likelihood over observed data

**Week 10**

*no technical curriculum, prepping Aims and slide decks*

**Week 11: Full HBHI workflow**

- Run the full HBHI workflow and display results (Obj 2d, 3a, 3b)
- Compare two projection scenario with different interventions (Obj 2e)
- Roundtable: Challenge and limitation of current HBHI analysis (Obj 4a, 4b, 4c)

**Week 12: Individual properties**

- Demonstrate the use of individual properties in EMOD (Obj 2a)
    + RTS,S scenarios

**Week 13: Other EMOD application**

- Demonstrate the use of EMOD in generic and nxtek setting (Obj 2a)

**Week 14: Wildcard topic (based on feedback)**

- Roundtable: Technical difficulty and challenge to run EMOD experiments in home institute (Obj None)
    + What can NUMM help? Before or after return?


**Week 15**

*no technical curriculum, prepping Aims and slide decks*

**Week 16**

*no technical curriculum, final week presentations*