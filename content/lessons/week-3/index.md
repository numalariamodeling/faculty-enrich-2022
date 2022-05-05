---
date: "2019-02-11T19:27:37+10:00"
title: Week 3 - June 13
weight: 4
---

### Logistics

- Wednesday June 15: Second draft Aims page due
- Wednesday June 15, 1pm: Ousmane IGH WIP
- Friday June 17: Journal club

### Technical track (EMOD)

Topic: Interventions in EMOD

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
- [=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-3-interventions-in-emod-)

### Journal club

Presenter: (add presenter name)

Runge M, Snow RW, Molteni F, Thawer S, Mohamed A, Mandike R, Giorgi E, Macharia PM, Smith TA, Lengeler C, Pothin E. 
[Simulating the council-specific impact of anti-malaria interventions: a tool to support malaria strategic planning in 
Tanzania.](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0228469) PloS one. 2020 Feb 19;15(2):e0228469.

### Proposal writing

Second draft of Aims page due Wednesday. Feedback sessions on Aims pages.

---

## Interventions in EMOD

Malaria interventions that can be readily added into the simulations are:
- [Case management](/modules/emod-how-to/emod-how-to/#add-case-management) (Better treatment seeking behaviour can reduce malaria burden and transmission)
- Insecticide-based intervention
    + [ITN](/modules/emod-how-to/emod-how-to/#add-itn) (Insecticide-treated bed nets)
    + [IRS](/modules/emod-how-to/emod-how-to/#add-irs) (Indoor residual spraying)
- [Larvicides](/modules/emod-how-to/emod-how-to/#add-larvicides)
- Antimalarial drug campaigns
- Malaria vaccine

You can add these campaigns to your campaign builder object through either `dtk-tools` or `dtk-tools-malaria` functions. We will go through the each of these examples available in the "How To's" (click the links above to go to the corresponding sections).

For antimalarial drug campaigns, we can use a single function `add_drug_campaign()` ([See here](/modules/emod-how-to/emod-how-to/#add-drug-campaigns)) to cover campaigns such as mass drug administration (MDA), mass screen and test (MSAT), and their variants, as well as seasonal malaria chemotherapy (SMC). Note that intermittent preventive treatment in infancy (IPTi) or in pregnancy (IPTp) is not directly available in the simulation. Since not everyone adhere to taking the full course of drugs, it is important to adjust drug adherence accordingly, which is [easily specified using an additional function](/modules/emod-how-to/emod-how-to/#change-drug-adherence). We have calibrated the parameters for most of the drugs, and you can see them [here](https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria/blob/master/malaria/interventions/malaria_drugs.py).

For malaria vaccine, because vaccine schedule and booster requirement can be elaborate, we will [walk through an example](/modules/emod-how-to/emod-how-to/#add-vaccine) of how to distribute RTSS vaccine via age-based immunization program.

When someone receive an intervention, e.g., treatment from seeking care, getting a bednet etc., an "event" would be generated and broadcasted. These "events" allow us to do bookkeeping, and ensure the intervention is properly specified.