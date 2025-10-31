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
See section 5 of the **Automating Jitter Measurements for SNSPD Readout Chip** report. 

## Key Bugs to Note
See section 4 of the **Automating Jitter Measurements for SNSPD Readout Chip** report. 

### How scope data is saved (from main.py)
See section 5 of the **Automating Jitter Measurements for SNSPD Readout Chip** report. 

