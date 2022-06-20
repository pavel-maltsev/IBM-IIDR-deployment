# Deploy IIDR for DB2
## Pre-work

### Create a folder for refresh loader

mkdir /home/db2inst1/refresh_loader

chmod g+w refresh_loader

### Add iidr user to the db2iadm1 group

usermod -a -G db2iadm1 iidr

RESTART THE SESSION TO RETRIEVE LATEST GROUP ASSIGNMENT FOR IIDR

### Create Schema for metadata 

iidr@iidr-virtual-machine:~/Desktop$ su - db2inst1

Password: 

db2inst1@iidr-virtual-machine:~$ db2 create schema CDCmetadata

## Start IIDR for DB2 deployment

### Execute Bash

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ bash /opt/Distrib/setup-iidr-11.4.0.4-5625-linux-x86.bin 

### Follow dialogs for deployment

Locale - English

Select DataStore type - 3-IBM DB2 - (not IBM DB2 for z/OS)

Offering - 1 or 4 for this setup

Path where to install

/home/iidr/agent-db2

Instance folder same as install path - will be created inside

/home/iidr/agent-db2

INSTALL

Run Configuration Tool

Name of new instance - `DB2SOURCE`  (will be visible in Management Console)

Port number  - may keep default - `10901`

Staging Storage Disk - for the test case `20GB` is enough

Maximum memory allowed for instance - keep `default`

Manage encryption profiles? Always answer - `YES` even if not needed

Add encryption profile

Profile name = `none` (no quotes needed)

Engine-to-engine encryption enabled - select `2-Disabled`

Path to keystore - empty

path for trust store - default

password to encrypt trust store - ENTER

trust store type - Default

Use encryption profile which has just been created - select `1-none`

Select DB2 instance - `db2inst1`

Select database name - `1.Source`

configure advanced - `no`

Enter Username = `db2inst1`

Enter password - `db2inst1`

select schema for metadata created in pre-work section

`1. CDCmetadata`

Enter the refresh loader path created in pre-work section

Do you want to start instance DB2SOURCE? = `YES`

## Verify install of agent

./dmconfigurets

`List current instances`

Find your new instance in the list with status RUNNING

## If not Configured within IIDR setup start
```./dmconfigurets```



# Deploy IIDR for Kafka
## Pre-work




## Start IIDR for Kafka deployment

### Execute Bash

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ bash /opt/Distrib/setup-iidr-11.4.0.4-5625-linux-x86.bin 

### Follow dialogs for deployment

Locale - 2. English

-> 1. Install new product

Select DataStore type - 2- Kafka

Offering - 1 or 4 for this setup

Path where to install

/home/iidr/agent-kafka

Confirm Path

Press 1 to agree with License Agreement terms

Instance folder same as install path - will be created inside

/home/iidr/agent-kafka

INSTALL

Run Configuration Tool

Name of new instance - `KafkaTARGET`  (will be visible in Management Console)

Port number  - may keep default - `11701`

Maximum memory allowed for instance - `4096`

Manage encryption profiles? Always answer - `YES` even if not needed

### Start creation of encryption profile 

Add encryption profile

Profile name = `profile2` (no quotes needed) or none as in previous case

Engine-to-engine encryption enabled - select `2-Disabled`

Path to keystore - empty

path for trust store - default

password to encrypt trust store - ENTER

trust store type - Default (JKS Java)

-> 4. Completed management of encryption profiles

--end of creation of encryption profile

### Continue deployment of Agent

Use encryption profile which has just been created - select `1-profile2`

Select y to use JMS or TCP/IP or n to use TCP only communication connection: `n`

Create password for user tsuser

Enter password - `tsuser`

confirm password

Would you like to start instance: `Yes`

-> 6. Exit

# Deploy Access Server

## Execute Bash

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ bash /opt/Distrib/iidraccess-11.4.0.4-11083-linux-x86-setup.bin 

## Follow dialogs for deployment

English

install folder = /home/iidr/access-srv

Agree to License Agreement `1`

LDAP configuration
-> 1. none (Standard Mode)

Default Port for access server: `10101`

User Data Folder: `/home/iidr/access-srv` (same as install folder)

INSTALL

## Start Access Server
iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ nohup ./dmaccessserver >../access.log 2>&1 &
```[1] 12581```

It has to be started prior to creation of the user

## Create user for Access Server

Create Admin User for Access Server/Management Console - requires access server to be started

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ ./dmcreateuser admin "IIDR Admin" "x" inf0Server SYSADMIN TRUE FALSE FALSE

Password here is not important since you will be required to change that with first login to Management Console

# After restart all components can be started with

cd agent-db2/bin/

nohup ./dmts64 -I DB2SOURCE >../agentdb2.log 2>&1 &

cd ../../agent-kafka/bin/

nohup ./dmts64 -I KafkaTARGET >../agentkafka.log 2>&1 &

iidr@ubuntu:~/agent-db2$ cd ../agent-kafka/bin/

iidr@ubuntu:~/agent-kafka/bin$ nohup ./dmts64 -I KafkaTARGET >../agentkafka.log 2>&1 &

iidr@ubuntu:~$ cd access-srv/bin/

iidr@ubuntu:~/access-srv/bin$ nohup ./dmaccessserver >../access.log 2>&1 &

# To stop the agent components

```
cd agent-db2/bin/

dmshutdown [-I <instance_name>]
```
Use this command to gracefully shut down CDC Replication.
  
```
dmterminate [-L <locale>]
```
  
Use this command to terminate all processes for all instances running on a or Linux® server.
Use this command when you cannot completely shut down CDC Replication using the dmshutdown command.
