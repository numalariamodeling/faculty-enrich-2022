---
date: "2022-04-21T10:49:22-05:00"
draft: false
summary: Something about this module.
title: Installing EMOD
weight: 2
---

## Windows
EMOD is easiest to install (and use) in Windows.

### Ingredients
- Access to the `dtk-tools` and `dtk-tools-malaria` github repository.
- The excutable binary `Eradication.exe`
- Administrator access to a Windows OS preferably with PowerShell (This should be available by default in Windows 7 onwards)

### Steps to install `dtk-tools`
1. Install Python 3.6.0: Download the installer here for [64-bit system](https://www.python.org/ftp/python/3.6.0/python-3.6.0-amd64.exe) or [32-bit](https://www.python.org/ftp/python/3.6.0/python-3.6.0.exe) (Note: this is rare.) Check the checkbox "Add Python 3.6.0 to PATH" to make your life easier.
2. Set up environment variables PATH. (Note: If you did the checkbox in previous step, you can skip this)
    + In the Windows Search Bar, type "Environment" and this should typically point you to "Edit the system environment variables", then click on it.
    + Alternatively, go to "Control Panel" -> "System and Security" -> "System" -> "Advanced System Settings"
    + Click "Environment Variables" button
    + In the "System variables" section, look out for the "Path" entry, double click on it.
    + Here you will need to fill in the path to your `python.exe` **and** `pip.exe`
    + This is typically in `C:\Users\(username)\AppData\Local\Programs\Python\Python36` and `C:\Users\(username)\AppData\Local\Programs\Python\Python36\Scripts`. Both are required to be added.
3. Now launch your powershell (or other terminal such as CMD, or PyCharm terminal). And navigate to your "work folder" (e.g., mine is `C:\Users\(username)\`)
    + Easiest way is to type "powershell" in your search bar  (or "echo %homepath%" in cmd/ pycharm terminal").
    + If not, you can go to "Start" menu and look for it.
4. Install `virtualenv` using `pip`
    + Command: `pip install virtualenv`
5. Create a virtual environment of name of your choice. I use `dtk` for mine.
    + Command: `virtualenv dtk`
    + Virtual environment creates a compartment in your computer in which you need to separately install packages of your choice. Two virtual environments can carry different set of packages.
6. Activate the virtual environment of your choice.
    + Command: `dtk\Scripts\activate`
    + You *should* notice now your prompt is preceded by `(dtk)` (or name of your environment)
    + Note if you see the error message 'activate.ps1 cannot be loaded because running scripts is disabled on this system' you need to use a different terminal than powershell ([about_Execution_Policies](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7.2)).
7. Now it's time to install `dtk-tools` and its associated packages. Clone the git repository to wherever you want it to be. Navigate over.
    + Say `C:\Users\(username)\work`. 
    + Navigate to the cloned repository, say `C:\Users\(username)\work\dtk-tools`
8. Install the tool using `setup_manual.py` script
    + Command: `python setup_manual.py`
    + Yes, there is a `setup.py` script but we typically have better success using the separate script
9. If you don't see any noticeable error messages, use `dtk version` command to verify if you successfully installed the `dtk-tools`, if successful you will see the image below:

![figure](/images/dtk_version.png)  
</br>
### Steps to install `dtk-tools-malaria`
1. Clone the git repository to wherever you want it to be. Navigate over.
2. Activate the virtual environment of your choice, if you haven't already.
3. Install the tool using `setup.py` script
    + Command: `python setup.py develop`
    + This is the "standard" way to install a custom package for python. Specifying the `develop` behind allows changes made to the code repository to directly reflect in the package. Sometimes, you want to use `install` instead: future changes to the code repository will not be captured into the installed package in your computer until you rerun the command.
4. If there are no clear error messages, go to the next section to verify that both `dtk-tools` and `dtk-tools-malaria` are installed successfully and useable!

### Verify that you are all set!
1. Navigate to the `dtk-tools-malaria` folder and go to the `examples` subfolder.
2. Edit the `simtools.ini` (using PyCharm or Notepad++) to make sure that they are pointing to the right path. I.e., change all the `path\to` to correspond to the location of your `dtk-tools-malaria` folder.
3. In your terminal, run the `run_exampleSim.py` script in the `examples` subfolder.
    + Command: `python run_exampleSim.py`
4. If the simulation runs successfully, both `dtk-tools` and `dtk-tools-malaria` are successfully installed in your machine.