Creation of Swarm Cluster

Spin of three Virtual Machines with Ubuntu 16.0 OS. (Can it be tested on CentOS as well)

Install docker and docker compose in all the virtual machines
	
	    Create a dockerinstall.sh
	
		#!/bin/bash

		apt-get update
		apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
		apt-add-repository "deb https://apt.dockerproject.org/repo ubuntu-xenial main"
		apt-get update
		apt-get -y install docker-engine
		systemctl enable docker
		apt-get -y install python-pip
		pip install docker-compose
		
		Make the script executable
		
		$chmod u+x dockerinstall.sh
		
		Run the script
		
		./dockerinstall.sh
		
		
Clone the project 

    $git clone https://experiencedevops@bitbucket.org/experiencedevops/swarm-monitoring-grafana.git

Create the directory for volume mapping

    $mkdir /var/dockerdata/prometheus
    $mkdir /var/dockerdata/prometheus/data
    $mkdir /var/dockerdata/prometheus/rules	

Copy the prometheus configuration files to  /var/dockerdata/prometheus

    $mv /swarm-monitoring-grafana/configs/prometheus.yml /var/dockerdata/prometheus/

Initiate Docker Swarm

    $docker swarm init --advertise-addr MASTERNODE_PUBLIC_IP

The above command produces joining token to be used in worker nodes to join the swarm cluster.

Check the nodes in the swarm

    $docker node ls
	
Create the networks

	$ docker network create -d overlay monitoring
	$ docker network create -d overlay logging

Deploy the application stack

    $cd app_stack
    $docker stack deploy -c docker-compose.yml appstack

Deploy the monitoring stack

    $cd monitoring_stack
    $docker stack deploy -c docker-compose.yml monitoring_stack

Check the stack

    $docker stack ps appstack
    $docker stack ps monitoring_stack

Grafana Configuration
Access Grafana in the browser by hitting http://MASTERNODE_PUBLIC_IP:3000

Add Prometheus datasource
DataSource Name : Prometheus
Choose connection type : Prometheus
URL : http://MASTERNODEIP:9090
Access : Direct
Click Save & Test

Add New Dashboard
Import the docker-swarm-container-overview_rev23.json
Choose "Prometheus" as the datasource
Click on Save



