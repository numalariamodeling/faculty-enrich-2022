---
date: "2022-05-03T13:49:00-05:00"
draft: false
title: Troubleshooting
weight: 5
---

Here is a series of common problems that we face when getting EMOD to work, and the potential solutions. If your problem is not in here, and you figured it out, please consider documenting them here!  
For general troubleshooting and additional helpful tools you may also check out ['Troubleshooting & more'](https://faculty-enrich-2022.netlify.app/resources/help-faq/) under resources.

{{< toc >}}

## Simulation(s) failed

> After running the experiment script, the software will display the progress of your experiments, e.g., how many simulations are in progress, how many succeeded. Sometimes you see that one or more simulations within your experiment has failed.

There can be a whole lot of reasons why your simulations failed. It could be that your input folder is set up incorrectly or that your system doesn't have enough memory. The first step to troubleshoot is to look out for `StdErr.txt` files for your output. This is located at your experiment output folders (check `simtools.ini` if you don't know where they are). Navigate to the corresponding experiment folder and the simulation subfolder. If you're running multiple simulations within an experiment, look for simulation that you know has failed. `StdErr.txt` is located in the simulation subfolder, and often provide good pointer to what has caused the simulation to fail.

### Possible reason 1: Memory not enough
Here's an example:

```
00:00:00 [0] [E] [Eradication] 

bad allocation
Memory allocation failure: try reducing the memory footprint of your simulation or using more cores.
```

This indicate that other apps and programs are taking up too much memories in your computer that there aren't enough for the `Eradication.exe`. You can try to close some of the background processes and apps, or you may need to reboot your computer.

### Possible reason 2: `msmpi.dll` issue

> There are times when your `StdErr.txt` has no error message, and yet the Simulation(s) failed. Try running the `Eradication.exe` in its folder. Without additional argument, the program should return following message: `For full usage, run: Eradication.exe --help` 

If the program does not produce message like this, this is hinting at the lack of `msmpi.dll` in your operating system. You can [download and install](https://www.microsoft.com/en-us/download/details.aspx?id=100593) the MS-MPI, and run `Eradication.exe` again and see if you get the message mentioned in the quoate.

### Possible reason 3: something wrong in the simulation input files


#### CheckIpKeyInWhitelist 
<details><summary><span style="color: blue";">1 Setup analyzer class & define parameters </span></summary>
<p>
```
00:00:01 [0] [E] [Eradication] 

GeneralConfigurationException: 
Exception in utils\BaseProperties.cpp at 631 in Kernel::BaseFactory::CheckIpKeyInWhitelist.
Invalid IndividualProperties key 'Access' found in demographics file. Use one of: 'Accessibility', 'Age_Bin', 'Geographic', 'HasActiveTB', 'InterventionStatus', 'Place', 'QualityOfCare', 'Risk', 
```

The custom defined IP 'Access' is not valid (not found in Whitelist) .
Solution: Disable Whitelist

```
cb.update_params('Disable_IP_Whitelist' : 1)
```

</p>
</details>


## Analyzers don't return results

> (The problem statement)

Explanations and potential solutions

> (More problem statements)

More explanations and potential solutions
