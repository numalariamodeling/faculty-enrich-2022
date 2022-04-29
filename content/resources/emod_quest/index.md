---
date: "2019-02-11T19:27:37+10:00"
title: EMOD & Quest
weight: 2
---

<!--more-->

Northwesterns University's high performance computing (HPC) system is called Quest, you can read more about Quest [here](https://www.it.northwestern.edu/research/user-services/quest/overview.html).

Good to know:
- Connect to Quest using the software [MobaXterm](https://mobaxterm.mobatek.net/) or [FastX](https://www.starnet.com/fastx/) .
- The  [Quest Quick Start Guide](https://kb.northwestern.edu/page.php?id=70706) provide useful information to get started and find help to common questions and issues when using Quest.
- On the cluster, the SLURM job scheduling system is used [SLURM guide](https://slurm.schedmd.com/faq.html).


### Installation steps for EMOD and dtk on Quest (`NUCLUSTER`)

<details><summary>1. Configuring your bash and python environments</summary>
<p>
  
  You will need access to `python 3.6` (and virtualenv), `git`, and `singularity`.
These are available in via the module command.

For convenience, it is recommended to modify your `.bashrc` file to always load your modules when you open a new bash shell (e.g. connect via PuTTY). Please set your .bashrc file to contain at a minimum the following with your favorite file editor:
Edit file: `~/.bashrc`
Paste in (the final file should contain exactly this):

` .bashrc`

Source global definitions

    if [ -f /etc/bashrc]; then
            . /etc/bashrc
    fi

Uncomment the following line if you don't like systemctl's auto-paging feature:
`export SYSTEMD_PAGER=`

User specific aliases and functions

    module load singularity/latest
    module load git/2.8.2
    module load python/anaconda3.6
    
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/lib
    
    source ~/environments/dtk-tools-p36/bin/activate


Now run:

    source ~/.bashrc
    rsync /software/anaconda3.6/pkgs/python-3.6.6-h5001a0f_3/lib/libpython3.6m.so.1.0 ~/lib/

You now have `python 3.6` and `git` ready for use. 
You may notice an error about `~/environments/dtk-tools-p36/bin/activate` not existing. 
This is fine and expected; we will be setting it up next.

You will need to create a virtual python environment for the purpose of holding all the python packages DTK-Tools depends on.
To do this, we will need to install the python virtualenv package, use it, and activate our new environment (which will be nearly blank to start).

Run (one at a time):

    pip install --user virtualenv
    ~/.local/bin/virtualenv ~/environments/dtk-tools-p36
    source ~/environments/dtk-tools-p36/bin/activate
    
All of your python work (e.g. installing packages, sourcing packages) will use the environment `dtk-tools-p36`  because of the final source command above. Because your `~/.bashrc` file is activating your virtual environment, it will be active every time you log in from now on (if wanted, otherwise disable)
  
</p>
</details>

<details><summary>2. Installing DTK-Tools and disease-specific packages</summary>
<p>
DTK-Tools and disease specific packages can be installed via git. Run the following red commands, one after another in your terminal to install DTK-Tools:
    
    cd ~
    git clone https://github.com/InstituteforDiseaseModeling/dtk-tools.git dtk-tools-p36
    cd dtk-tools-p36
    python setup_manual.py

You can quickly verify that installation went ok by running (and checking for obvious errors printed to screen):
`dtk -h`

Now install the malaria-specific packages, one command at a time:

    cd ~
    git clone https://github.com/InstituteforDiseaseModeling/dtk-tools-malaria.git
    cd dtk-tools-malaria
    python setup.py develop
    cd ~
    git clone https://github.com/InstituteforDiseaseModeling/malaria-toolbox.git
    cd malaria-toolbox
    python setup.py develop
  
</p>
</details>  

<details><summary>3. Getting EMOD and runtime environment</summary>
<p>
- EMOD executable (`Eradication`) file for unix is shared via Box [here](https://northwestern.box.com/s/tgzx4celqvpz2x5p2casfp3h6dwx4wn6).



Edit `exe_path` in `simtools.ini` (see step 4 for more details on the `simtools.ini`)
Path to the model executable

    exe_path = /projects/p30781/bin/Eradication

Or if preferring a copy in home dir
Run in terminal

    cd ~ 
    mkdir bin
    cp  /projects/p30781/bin/Eradication   /home/<username>/bin/Eradication

Edit `exe_path` in `simtools.ini` (see step 4 for more details on the `simtools.ini`)
Path to the model executable

    exe_path = /projects/<username>/bin/Eradication

  
</p>
</details>  

<details><summary>4. Configuring DTK-Tools to use slurm</summary>
<p>
  
DTK-Tools makes use of a separate `simtools.ini` block type called `NUCLUSTER` in order to work with slurm in linux. The typical configurations in a CLUSTER-type block are a mix of traditional DTK-Tools configurations identifying important files and slurm-specific options. These slurm options are passed into the run script that will be submitted with the sbatch command. A simtools.ini file should go in the directory you plan to use as your working directory (where you will run commands from). It is highly suggested to use full paths in simtools.ini to avoid some pathing bugs in DTK-Tools (sigh). Here is an example for user jlg1657 on Quest. 

    [NUCLUSTER]
    type = CLUSTER
    # Path where the experiment/simulation outputs will be stored
    sim_root = /home/jlg1657/experiments
    
    # Path for the model to find the input files
    input_root = /home/jlg1657/dtk-tools-malaria/examples/simple_1node_drug_interventions/inputs
    
    # Path where a 'reporter_plugins' folder containing the needed DLLs
    base_collection_id =
    
    # Path to the model executable
    exe_path = /home/jlg1657/bin/Eradication
    
    # singularity command to load the execution environment and run the executable
    singularity_command = singularity exec -B {working_directory}:/data -B {exe_root}:/exec --pwd /data/$current_folder ~/images/centos_dtk-build.sif /exec/{exe_name}
    
    # Directory containing dtk_post_process.py, if needed
    python_path =
    
    # Resources request
    nodes = 1
    cpu_per_task = 1
    memory_per_cpu = 8192 # MB of memory
    
    # Which email to send the notifications to
    notification_email = jgerardin@northwestern.edu
    
    # Partition to use
    #partition = short
    partition = b1139


Limit time on this job hrs:min:sec

    time_limit = 04:00:00

Must be set at NU. Jobs will run with the specified account in slurm

    account = p30781

  
</p>
</details>  

<details><summary>5. Running jobs (aka experiments of simulations)</summary>
<p>
  
  
From this point, you should already have a working EMOD runtime image and binary, DTK-Tools installed and working, and an appropriate simtools.ini file (and block) for your slurm cluster. This section assumes as much and follows an example using the included simple_1node_drug_interventions in the dtk-tools-malaria python package.
Run:

    cd ~/dtk-tools-malaria/examples/simple_1node_drug_interventions/

- Edit file: `simtools.ini`
- Add the above red NUCLUSTER simtools.ini block to the end of the file. Make sure to change all usernames and emails to match yours.
- Edit file: create_serialized_file.py
- Change the SetupParser.default_block to `NUCLUSTER` 

    python create_serialized_file.py

DTK-Tools will automatically submit and wait for your jobs to complete and your script will resume after their successful completion. You will receive a completion email from slurm reporting the status of your jobs (successful, failed, some of both). If any fail, you will want to check the slurm*.err files in your experiment directory and try again. Nothing needs to be done on success; DTK-Tools via the slurm sacct command will automatically detect the successes and continue (there can be a delay in detection).

This particular script contains no follow-on analysis or processes, so it is done when the cluster jobs are finished.

As for interpreting the slurm emails, the following part of the email title indicates success (cryptic as it is). Anything else denotes at least one failed job.
`Ended, COMPLETED, ExitCode [0-0]`

Next is to run `run_with_interventions.py`:
- Edit file: run_with_interventions.py
- Change the environment variable to ‘NUCLUSTER’
- Change the burnin_id variable to be the experiment id previously generated by python create_serialized_file.py. This will be everything after the triple underscore (___) in the directory name of the experiment. E.g. 2019_12_09_16_14_47_378749 in the prior screenshot.

    python run_with_interventions.py

Again, nothing else needs to be done other than wait for completion.
If you wish to look at the result files, you can find them in the experiment results directory, which was specified in our `simtools.ini` file. 

Simulation output files are located in `<experiment_dir>/<simulation_dir>/output/` . The files used as inputs for running the simulations are in `Assets/`, and all simulation output files for stdout and stderr are redirected to files in the experiment directory. 
You can use these files to help debug any failures.

</p>
</details>  
  


  
  

