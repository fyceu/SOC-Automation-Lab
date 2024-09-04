## Guide
In this step-by-step guide, you will install the necessary Security tools and services on Windows 10 Pro workstation and Ubuntu Desktop 22.04 
## Install Sysmon
Within your Windows machine, navigate to the following page to download and install Sysmon:

- https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon

Additionally, we will need the following Sysmon config file:

- https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml

Extract Sysmon.zip and move the config file within this folder. 
Open up PowerShell with administrator privileges and run the following command

```.\Sysmon64.exe -i sysmonconfig.xml```

Sysmon should now be installed on the system. Verify that Sysmon has successfully installed  by locating it within the Services application.
## Installing Wazuh
If this is the first time logging into your Ubuntu VM, ensure you update the system

```sudo apt-get update && apt-get upgrade -y``` 

Once your system is up to date, you can install Wazuh using the following command: 

```curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh && sudo bash ./wazuh-install.sh -a```

**NOTE**: Add the Wazuh admin login credentials somewhere secure. These credentials will be used to access the Wazuh dashboard
## Installing TheHive
In another Ubuntu VM, update and upgrade your system.

```sudo apt-get update && apt-get upgrade -y```

TheHive requires multiple dependencies and services in order to run properly. Install the required dependencies:

```apt install wget gnupg apt-transport-https git ca-certificates ca-certificates-java curl  software-properties-common python3-pip lsb-release```

### Installing Java
---
Install Java by running the following commands

```bash 
wget -qO- https://apt.corretto.aws/corretto.key | sudo gpg --dearmor  -o /usr/share/keyrings/corretto.gpg
echo "deb [signed-by=/usr/share/keyrings/corretto.gpg] https://apt.corretto.aws stable main" |  sudo tee -a /etc/apt/sources.list.d/corretto.sources.list
sudo apt update
sudo apt install java-common java-11-amazon-corretto-jdk
echo JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto" | sudo tee -a /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-11-amazon-corretto"
```

### Installing Cassandra
---
Install Cassandra by running the following commands:

```bash 
wget -qO -  https://downloads.apache.org/cassandra/KEYS | sudo gpg --dearmor  -o /usr/share/keyrings/cassandra-archive.gpg
echo "deb [signed-by=/usr/share/keyrings/cassandra-archive.gpg] https://debian.cassandra.apache.org 40x main" |  sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra
```

### Installing ElasticSearch
---
Install ElasticSearch by running the following commands:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch |  sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
sudo apt-get install apt-transport-https
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/7.x/apt stable main" |  sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
sudo apt install elasticsearch
```
#### Optional 
Create a jvm.options file within ```/etc/elasticsearch/jvm.options.d``` and put the following configurations in that file:

```txt
-Dlog4j2.formatMsgNoLookups=true
-Xms2g
-Xmx2g
```
### Install TheHive
---
Finally, we can install TheHive

```bash 
wget -O- https://archives.strangebee.com/keys/strangebee.gpg | sudo gpg --dearmor -o /usr/share/keyrings/strangebee-archive-keyring.gpg
echo 'deb [signed-by=/usr/share/keyrings/strangebee-archive-keyring.gpg] https://deb.strangebee.com thehive-5.2 main' | sudo tee -a /etc/apt/sources.list.d/strangebee.list
sudo apt-get update
sudo apt-get install -y thehive
```

Default Credentials on port 9000

Admin Credentials: admin@thehive.local:secret