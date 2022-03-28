# IBM-IIDR-deployment

This guide will cover core 4 areas of simple deployment scenario of IBM InfoSphere Data Replication on a single VM for a ubuntu server part and client applicaiton on Windows Server 2016

1) deployment of DB2 on Ubuntu
2) deployment of Kafka on Ubuntu
3) deployment and configuration of DB2 IIDR agent
4) deployment and configuration of Kafka IIDR agent
5) deployment and configuration of IIDR Access Server
6) deployment and configuration of IIDR Management Console

Core assumptions:

Server tiers of IIDR is deployed on Ubuntu Desktop 20.04 downloaded from here:


Client tier is deployed on Windows Server 2016 evaluation licence downloaded from here:


Environment configuration is performed on VMWare workstation with parameters of network setup for custom built net8 with NAT