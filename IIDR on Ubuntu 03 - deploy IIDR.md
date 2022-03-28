# Deploy IIDR for DB2
## Pre-work

Create a folder for refresh loader

mkdir /home/db2inst1/refresh_loader

chmod g+w refresh_loader

Add iidr user to the db2iadm1 group

usermod -a -G db2iadm1 iidr

RESTART THE SESSION TO RETRIEVE LATEST GROUP ASSIGNMENT FOR IIDR

Create Schema for metadata 

iidr@iidr-virtual-machine:~/Desktop$ su - db2inst1

Password: 

db2inst1@iidr-virtual-machine:~$ db2 create schema CDCmetadata

## Start IIDR for DB2 deployment

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ bash /opt/Distrib/setup-iidr-11.4.0.4-5625-linux-x86.bin 

Locale - English

Select DataStore type - 3-IBM DB2 - (not IBM DB2 for z/OS)

Offering - 1 or 4 for this setup

Path where to install

/home/iidr/agent-db2

Instance folder same as install path - will be created inside

/home/iidr/agent-db2

INSTALL

Run Configuration Tool

Name of new instance - DB2SOURCE  (will be visible in Management Console)

Port number  - may keep default - 10901

Staging Storage Disk - for the test case 20GB is enough

Maximum memory allowed for instance - keep default

Manage encryption profiles? Always answer - YES even if not needed

Add encryption profile

Profile name = "none" (no quotes needed)

Engine-to-engine encryption enabled - select 2-Disabled

Path to keystore - empty

path for trust store - default

password to encrypt trust store - ENTER

trust store type - Default

Use encryption profile which has just been created - select "1-none"

Select DB2 instance - db2inst1

Select database name - 1.Source

configure advanced - no

Enter Username = db2inst1

Enter password - db2inst1

select schema for metadata created in pre-work section

1. CDCmetadata

Enter the refresh loader path created in pre-work section

Do you want to start instance DB2SOURCE? = YES

./dmconfigurets

List current instances

Find your new instance in the list with status RUNNING

## If not Configured within IIDR setup start
```./dmconfigurets```




# Deploy Access Server
iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ bash /opt/Distrib/iidraccess-11.4.0.4-11083-linux-x86-setup.bin 


## Start Access Server
iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ nohup ./dmaccessserver >../access.log 2>&1 &
```[1] 12581```

Create Admin User for Access Server/Management Console - requires access server to be started

iidr@iidr-virtual-machine:~/iidr-access-srv/bin$ ./dmcreateuser admin "IIDR Admin" "x" inf0Server SYSADMIN TRUE FALSE FALSE

Password here is not important since you will be required to change that with first login to Management Console
