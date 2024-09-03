# SOC Automation Lab
This project will focus on automating and managing security operations workflows. The lab's objective is to simulate real-world SOC activities, from monitoring and alerting to incident response automation, providing hands-on experience with essential tools used in the security operations field. Additionally, this project will showcase the provisioning and implementation of different security tools and services, typically handled by a Security Engineer. The goal is to build, automate, and maintain security operation workflows that are efficient, scalable, and reproducible. 
## Lab Topology

### Topology Breakdown
- SOC Analyst: Local machine used to monitor and remotely manage services and tools
- Windows Workstation: Device used to stage malicious activity
- Ubuntu Server: Serves as the backbone of the security services -- hosting Wazuh, TheHive, and Shuffle.
- Wazuh: SIEM and XDR platform that will provide log collection, monitoring and intrusion detection for the lab. 
- Shuffle: Security Orchestration, Automation, and Response (SOAR) platform that automates incident response workflows. Integrating with Wazuh allows the initialization and triggering of playbooks in response to Wazuh alerts. 
- TheHive: Centralized platform for case management and incident response -- Used to track, manage, and resolve cases that are generated by alerts.
## Workflow
