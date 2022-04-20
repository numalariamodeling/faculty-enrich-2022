---
date: "2019-02-11T19:27:37+10:00"
draft: false
title: FAQ's and how to get help
weight: 3
---

<!--PLACEHOLDER, TEMPLATE to be edited-->

## Getting help 
Your question might be answered in one of the available resources linked below:

- [EMOD How To's](https://faculty-enrich-2022.netlify.app/modules/emod-how-to/emod-how-to/)
- [IDM's EMOD malaria model documentation](https://docs.idmod.org/projects/emod-malaria/en/latest/index.html)
- [Slack help channel](https://nu-idm-malaria.slack.com/archives/C013UV3NTL7)
- [SLURM guide](https://slurm.schedmd.com/faq.html) _(for use on NUCLUSTER)_
- [Quest Quick Start Guide](https://kb.northwestern.edu/page.php?id=70706) _(for use on NUCLUSTER)_

If not, please check the FAQ's below.


##  Troubleshooting

<details><summary>1. Problems with installation</summary>
<p>
  Possible reasons (check your error message):

 - Wrong python version 
 - Wrong versions or missing modules
 - Latest version of EMOD and dtk when cloning repository?

</p> 
</details></br> 
<details><summary>2. Problems with creating simulations</summary>
<p>
  Possible reasons (check your error message):

 - ...
 - ...

</p> 
</details></br> 
<details><summary>3. Problems with running simulations</summary>
<p>
  Possible reasons (check your error message):

 - Is the job running? -> check via `squeue -u <username>` (NUCLUSTER)
 - Check the log messages for errors
   - Did the simulation start? Y/N Problem with input vs scheduling  (i.e. wrong sbatch details)
 - Is there enough space on the drive? -> check via `homedu`  or `checkproject <projectnr>`  (NUCLUSTER)

</p> 
</details></br> 
<details><summary>4. Problems with analyzing simulations</summary>
<p>
  Possible reasons (check your error message):

 - Is the job complete? -> check via `squeue -u <username>` (NUCLUSTER)
 - Check the log messages for errors
   - Check for errors on SLURM `seff <jobid>`  (NUCLUSTER)
 - Did only some scenarios fail?
    - Rerun failed simulations i.e. due to time or memory limit

</p> 
</details></br> 
<details><summary>5. Problems with processing results and plotting</summary>
<p>
  Possible reasons (check your error message):

 - Check python version
 - Disable interactive display of figures via `matplotlib.use('Agg')`
 - ...

</p> 
</details></br> 



##  FAQ's

<details><summary>1. Some common question not answered above</summary>
<p>
  Possible answers

</p> 
</details></br> 
<details><summary>2. Another common question not answered above</summary>
<p>
  Possible answers

</p> 
</details></br> 


_Nothing found or issue persists? Please contact the course administrators for help._
