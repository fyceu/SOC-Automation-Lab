## Guide
In this step-by-step guide, you will install the Windows workstation and an Ubuntu Server. 
## Install Sysmon
Within your Windows machine, navigate to the following page to download and install Sysmon:
https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

Additionally, we will need the following Sysmon config file:
https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml

Extract Sysmon.zip and move the config file within this folder. 

Open up PowerShell with administrator privileges and run the following command
```.\Sysmon64.exe -i sysmonconfig.xml```

Sysmon should now be installed on the system. You can verify that Sysmon has successfully installed  by locating it within the Services application.
## Install Wazuh

## Install TheHive
