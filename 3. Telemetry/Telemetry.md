Within this section, we will generate telemetry within our Windows 10 machine; making sure it is being ingested into our Wazuh instance. The telemetry will contain Mimikatz and trigger a custom alert within Wazuh. 
## Configuration 

Again with some more configuration... As of right now, any logs being created on our Windows machine are not being ingested within Wazuh. We can fix that by adjusting the configuration file: 

**NOTE**: Please make a copy of this configuration file!

```C:\Program Files (x86)\ossec-agent\ossec.conf```

Within this configuration file, we will need to add in Sysmon logs and PowerShell logs. 

```text
  <!-- Log analysis -->
  <localfile>
    <location>Microsoft-Windows-Sysmon/Operational</location>
    <log_format>eventchannel</log_format>
  </localfile>

  <!-- Log analysis -->
  <localfile>
    <location>Microsoft-Windows-PowerShell/Operational</location>
    <log_format>eventchannel</log_format>
  </localfile>
```
![[ossecConf.png]]
The location field was derived by the log's Full Name within Windows Event Viewer.

Event Viewer > Application and Service Logs > Microsoft > Windows > Sysmon (or PowerShell) > Operational > Right click > Properties > Copy Full Name
![[SysmonFullName.png]] 
