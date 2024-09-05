In this section, we will tweak a few configuration settings of the services we installed in the previous step. These changes will allow us to host our TheHive instance on our Ubuntu server, allowing us to access the TheHive dashboard from our local machine.
## Cassandra

Change configuration file of cassandra

located at: ```/etc/cassandra/cassandra.yaml```
Set your public IP address of the Ubuntu server for the following: 
- listening_address
- rpc_address
- seeds attribute within seed_provider (keep port 7000)

## ElasticSearch

Change the configuration file of Elastic Search

located at: ```/etc/elasticsearch/elasticsearch.yml```
- Uncomment cluster name (I changed name to SOCAutomation)
- Uncomment node.name
- Uncomment network.host 
	- set to local IP of Ubuntu server
- Uncomment http.port
- Uncomment cluster.initial_master_nodes
	- remove node-2 if applicable

## TheHive
Change the configuration file of TheHive

located at: ```/etc/thehive/application.conf```
- change db.janusgraph hostname to public IP of Ubuntu server 
- set cluster-name to cluster name set in ElasticSearch config
- change index.search hostname to public IP of Ubuntu server
- change application.baseUrl to public IP of Ubuntu server (port 9000)

## Running services
Run the following commands consecutively to start TheHive

```systemctl start cassandra```

```systemctl start elasticsearch```

```systemctl enable elasticsearch```

```systemctl start thehive```

```systemctl enable thehive```

If done successfully, you should be able to access TheHive dashboard on your local machine using the public IP address of your Ubuntu server. 

## Troubleshooting

Ensure that all three services are running before accessing TheHive on your local machine. 

You can check the status of each service using the following commands:
- ```systemctl status cassandra```
- ```systemctl status elasticsearch```
- ```systemctl status thehive```

If you are still having issues, try completing the [OPTIONAL](https://github.com/fyceu/SOC-Automation-Lab/blob/wip/1.%20Installation/Install.md#optional) section from the previous step. 