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

Here either server name if DNS is set up or IP address. IP address can be checked on Ubuntu VM with `ip addr`

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

In the bottom left tab Datastores check status of connection by the icon. 
(Alternative is to use tab Configuration, tab Subscription and select icon Datastore connection)
If the icon is not-connected, right click the DB2Source and select Connect


# DB2 data preparation

Now the time to prepare database for the data replication test

su - db2inst1

db2 connect to SOURCE

db2 -t

this will allow to enter SQL commands with ; separator

create table demo (Code integer not null primary key, FirstName varchar(50), LastName varchar(50));

insert into demo1 values (1, 'name1', 'surname1');

select * from demo1; 
should show that single row


# Back in Management Console

Configuration tab, Subscriptions tab, New Subscription icon click

Name `Subscription1`
Project `Default Project`
Datastores Source `DB2Source`
Datastores Target `KafkaTarget`

`OK` to close window

Dialog:
`Subscription has been created successfully. Do you want to mat tables for the subscription?`
Answer `YES`

Map tables - Select mapping type

`Multiple Kafka Mannings`
`Next`

Select Source Tables
Schema `DB2inst`, table `DEMO1`

`Next` -> `Finish`

On the right side of the screen click on the table name `DB2INST1.DEMO1`. This will open the right pane bottom section wiht mapping details.

Derived columns - those are transformations on the source.

Click `New Derived Column`, insert Name `FullName`, Data type `Varchar`, Length `100`

Evaluation Frequency can be either for `After image only` or `Before and After Image`

Derived column can either use:
1) internal expression language for the rows mostly for data type conversion and basic transformations 
2) call the java procedure in a form or .jar file on the Source Agent side

Click the button to create basic expression for concatination of the First and Last Names.

Expression should look like `%CONCAT(LASTNAME, ' ', FIRSTNAME)`

Use `Verify` button to validate expression.

After clicking `OK` the Mappings window will list a new column which has been just designed.

IMPORTANT

The new column has been created but not yet included in replication. On the `Filtering` tab next to `Column Mappings` tab the `Replicate` checkbox has to be now checked.

Same Filtering tab allows to exclude both the columns and rows from replication. Columns - by unselecting checkboxes and rows by creation of the `Row-filtering expression`.

Now Save the changes by button below.

## Changing Subscription parameters

On the Configurations tab, Subscriptions tab, right click the SUBSCRIPTION1 subscription. 
Select `User exits`
User Exit Type should be default `Java Class`

for the rest 2 lines the documentation has to be used to provide the exact correct string values:

Google for `knowledge center infosphere data replication v11.4`

Select IBM Documentation in results list

On the knowledge center portal left navigation pane open secition `Change data capture(CDC) Replication` - `CDC Replication Engine for Kafka` - `Kafka custom operation processor (KCOP) for the CDC Replication Engine for Kafka` - `Enabling integrated Kafka custom operations processors (KCOP)`

Most common options are first and second in the documentation: "Write audit records in CSV format" or "Write JSON format records"


0:27:00
