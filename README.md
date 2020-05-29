# Docker-kafka
Setup of kafka Cluster via Docker and importing of metrics on Grafana by the use of Prometheus and JMX-Exporter

# Setting up Kafka monitoring using Prometheus and Grafana on Docker

Kafka is a distributed streaming platform that is used publish and subscribe to streams of records. Kafka is used for fault tolerant storage. It replicates topic log partitions to multiple servers. Kafka is designed to allow your apps to process records as they occur. 

## Prerequisites
This tutorial assumes that you have stable build of docker and docker-compose pre-installed. Installation guidelines can be found [here](https://docs.docker.com/compose/install/).

## Initializing your containers

In a suitable location, clone the set up repo. Switch to this directory and call docker-compose to start your containers
```
$ git clone https://github.com/NealMenon/KafkaMonitor.git
$ cd Docker-Kafka-Prom-Graf
$ docker-compose up -d
```
The -d flag starts the containers in background mode. At the point, you may call ``docker-compose ps`` to confirm that your 4 containers are up. Additionally, you may shut down your containers using ``docker-compose stop CONTAINER_NAME``

## Kafka set up
To similiate a kafka set up, we will set up a sample Producer and Consumer under a sample topic. Start by enterring the kafka container using the docker command and creating a topic
```
$ sudo docker exec -it kafka /bin/sh
$ cd /opt/kafka
$ ./bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic helloworld
```
You can see the list of all topics created using 
```
$ bin/kafka-topics.sh --list --zookeeper zookeeper:2181
```
Now that we have a sample topic called helloworld, we will link a producer to this topic. Also, we'll add some data: 

```
$ bin/kafka-console-producer.sh --broker-list localhost:9092 --topic helloworld
>this is the FIRST message
>this is the SECOND message
>this is the THIRD message
>this is the FOURTH message
>this is the FIFTH message

```
In a second terminal opened at the same location, we'll create a consumer on this topic. 
```
$ sudo docker exec -it kafka /bin/sh
$ cd opt/kafka
$ bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic helloworld --from-beginning
```
Creating a script for producer that simulates a constant producer is left to the user as per need.

## Prometheus set up

Opening [Prometheus Dashboard](http://localhost:9090/graph) show a rudimentary view of all Kafka metrics. Confirm that Kafka data is being scraped by visiting [targets](http://localhost:9090/targets), where the endpoint kafka should be up.
### Adding Up metrics to Prometheus Dashboard

Metrics can be observed right from the Prometheus dashboard. Click on the graph optino and enter the prometheus query desired. 


## Grafana set up

### Log in
Login into [Grafana](http://localhost:3000/login) using credentials 'admin' in both user and password fields. You may or may not choose to skip changing the password. 

### Add Data Source
Before setting up a dashboard, Prometheus must be added as a data source. Do so by clicking on the Add you first data source card and choosing Prometheus. Assign a suitable name for this and provide the source URL as ``http://localhost:9090`` and Access type of Browser (not Server). Confirm that everything is set upright with the Save & Test button and return to Grafana dashboard. 

### Add basic dashboard
On the left side of the dashboard, hover over the Create tab and choose Import. Enter ``721`` under Import via Grafana and Load. Once again, assign a suitable name and choose source as the same Prometheus data source we set up above and import. <br>
The scope of the graphs and refresh rate can be adjusted on the top right of the dashboard.


### Adding metrics to Grafana Panel
Additional panels can be added by clicking the add panel option and entering the desired query. Grafana suggests possible queries based on the input provided and complete action with ``shift + enter``.

## Setting up email alerts

### Setting up SMTP on your account to receive alert notifications

We will set up an SMTP server to send alerts to your gmail account from Grafana's dashboard. However, as a security feature, Google requires this to be activated and simply blocks it for accounts with two factor aythentication. &nbsp; <br> 
If this meets ypur needs, enable SMTP access to [your account](https://myaccount.google.com/lesssecureapps) and allowing the use of less secure apps. Follwoing this, install SMTP with: 
```
$ sudo apt-get update
$ sudo apt-get install ssmtp
```

Edit the ssmtp.conf file to connect to gmail account: 
```
$ vi /etc/ssmtp/ssmtp.conf
root=(emailID)
mailhub=smtp.gmail.com.587
FromLineOverride=YES
AuthUser=(emailID)
AuthPass=(passwd)
UseTLS=YES
UseSTARTTLS=YES
```
Run the command &nbsp;&nbsp;``$ echo “Email using CLI” | ssmtp (emailID)``.


### Adding up Alerts Rule to Grafana Panel
Locate and edit grafana.ini to enable sending emails using SMTP:
```
$ vi /etc/grafana/grafana.ini
Change SMTP section as shown below:
[smtp]
enabled=true
host=smtp.gmail.com:587
user=(emailID)
password=(passwd)
;cert_file =
;key_file =
skip_verify = true
from_address = (emailID)
```
#Dashboard Picture
![Dashboard](https://github.com/sac6120/Docker-kafka/blob/master/Screenshot%20from%202020-05-28%2011-05-32.png)

#Alert Notifications Picture
![Alert](https://github.com/sac6120/Docker-kafka/blob/master/Screenshot%20from%202020-05-27%2023-12-42.png)
