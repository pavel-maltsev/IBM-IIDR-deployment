# Deploy Management Console

## Important

This can be performed only on Windows workstation!!!

Follow standard UI wizard steps to deploy Management Console

Make sure that all steps from previous documents are completed

## Launch Management Console

### One time pre-work

User Name `admin`
Password  - the one setup on previous file with Access server deployment `inf0Server`

Server Name `192.168.171.131` in my case

Here either server name if DNS is setup or IP address. IP address can be checked on Ubuntu VM with `ip addr`

Port Number (also is taken from results for Access Server deployment) `10101`

At the first login console will require to change password for admin user of Access Server

Enter new password `admin` - that is incorrect - requires 2 alphabetic and 2 non-alphabetic characters. 

e.g. `adminx01`

## Configure Access Manager

Switch to Access Manager tab

At the bottom the User Management section displays this only user with details

### Create DB2 Datastore

At the upper section under Datastore Management use icon to add New Datastore

Name `DB2Source`

Host Name `192.168.171.131`

Port (value from agent-db2 on Ubuntu): `10901`

Check with PING button if valid.

If not valid, check on Ubuntu
`ss -l -n --tcp`

Agent db2 should be started at this point, same as DB2 without which agent won't start

```
su - db2inst1
db2start
```

```
cd agent-db2/bin/
nohup ./dmts64 -I DB2SOURCE >../agentdb2.log 2>&1 &
cd ../../agent-kafka/bin/
nohup ./dmts64 -I KafkaTARGET >../agentkafka.log 2>&1 &
```
to check status the logs can be viewed:
```
cd /home/iidr/agent-db2
cat agentdb2.log
```
Content should be `IBM Data Replication is running`


### Create Kafka Datastore

At the upper section under Datastore Management use icon to add New Datastore

Name `KafkaTarget`

Host Name `192.168.171.131`

Port (value from agent-kafka on Ubuntu): `11701`

Check with PING button if valid.

###

At this point on the Access Manager tab 2 Datastores should be visible: DB2Source with type Dual and KafkaTarget with type Target

In access Manager right click on KafkaTarget and select Assign User
DBlogin ```tsuser```
Password 2 times ```tsuser```

In access Manager right click on DB2Source and select Assign User

Select Users
default one `admin` - OK 
On right side Connection Managemen - DB2Source tab double-click admin
in window opened insert values for:

DB Login : `db2inst1`
DB Password 2 times : `db2inst1` ?? 

`OK`
window closed

In the bottom left tab Datastores check status of connection by the icon. If the icon is not-connected, right click the DB2Source and select Connect


File #3 0:11:23

