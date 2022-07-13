---
date: "2019-02-11T19:30:08+10:00"
draft: false
title: Week 7 - July 11
weight: 8
---

<!--more-->

### Logistics

- Wednesday July 13, 10am: Ousmane WIP practice
- Friday July 15, 1pm: Sebastian thesis defense practice
- Annie out all week

### Technical track (EMOD)

Topic: Sweeping and calibration

- Understand the concept and needs for sweeping and calibration to prevalence data (Obj 2f)
- Perform the following for own research project (Obj 2f):
    + Sweep through a range of values for a parameter
    + Determine the best value for the parameter by calculating likelihood over observed data
- [=> example exercises](https://github.com/numalariamodeling/faculty-enrich-2022-examples#week-7-sweeping-and-calibration-)

### Journal club

No journal club this week.

### Proposal writing

Feedback sessions on Aims third drafts.

---

## Sweeping and Calibration

One of the important part of modelling is calibration. We don't always know some of the parameters in our model, yet these parameters play important role in shaping our model output. As a result, we need to estimate these parameters... but how?

We "fit" or "calibrate" the parameters to some real data that are available to us. Essentially, we propose a range of values for these parameters, and run the model to see if the output matches the actual observed data. This can sometimes be known as "sweeping" through a range of values. The set of proposed values that allow the model to match the actual data well would be chosen for subsequent modelling steps.

Here's a simple example: Let's say we are trying to model the malaria transmission of a district somewhere from 2012 to 2019. We gather the district's demographic and climates information and incorporate into our model. Reviewing the intervention history of the district, we found that people in the district did not have proper healthcare to manage clinical or severe malaria. However, some people did receive ITN in the beginning of 2010, but no one knows what are the coverage!

Using a suitable ITN coverage is important here since the effect of ITN will last long enough into the next few years. If we assume a smaller than actual coverage, we may over estimate the malaria burden and vice versa.

It turns out that there was a household survey conducted in the district in 2011. A number of children under 5 years old were tested for malaria during the survey, and we know how many of them are positive.

We can propose a range of ITN coverage values from 20% to 50% and run the model (Let's say the district manager is confident that not more than half of the people got it). We can then compare the parasite prevalence rate (PfPR) in the model with the actual household survey data using say a likelihood-based method.

In this week's example, we are walking through the sweeping and calibration of ITN coverage under this storyline. We will be doing the following:
- Run 20 years of burn-in (So the community builds up some immunity). This would correspond to 1990 to 2009 in the story above.
- Run 2 years of simulations, in which ITN is given out in the beginning. This would mimic the 2010 and 2011 in the story above. In these simulations, ITN coverage for 0 to 10 years old vary from 0.2 to 0.5.
- Collect results from the 2-year simulations using analyzer.
- Compare simulated PfPR vs. real PfPR in 2011, then find the best ITN coverage.