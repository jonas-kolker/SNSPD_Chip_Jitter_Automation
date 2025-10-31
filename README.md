# <p style="text-align:center"> Automated Jitter Measurement  Documentation </p> 

This code is designed to run with Teledyne LeCroy MAUI™ oscilloscopes. The primary intent is to have a set of functions that simplifies the running of jitter measurements. Specifically, a situation where two signals have corresponding edge events with varying time offsets. The jitter is the variation in these offsets over the course of many trigger events.

Here we outline an overview of connecting the scope and the code functionality.

# Connecting the scope with PC
The scope should be connected to the PC via ethernet on a local network. We additionally used a switch as an intermediate between the PC and scope. Proper connectivity should be confirmed by first pinging the scope from the PC and then attempting to ping the PC from the scope.

### Disable firewall
An important note is that the default scope settings have a firewall enabled that prevents other devices on the local network from interfacing with it. To bypass this you must disable these settings (via the standard Windows 10 interface), which requires admin credentials. By default these are 

<p style="text-align:center"> Username: LCRYADMIN </p> 
<p style="text-align:center"> Password: SCOPEADMIN </p> 

### IP address
When the scope is connected to a local network it will have a distinct IP address. This can be found either by typing "ipconfig" in the windows command line, or in the native scope software by going to Utilities > Utilities Setup > Remote. The IP address should be displayed. 

You will know the scope and PC are properly connected if you can add and see the scope on NI Max under "Network Devices".

### Control Drivers
There are two drivers that can be used to remotely control the scope through python. These are the VISA driver and the ActiveDSO driver. They both work to rovide an interface to transfer data between the PC and scope. 

The `MAUI.py` file is written to work through ActiveDSO. For this to work you must install the ActiveDSO software. A guide can be found [here.](https://www.teledynelecroy.com/doc/using-python-with-activedso-for-remote-communication) This will require a Teledyne Lecroy account. It will take a few buisiness days to get approval if you're registering one for the first time. 

With that being said, VISA has all the same functionality and in some cases may already be installed on the PC. The syntax is quite similar for the two drivers, and re-writing `MAUI.py` to work with VISA shouldn't be overly convoluted (just a little annoying perhaps).

# Overview of Files
See section 5 of the **Automating Jitter Measurements for SNSPD Readout Chip** report for further explainations. These are a brief overview of python files files. 


## `MAUI.py`
The [`MAUI.py`](Scope_Interfacing_Code/MAUI.py) file contains a class that streamlines the connection and control processes outlined in the manual. As mentioned earlier, it will not work without ActiveDSO installed. 

## `scope_stuff_MDP.py`
The [`scope_stuff_MDP.py`](Scope_Interfacing_Code/scope_stuff_MDP.py) file utilizes the class defined in `MAUI.py` for higher level functions directly designed with jitter measurements in mind.

The 3 main functions that get called over the course of a measurement are:

- `extract_waves_multi_seq()` is what will set the acquisition mode and triggers on the scope and then transfer from the 2 channels to the scope as numpy arrays. It also returns the true number of samples as limited by the scope. This is important to have, since it's used by `get_offsets()`
- `get_offsets()` takes two waveforms (each with time and signal data) and then detects rising/falling edges between channels to get the time offset between them. While it's functional, the current implementation is quite inefficient and should be improved. See the **Key Issues to Address** section for more details.
- `make_histogram_and_guassian()` takes offset data and forms it into a histogram. It also has a `stdv_cutoff` parameter that, for nonzero values, will exclude all datapoints more than that many standard deviations from the mean. It will also try to overlay a gaussian who's FWHM is derived from the standard deviation of the filtered data. The accuracy of this fit can be inconsistent - we elaborae in **Key Issues to Address**


## `main.py`
The [`main.py`](main.py) file uses functions from `scope_stuff_MDP.py` to outline a protocol that includes adjusting the chip parameters through a connected arduino. Running this file sweeps a chip parameter, triggers the MAUI scope, pulls burst sequences, computes timing offsets between the two channels, saves raw waveforms and offsets, and exports a histogram.

A directory to store the waveform and jitter data is created at `C:\\LeCroy\\ScopeData`. Within this directory we'll store folders with data corresponding to the specific chip parameter and value being investigated at that moment

Within each waveform folder there will be `num_loops` number of `.npy` files with waveform sequences.

There will also be a `.txt` file that stores offset values. Whie the program is running there will be multiple such files for every loop, but at the end these are all concantenated into one large file for a parameter and value.

## Key Bugs/Quirks to Note
See sections 4 and 6 of the **Automating Jitter Measurements for SNSPD Readout Chip** report for details on data and processings quirks to be aware of. 

## How scope data is saved (from main.py)
See section 6 of the **Automating Jitter Measurements for SNSPD Readout Chip** report for details on the file naming conventions. 

