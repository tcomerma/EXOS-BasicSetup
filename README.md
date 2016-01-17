# EXOS-BasicSetup
Base configuration for Extreme Networks EXOS based Switches
Toni Comerma -Jan 2016

The aim of these scripts is to provide a simple way to do the initial configuration of
Extreme Summit switches. The whole architecture is:

* Management port of the Summit switch connected to a network with DHCP enabled
* DHCP setup to provide, using option 43 the following
	Code 100: TFTP Server IP Address
	Code 101: Config file name (must point to base.xsf)
	Code 102: SNMP Trap Server IP Address

See the following link for dhcp setup: 
 - https://gtacknowledge.extremenetworks.com/articles/How_To/Windows-Server-Auto-Provision-of-Summit-Switches

 * A tftpd server (netsight for instance). Copy the scripts (and exos images/modules) to the TFTP server.

 When the switch boots, it sends a dhcp request that is answered by the server with an IP address, but also with the IP address and config filename to load. After configuring the management interface
 it will download and execute the script.


The base.xsf will in turn:
* Download common.xsf. This file should contain the configuration to apply to all switches.
* It determines the model of the switch (for example, if using the VM, the model will be Summit-PC). Once done, it will try to download the file <model>.xsf. This file should contain the config to apply
to all the switches sharing the same model.
* It determines the System MAC and tries to download a file <mac>.xsf. This file should contain the
config specific for this device (for example, name and IP address).
* Execute the three downloaded scripts in order.

* The script will also upgrade the firmware if the variable "Upgrade" is set to "yes". Note that, as the scripts are executed in order -base, common, model specific, device specific- you can overwrite the variable. I "Upgrade" is set to "yes", you must inform the variables:
  - targetVER: Version that you want to apply
  - targetIMG name of the file containing the image to download
  - targetMOD List of modules to upgrade at the same time (for examle ssl, etc...)
As this files are platform dependant, it's better to set up at <model>.xsf if all devices share the same version.  

Disclaimer: This is more a proof of concept than a finished product. Test it, hack it, fork it.