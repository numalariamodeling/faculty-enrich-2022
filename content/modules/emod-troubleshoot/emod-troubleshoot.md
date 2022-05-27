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


<details><summary><span style="color: blue";">CheckIpKeyInWhitelist</span></summary>
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
 

<details><summary><span style="color: blue";">application called MPI_Abort</span></summary>
<p>

```
00:00:11 [0] [E] [SerializedPopulation] Unexpected error reading serialized population file, '/projects/b1139/experiments/burnin_0125___2022_02_10_09_43_09_881919/Simulation_YYR06BON/output/state-03650.dtk'.
00:00:11 [0] [E] [Eradication] 

SerializationException: 
Exception in utils/JsonFullReader.cpp at 69 in startObject.
Expected to starting reading an object and it is not an object.


/exec/Eradication() [0x94c2e3]
/exec/Eradication(_ZN6Kernel17DetailedExceptionC1EPKciS2_+0x2e7) [0x94ce37]
/exec/Eradication(_ZN6Kernel22SerializationExceptionC2EPKciS2_S2_+0x2f) [0x954bff]
/exec/Eradication(_ZN6Kernel14JsonFullReader11startObjectEv+0x250) [0x95f580]
/exec/Eradication(_ZN6Kernel14JsonFullReader10startClassERSs+0x16) [0x95d8d6]
/exec/Eradication(_ZN6Kernel13ISerializable9serializeERNS_8IArchiveERPS0_+0xaf) [0x95c6ff]
/exec/Eradication(_ZN6Kernel8IArchiveanERPNS_13ISerializableE+0x9) [0x95a619]
/exec/Eradication(_ZN6Kernel20UsageDependentBednet9serializeERNS_8IArchiveEPS0_+0x1f1) [0x7f8a91]
/exec/Eradication(_ZN6Kernel13ISerializable9serializeERNS_8IArchiveERPS0_+0x2d3) [0x95c923]
/exec/Eradication(_ZN6Kernel8IArchiveanERPNS_13ISerializableE+0x9) [0x95a619]
/exec/Eradication(_ZN6Kernel22InterventionsContainer9serializeERNS_8IArchiveEPS0_+0xf3) [0x620b13]
/exec/Eradication(_ZN6Kernel28VectorInterventionsContainer9serializeERNS_8IArchiveEPS0_+0x11) [0x7b43a1]
/exec/Eradication(_ZN6Kernel29MalariaInterventionsContainer9serializeERNS_8IArchiveEPS0_+0x29) [0x821299]
/exec/Eradication(_ZN6Kernel13ISerializable9serializeERNS_8IArchiveERPS0_+0x2d3) [0x95c923]
/exec/Eradication(_ZN6Kernel8IArchiveanERPNS_13ISerializableE+0x9) [0x95a619]
/exec/Eradication(_ZN6Kernel15IndividualHuman9serializeERNS_8IArchiveEPS0_+0x264) [0x6a1864]
/exec/Eradication(_ZN6Kernel21IndividualHumanVector9serializeERNS_8IArchiveEPS0_+0x11) [0x7653a1]
/exec/Eradication(_ZN6Kernel22IndividualHumanMalaria9serializeERNS_8IArchiveEPS0_+0x2f) [0x80c76f]
/exec/Eradication(_ZN6Kernel13ISerializable9serializeERNS_8IArchiveERPS0_+0x2d3) [0x95c923]
/exec/Eradication(_ZN6Kernel8IArchiveanERPNS_13ISerializableE+0x9) [0x95a619]
/exec/Eradication(_ZN6Kernel4Node9serializeERNS_8IArchiveEPS0_+0xd9d) [0x6bca2d]
/exec/Eradication(_ZN6Kernel10NodeVector9serializeERNS_8IArchiveEPS0_+0x29) [0x775719]
/exec/Eradication(_ZN6Kernel11NodeMalaria9serializeERNS_8IArchiveEPS0_+0x11) [0x825c41]
/exec/Eradication(_ZN6Kernel13ISerializable9serializeERNS_8IArchiveERPS0_+0x2d3) [0x95c923]
/exec/Eradication(_ZN6Kernel8IArchiveanERPNS_13ISerializableE+0x9) [0x95a619]
/exec/Eradication(_ZN15SerializedState16ReadDtkVersion34EP8_IO_FILEPKcRNS_6HeaderE+0x1a8) [0x5f9508]
/exec/Eradication(_ZN15SerializedState24LoadSerializedSimulationEPKc+0xaf) [0x5f9c1f]
/exec/Eradication(_ZN6Kernel17SimulationFactory16CreateSimulationEv+0xbc) [0x5f2b5c]
/exec/Eradication(_ZN17DefaultController16execute_internalEv+0xb2) [0x5cda42]
/exec/Eradication(_Z21ControllerInitWrapperiPPcPN6IdmMpi16MessageInterfaceE+0x1a14) [0x8e85a4]
/exec/Eradication(_Z14MPIInitWrapperiPPc+0x24) [0x8ea384]
/exec/Eradication(main+0x372) [0x5b4042]
/lib64/libc.so.6(__libc_start_main+0xf5) [0x2b947407d495]
/exec/Eradication() [0x5b89fe]

application called MPI_Abort(MPI_COMM_WORLD, -1) - process 0
```

The key error here is `Expected to starting reading an object and it is not an object.` which refers to reading the serialization file `/state-03650.dtk`.
Most likely reason is that a wrong burnin_id was specified, i.e. referring to a burnin simulation that was run with a different version as the current simulation.



</p>
</details>    



## Analyzers don't return results

> (The problem statement)

Explanations and potential solutions

> (More problem statements)

More explanations and potential solutions
