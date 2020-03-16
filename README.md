# ELK-Setup
Documentation:
* How To Install Elasticsearch, Logstash, and Kibana (Elastic Stack) on Ubuntu 18.04
https://www.digitalocean.com/community/tutorials/how-to-install-elasticsearch-logstash-and-kibana-elastic-stack-on-ubuntu-18-04

* Install and configure Curator on Ubuntu 18.04
https://medium.com/@vidyasonawane20/install-and-configure-elasticsearch-curator-to-delete-the-old-indices-acee09344852

* Download and install filebeat
https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-installation.html
`curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.0-amd64.deb
sudo dpkg -i filebeat-7.6.0-amd64.deb`


[Solved]

1.	 Kibana is not accesible from other servers
	  - Check the kibana port whether it is allowed to connect from other hosts
    - Change the server.host which is localhost by default in kibana.yml file to 0.0.0.0 or IP:5601
	  - then try `curl "http://IP:5601/api/status"` from other host


2.	After configuring the filebeat, we get this error, `Failed to import dashboard: Failed to load directory /usr/share/filebeat/kibana/7/dashboard`
	- Restart the kibana using
		-  `sudo systemctl restart kibana`
		-  `sudo systemctl enable kibana`
		-  `sudo systemctl status kibana`

3.	Curator job is not running
	- give the full path of curator and bash executables like `/opt/elastic-searchcurator/curator` in curator.sh and `/bin/bash` in crontab -e

4. 	`Exiting: error initializing publisher: error initializing processors: Cannot connect to the docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?`
	- install filebeat by downloading the deb package and avoid using apt-get.

5.	Connection timeout error while connecting to elasticsearch from other host. 
	- try changing the network.host in elasticsearch.yml file to 0.0.0.0 
	- check the firewall, it should be inactive.
	- Open the port 9200 in AWS security group

6.	Elasticsearch stopped working
	- To trobleshoot the error, see the logs of elasticsearch which is stored in `/var/log/elasticsearch` directory.
	- The error was __outOfMemoryError: Java Heap Space__
	- Install **monit** to monitor the memory usage and configure the elasticsearch to restart when it reaches the memory usage of 80% 

6.	Kibana is giving the error : FORBIDDEN/12/index read-only / allow delete (api)]
	- Run the following query in kibana dev tool
	
	```
	PUT filebeat-*/_settings
	{
	"index": {
	"blocks": {
	"read_only_allow_delete": "false"
	}
	}
	}
	```
