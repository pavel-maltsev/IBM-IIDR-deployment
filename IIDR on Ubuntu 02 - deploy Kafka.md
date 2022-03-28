# Deploy Kafka server on Ubuntu

https://docs.confluent.io/4.1.1/installation/installing_cp/deb-ubuntu.html

iidr@iidr-virtual-machine:~/Desktop$ wget -qO - https://packages.confluent.io/deb/4.1/archive.key | sudo apt-key add -

[sudo] password for iidr: 

```OK```

iidr@iidr-virtual-machine:~/Desktop$ sudo add-apt-repository "deb [arch=amd64] https://packages.confluent.io/deb/4.1 stable main"

iidr@iidr-virtual-machine:~/Desktop$ sudo apt-get update && sudo apt-get install confluent-platform-oss-2.11

Kafka has been deployed to /usr/bin

switch to root user

su -

install Midnight commander 

apt install mc

run mc

root@iidr-virtual-machine:~/mc

go to /etc/kafka

modify /etc/kafka/zookeeper.properties

insert

```
tickTime=2000
dataDir=/var/lib/zookeeper/
clientPort=2181
initLimit=5
syncLimit=2
server.1=iidr-virtual-machine:2888:3888
autopurge.snapRetainCount=3
autopurge.purgeInterval=24
```

root@iidr-virtual-machine:/etc/kafka# cd /var/lib/kafka/

create file 'myid'

root@iidr-virtual-machine:/var/lib/zookeeper# vi myid

root@iidr-virtual-machine:/var/lib/zookeeper# cat myid 

root@iidr-virtual-machine:/var/lib/zookeeper# ls

```myid```

root@iidr-virtual-machine:/var/lib/zookeeper# echo "1" > myid

root@iidr-virtual-machine:/var/lib/kafka# cd /etc/kafka/

root@iidr-virtual-machine:/etc/kafka# vi server.properties 

and other files are per link above

root@iidr-virtual-machine:/usr/bin# sudo apt install default-jre

root@iidr-virtual-machine:/usr/bin# sudo apt install default-jdk


root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-zookeeper

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-zookeeper

root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-kafka

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-kafka

root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-schema-registry

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-schema-registry


root@iidr-virtual-machine:/usr/bin# kafka-topics --zookeeper=localhost:2181 --list
```
__confluent.support.metrics
_confluent-metrics
_schemas
```

root@iidr-virtual-machine:/usr/bin# kafka-topics --zookeeper iidr-virtual-machine:2181 --create --topic KafkaTopicTest --replication-factor 1 --partitions 1

```Created topic "KafkaTopicTest".```





