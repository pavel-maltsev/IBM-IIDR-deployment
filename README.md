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

# File content

## Update IIDR on Ubuntu 01 - deploy DB2.md

Core steps to deploy DB2, such as:

1) OS user creation
2) Find and download correct distributive from FixCentral
3) Manage pre-requisites for DB2 deployment
4) Unpack and deploy DB2
5) Post-configrue DB2 with creation additional users
6) Creation of the new schema
7) Turn on Archive logs
8) Configure DB2 for autostart

## IIDR on Ubuntu 02 - deploy Kafka.md

Core steps to deploy Kafka, such as:

1)


IIDR on Ubuntu 03 - deploy IIDR.md


IIDR on Ubuntu - Running services list.md