# Kafka Security
Setup and configure Security functionality in Kafka.  
This repo uses OpenSource Apache Kafka deployed on EC2 (AWS).  

[goto setup Kafka](./Setup-Kafka)

[goto setup Kerberos](./Setup-Kerberos)

[goto setup SSL](./Setup-SSL)

# Setup Kafka&Zookeeper in AWS / Azure
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

nc -vz mysuperkafka3.northeurope.cloudapp.azure.com 9092
nc -vz mysuperkafka3.northeurope.cloudapp.azure.com 2181

http://mysuperkafka3.northeurope.cloudapp.azure.com/

cat /home/cloudshell/kafka/logs/server.log

az vm nic list --vm-name myVM --resource-group myResourceGroup

sudo netstat -plnt 

# Kafka manager 
wget https://github.com/yahoo/kafka-manager/archive/master.zip
sudo apt-get install unzip
sudo apt-get install scala
sudo apt-get install default-jdk

unzip master.zip
./sbt clean dist
cd  target/universal/
unzip kafka-manager-2.0.0.2.zip
cd kafka-manager-2.0.0.2/target/universal~

#move cmak folder to /home/cloudshell/
./bin/cmak -Dkafka-manager.zkhosts="mysuperkafka3.northeurope.cloudapp.azure.com:2181"


Starting the service
--------------------

After extracting the produced zipfile, and changing the working directory to it, you can
run the service like this:

$ bin/cmak

By default, it will choose port 9000. This is overridable, as is the location of the
configuration file. For example:

$ bin/cmak -Dconfig.file=/path/to/application.conf -Dhttp.port=8080

Again, if java is not in your path, or you need to run against a different version of java,
add the -java-home option as follows:

$ bin/cmak -java-home /usr/lib/jvm/zulu-11-amd64


Starting the service with Security
----------------------------------

To add JAAS configuration for SASL, add the config file location at start:

$ bin/cmak -Djava.security.auth.login.config=/path/to/my-jaas.conf

NOTE: Make sure the user running CMAK (pka kafka manager) has read permissions on the jaas config file


https://github-wiki-see.page/m/FortFaster/Kafka-Document/wiki/Kafka-Manager-Install
https://linuxways.net/ubuntu/how-to-install-and-configure-apache-kafka-on-ubuntu-20-04/
official DOC : https://github.com/yahoo/CMAK

#!command ubuntu
sudo nano /etc/systemd/system/kafka-manager.service
#!config
[Unit]
Description=Kafka Manager
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
ExecStart=/home/cloudshell/cmak-3.0.0.5 
Type=simple


sudo systemctl daemon-reload
sudo systemctl enable --now kafka-manager.service
sudo systemctl status kafka-manager.service

### SSL on kafka broker ###
### Trustore and keystore for kafka broker and adjust the configuration
## 1/ Certificate Authority 
mkdir ssl 
cd ssl
openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes 
# ca-cert and ca-key is being created .

## 2/ hands-on kafka broker on port 9093 using keytool
export SRVPASS=serversecret
cd ssl 
# keystore jks , CN = DNS of , Pkcs12 the format of saving 
keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname="CN=MYDNS" -storetype pkcs12
 
#output is cert-file 
keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreatesearial -passin pass:$SRVPASS

# output cert-signed for our kafka broker
#check the certificate 
keytool -printcert -v -file cert-signed

# certificate add to keystore
#CREATE truststore in kafka broker
keytool-keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
keytool -keystore -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
keytool -keystore -keystore kafka.server.keystore.jks -alias CARoot -import -file cert-signed -storepass $SRVPASS -keypass $SRVPASS -noprompt


sudo ~/kafka/config/server.properties
ssl.keystore.location=/home/cloudshell/ssl/kafka.server.keystore.jks
ssl.keystore.password=serversecret
ssl.key.password=serversecret
ssl.truststore.location=/home/cloudshell/ssl/kafka.server.truststore.jks
ssl.truststore.password=serversecret

listeners=PLAINTEXT://0.0.0.0:9092,SSL://0.0.0.0:9093
advertised.listners=PLAINTEXT://<YOURDNS>:9092,SSL://<YOURDNS>:9093

#check if its ok for SSL 
sudo systemctl restart kafka
sudo grep "EndPoint" /home/cloudshell/kafka/logs/server.log

### Open port inbound 9093 ###
### SSL setup for Clients  ###
openssl s_client -connect <YOURDNS>:9093

export CLIPASS=clientpass
mkdir ssl 
cd ssl
# chainof trust import CA certificate 
# scp -i ~/kafka-security.pem ubuntu@<DNS>:/home/cloudshell/ssl/ca-cert .
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

keytool -list -v -keystore kafka.client.truststore.jks 

# client.properties 
security.protocol=SSL
ssl.truststore.location=/home/cloudshell/ssl/kafka.client.trustore.jks
ssl.trustore.password=clientpass

#test 
~/kafka/bin/kafka-console-producer.sh --broker-list DNS:9093 --topic kafka-security-topic --producer.config ~/ssl/client.properties





