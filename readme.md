# SOC Automation Lab
This project will focus on automating and managing security operations workflows. The lab's objective is to simulate real-world SOC activities, from monitoring and alerting to incident response automation, providing hands-on experience with essential tools used in the security operations field. The goal is to build, automate, and maintain SOC workflows that are efficient, scalable, and reproducible. 
## Lab Topology

### Topology Breakdown
- Windows Workstation: 
- Ubuntu Server: This server will be the backbone for hosting our security services. 
- Wazuh: 
- Shuffle: SOAR (Security Operations Automation and Response) platform that automates incident response workflows. Integrating with Wazuh will allow us to initialize and trigger playbooks response to Wazuh alerts. 
- TheHive: Centralized platform for case management and incident response. We will use it to track, manage, and resolve incident reports. 