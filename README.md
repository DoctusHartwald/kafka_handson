# Kafka Security
Setup and configure Security functionality in Kafka.  
This repo uses OpenSource Apache Kafka deployed on EC2 (AWS).  

[goto setup Kafka](./Setup-Kafka)

[goto setup Kerberos](./Setup-Kerberos)

[goto setup SSL](./Setup-SSL)


# Setup Kafka&Zookeeper in AWS
For this course we will use a single-node Kafka installation on an EC2 instance in AWS.
The setup consists of the following steps:

1.) [Launch EC2 instance on AWS](./Launch-EC2.md)

2.) [Install Apache Kafka](./Install-Kafka.md)

ln -s kafka_2.11-2.2.0 kafka
## Check zoopkeeper is stopped 
## setup Services 

Setup  -- sudo vi /etc/systemd/system/zookeeper.service
Setup -- sudo vi /etc/systemd/system/kafka.service

## enable system services 
sudo systemctl enable zookeeper

Created symlink /etc/systemd/system/multi-user.target.wants/zookeeper.service → /etc/systemd/system/zookeeper.service.

sudo systemctl enable kafka
Created symlink /etc/systemd/system/multi-user.target.wants/kafka.service → /etc/systemd/system/kafka.service.

## check current status 
sudo systemctl start zookeeper
sudo systemctl restart zookeeper

sudo systemctl status zookeeper

## start services 
sudo systemctl status kafka
sudo systemctl start kafka
sudo systemctl restart kafka
sudo systemctl daemon-reload

sudo service zookeeper start 


sudo journalctl -u kafka 

tail -n 10 ~/kafka/logs/zookeeper.out
tail -n 10 ~/kafka/logs/zookeeper.out

3.) Set [service scripts](./Set-Servicescripts.md)


# Test Producer consumer
## topic creation to zookeeper
## publish data from the newly created topic 
## consuming data from the newly created topic 

## add port 2181 Zookeeper access / 9092 Kafka plain text port 
#configure NSG
#configure DNS listner 
sudo /home/cloudshell/kafka/config/server.properties
advertised.listeners=PLAINTEXT://mysuperkafka.northeuropecloudapp.azure.com:9092
zookeeper.connect=mysuperkafka.northeuropecloudapp.azure.com:2181


sudo apt-get install -y net-tools netcat tar openjdk-8-jdk

echo "ruok" | nc mysuperkafka.northeuropecloudapp.azure.com 2181

nc -vz localhost 9092
nc -vz localhost 2181

cat /home/cloudshell/kafka/logs/server.log


