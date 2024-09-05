Within this section, we will generate telemetry within our Windows 10 machine; making sure it is being ingested into our Wazuh instance. The telemetry will contain Mimikatz and trigger a custom alert within Wazuh. 
## Configure Log Ingestion 

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

![ossecConf](https://github.com/user-attachments/assets/5f1cf1f0-a765-44df-82fb-1c8092b5864d) 

The location field was derived by the log's Full Name within Windows Event Viewer.

Event Viewer > Application and Service Logs > Microsoft > Windows > Sysmon (or PowerShell) > Operational > Right click > Properties > Copy Full Name

![SysmonFullName](https://github.com/user-attachments/assets/86e1d045-b82a-4e5e-baa5-b65ce810fc1c)

---

Wazuh by default does not log everything. It will only log if an alert or rule is triggered. We can change this setting by altering the configuration file.

**NOTE: Create a backup of the ossec configuration file**

```cp /var/ossec/etc/ossec.conf ~/ossec-backup.conf```

Underneath ```<ossec_config>```, set the following to yes:
- ```<logall>no</logall>```
- ```<logall_json>no</logall_json>```

Save the configuration file and restart the Wazuh service
- ```systemctl restart wazuh-manager.service```

![WazuhOssecConf](https://github.com/user-attachments/assets/8d142893-90da-4d7d-8832-aa1babc75ec9) 

Wazuh will now archive ALL logs in the following directory:
```ls /var/ossec/logs/archives/archives.json```

In order to ingest these logs, we will need FileBeat to accept archive logs. We can achieve this by altering our FileBeat configuration file: ```/etc/filebeat/filebeat.yml```

Locate ```filebeat.modules```
- Set ```archives: enabled ``` to true
- Save config and restart FileBeat ```systemctl restart filebeat```

![fileBeat yml](https://github.com/user-attachments/assets/60fb5311-9f7d-4019-92e2-a2cc9cda5d28)

---
## Configure Wazuh Dashboard

We will need to create an index pattern for the archive logs we just enabled. 

Click on the side menu > Stack Management > Index Patterns

In this menu, we are able to create an index pattern at the top right. 

We will name this index ```wazuh-archive-**```
- For the timefield, we will choose ```timestamp``` (**NOT @timestamp**)

We will now be able to query the archive logs that are generated on the Windows Machine. 

![wazuhArchiveIndex](https://github.com/user-attachments/assets/32f6bb42-2107-4aac-839c-3819284c998a)

## Generate Telemetry
Now that we have our SIEM dashboard setup, we can start to create telemetry on our Windows machine. For this lab, we will be utilizing Mimikatz as our malware sample. 

But first, we need to adjust a few security settings:
- Create an exclusion folder 
	- Allows us to download Mimikatz without Microsoft Defender/AV eradicating it 
- Turn off security settings on browser
	- Allows us to download Mimikatz without browser denying download

Download and extract Mimikatz_trunk.zip into the excluded folder.
- https://github.com/gentilkiwi/mimikatz/releases/tag/2.2.0-20220919

To execute Mimikatz, open up PowerShell with admin privileges within that folder and run the executable. 

![mimikatz](https://github.com/user-attachments/assets/803f6b13-5e26-44a5-a8da-feed54f9bd7c)

We can check Windows Event Viewer to determine if Sysmon has detected the malicious program on our system. 
- Filter Sysmon logs with eventID 1

![mimikatzSysmonLogging](https://github.com/user-attachments/assets/ec2c94e9-3cee-47c0-8875-def9be396b24)

Typically, attackers would not keep the original name of the malware as that is an easy way for security services to detect and eradicate. We can change the file name of Mimikatz.exe to anything else: Bobs_burgers, Linux4Life, wordpress.exe, etc. 

For this example, I chose ```svchost.exe``` as that is a legitimate executable that malware like to disguise as.  

If you were to run the newly named file, check to see if WinEventViewer was able to detect its malicious behavior. 

![svchostDetected](https://github.com/user-attachments/assets/cd2f5eb2-d8ad-4da4-8cb3-6acc1e29cf2e)


## Custom Rule
Now that our archive logs are flowing into our SIEM, we can create a custom rule to detect for the malicious behavior of Mimikatz. 

To create a custom rule click the drop down menu next to the Wazuh logo at the top > Management > Rules > Manage Rules 

Search for sysmon and locate ```0800-sysmon_id_1.xml```
- These are default sysmon rules for eventID 1 created by Wazuh that we can edit for our scenario
- Open and copy the format of a single rule entry

Click Custom Rule and  open```local_rules.xml``` 
- paste the copied rule into here and edit it to match the following

```xml
  <rule id="100002" level="15">
    <if_group>sysmon_event1</if_group>
    <field name="win.eventdata.originalFileName" type="pcre2">(?i)mimikatz\.exe</field>
    <description>Mimikatz Usage Detected</description>
    <mitre>
      <id>T1003</id>
    </mitre>
  </rule>
  ```
- ruleID must start at 100000
- level = severity, so let's just set it to the max of 15
- fieldname = original file name (field from log in previous screenshot)
	- Allows detection of Mimikatz even if the file name was changed (ex: svchost.exe)
- remove ```<options>``` tag as we want ALL of the logs
- ID = MITRE ID
	- T1003: https://attack.mitre.org/techniques/T1003
	- Mimikatz: https://attack.mitre.org/software/S0002/

Ensure indentation matches the rule above. Save the rule, and restart the Wazuh-Manager

Once restarted, run Mimikatz once again to test the new alert.

![MimikatzDetected](https://github.com/user-attachments/assets/5a48ea10-c812-42cb-b8c5-abd6e842885f)
![Mimikatz detected](https://github.com/user-attachments/assets/13abc28a-36ae-4151-b8cc-f0f16798425c)

## Troubleshooting
