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

### Configure Access Manager

Switch to Access Manager tab

At the bottom the User Management section displays this only user with details

At the upper section under Datastore Management use icon to add New Datastore

Name `agentdb2`
Host Name `192.168.171.131`
Port (value from agent-db2 on Ubuntu): `11901`

Check with PING button if valid.

If not valid, check on Ubuntu
`ss -l -n --tcp`

Agent db2 should be started at this point, same as DB2 without which agent won't start

`cd agent-db2/bin/
nohup ./dmts64 -I DB2SOURCE >../agentdb2.log 2>&1 &
cd ../../agent-kafka/bin/
nohup ./dmts64 -I KafkaTARGET >../agentkafka.log 2>&1 &`

`db2start`
