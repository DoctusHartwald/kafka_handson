# kafka_handson
kafka hands  on security
ssh -i kafka-security.pem ubuntu@ec2-18-196.2dsddssds.amazon.com
ssh into EC2 instances 

##### TASKS
#1 /  download Kafka tar.gz
#2 / Start Zookeeper 
#3 / Start kafka 

# 1&2
sudo apt-get install -y wget  net-tools netcat tar openjdk-8-jdk
wget http://mirror.softaculous.com/apache/kafka/1.0.0/kafka_2.11-1.0.0.tgz
tar -xvf kafka_2.11-1.0.0.tgz
ln -s kafka_2.11-1.0.0 kafka

# start Zookeeper in the background
/kafka/bin/zookeeper-server-start.sh -daemon ~/kafka/config/zookeeper.properties
tail -n 5 ~/kafka/logs/zookeeper.out
# it will bind to 2181 port default port 

echo "ruok" | nc localhost 2181 ; echo
imok

#3/ start kafka
~/kafka/bin/kafka-server-start.sh -daemon ~/kafka/config/server.properties
tail -n 10 ~/kafka/logs/kafkaServer.out
