# Docker-kafka
Setup of kafka Cluster via Docker and importing of metrics on Grafana by the use of Prometheus and JMX-Exporter

Follow the above commands on Terminal
Go to Directory
On Terminal-1 run these commands
```console
sudo docker-compose up -d
sudo docker exec -it kafka /bin/sh
cd  /opt
cd kafka
./bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic sachin
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic sachin
```

On new terminal run:-
```console
sudo docker exec -it kafka /bin/sh
cd  /opt
cd kafka
./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic sachin
```
#Dashboard Picture
![Dashboard](https://github.com/sac6120/Docker-kafka/blob/master/Screenshot%20from%202020-05-27%2023-12-42.png)

#Alert Notifications Picture
![Alert](https://github.com/sac6120/Docker-kafka/blob/master/Screenshot%20from%202020-05-28%2011-05-32.png)
