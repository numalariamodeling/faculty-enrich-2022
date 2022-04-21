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
- Install Python 3.6.0: Download the installer here for [64-bit system](https://www.python.org/ftp/python/3.6.0/python-3.6.0-amd64.exe) or [32-bit](https://www.python.org/ftp/python/3.6.0/python-3.6.0.exe) (Note: this is rare.) Check the checkbox "Add Python 3.6.0 to PATH" to make your life easier.
- Set up environment variables PATH. (Note: If you did the checkbox in previous step, you can skip this)
    + In the Windows Search Bar, type "Environment" and this should typically point you to "Edit the system environment variables", then click on it.
    + Alternatively, go to "Control Panel" -> "System and Security" -> "System" -> "Advanced System Settings"
    + Click "Environment Variables" button
    + In the "System variables" section, look out for the "Path" entry, double click on it.
    + Here you will need to fill in the path to your `python.exe` **and** `pip.exe`
    + This is typically in `C:\Users\(username)\AppData\Local\Programs\Python\Python36` and `C:\Users\(username)\AppData\Local\Programs\Python\Python36\Scripts`. Both are required to be added.
- Now launch your powershell.
    + Easiest way is to type "powershell" in your search bar.
    + If not, you can go to "Start" menu and look for it.
- In the powershell, navigate to your "work folder" (e.g., mine is `C:\Users\(username)\`)
- Install `virtualenv` using `pip`
    + Command: `pip install virtualenv`
- Create a virtual environment of name of your choice. I use `dtk` for mine.
    + Command: `virtualenv dtk`
    + Virtual environment creates a compartment in your computer in which you need to separately install packages of your choice. Two virtual environments can carry different set of packages.
- Activate the virtual environment of your choice.
    + Command: `dtk\Scripts\activate`
    + You *should* notice now your prompt is preceded by `(dtk)` (or name of your environment)
- Now it's time to install `dtk-tools` and its associated packages. Clone the git repository to wherever you want it to be. Navigate over.
    + Say `C:\Users\(username)\work`. 
    + Navigate to the cloned repository, say `C:\Users\(username)\work\dtk-tools`
- Install the tool using `setup_manual.py` script
    + Command: `python setup_manual.py`
    + Yes, there is a `setup.py` script but we typically have better success using the separate script
- If you don't see any noticeable error messages, use `dtk version` command to verify if you successfully installed the `dtk-tools`

### Steps to install `dtk-tools-malaria`

### Verify that you are all set!