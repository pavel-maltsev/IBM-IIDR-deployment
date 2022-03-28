# Prep-work

## Prepare root user after the system install

iidr@iidr-virtual-machine:~/Desktop$ sudo passwd root
[sudo] password for iidr: 
New password: 
Retype new password: 
passwd: password updated successfully


## Download DB2 distributive to be installed

open Firefox
google search for "ibm fix central"
https://www.ibm.com/support/fixcentral/
search for db2

Search for latest version with UNIVERSAL in the name. In this case it is DB2-linuxx64-universal_fixpack-11.5.7.0-FP000 
Continue
provide IBM ID credentials
download using HTTP

## Install DB2 pre-requisites
iidr@iidr-virtual-machine:~/Desktop$ dpkg -l | grep pam

User root account for deployment of packages
iidr@iidr-virtual-machine:~/Desktop$ su -
Password: 
root@iidr-virtual-machine:~# apt install libpam-runtime:i386
root@iidr-virtual-machine:~# apt install libpam0g:i386

root@iidr-virtual-machine:~# apt search ksh

root@iidr-virtual-machine:~# apt install ksh
root@iidr-virtual-machine:~# dpkg -l | grep aio
ii  libsane-hpaio:amd64                        3.20.3+dfsg0-2                      amd64        HP SANE backend for multi-function peripherals
root@iidr-virtual-machine:~# apt install libaio1
root@iidr-virtual-machine:~# dpkg -l | grep libstd
ii  libstdc++6:amd64                           10.3.0-1ubuntu1~20.04               amd64        GNU Standard C++ Library v3

root@iidr-virtual-machine:~# apt install binutils

# Unpack and install DB2
root@iidr-virtual-machine:~# cd /home/iidr/Downloads/
root@iidr-virtual-machine:/home/iidr/Downloads# ls
v11.5.7_linuxx64_universal_fixpack.tar.gz
root@iidr-virtual-machine:/home/iidr/Downloads# tar -xvzf v11.5.7_linuxx64_universal_fixpack.tar.gz
root@iidr-virtual-machine:/home/iidr/Downloads# ls
universal  v11.5.7_linuxx64_universal_fixpack.tar.gz
root@iidr-virtual-machine:/home/iidr/Downloads# cd universal/
root@iidr-virtual-machine:/home/iidr/Downloads/universal# ./db2_install 
accept license agreement
point to folder
type SERVER when asked what to install
pureScale - answer no
wait till "The execution completed successfully"

# Use information from this link to post-configure DB2
https://github.com/zinal/Db2-Russian/blob/master/docs/Db2ManualInstance.md

root@iidr-virtual-machine:/home/iidr/Downloads/universal# groupadd db2iadm1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# useradd -g db2iadm1 db2inst1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# useradd -g db2iadm1 db2fenc1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# 
root@iidr-virtual-machine:/home/iidr/Downloads/universal# mkdir /home/db2inst1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# chown db2inst1:db2iadm1 /home/db2inst1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# mkdir /home/db2fenc1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# chown db2fenc1:db2iadm1 /home/db2fenc1
root@iidr-virtual-machine:/home/iidr/Downloads/universal# cd /etc/security/limits.d/
root@iidr-virtual-machine:/etc/security/limits.d# vi 30-db2.conf
paste to file 
```
@db2iadm1           soft    nproc           8192
@db2iadm1           hard    nproc           8192
@db2iadm1           soft    nofile          65536
@db2iadm1           hard    nofile          65536
@db2iadm1           soft    stack           16384
@db2iadm1           hard    stack           16384
```
save, exit
root@iidr-virtual-machine:/etc/security/limits.d# cat 30-db2.conf 
```
@db2iadm1           soft    nproc           8192
@db2iadm1           hard    nproc           8192
@db2iadm1           soft    nofile          65536
@db2iadm1           hard    nofile          65536
@db2iadm1           soft    stack           16384
@db2iadm1           hard    stack           16384
```

root@iidr-virtual-machine:/etc/security/limits.d# cd /opt/ibm/db2/V11.5/
root@iidr-virtual-machine:/opt/ibm/db2/V11.5# ./instance/db2icrt -p 25000 -u db2fenc1 db2inst1
DBI1070I  Program db2icrt completed successfully.

Change default mode for user db2inst1 to bash

root@iidr-virtual-machine:/opt/ibm/db2/V11.5# usermod --shell /bin/bash db2inst1
root@iidr-virtual-machine:/opt/ibm/db2/V11.5# su - db2inst1

Manually start DB2

db2inst1@iidr-virtual-machine:~$ db2start
SQL1063N  DB2START processing was successful.

Make sure db2 has started up and listener is on port 25000
```
db2inst1@iidr-virtual-machine:~$ ss -l -n --tcp
State               Recv-Q              Send-Q                             Local Address:Port                              Peer Address:Port              Process              
LISTEN              0                   128                                      0.0.0.0:25000                                  0.0.0.0:*                                      
LISTEN              0                   4096                               127.0.0.53%lo:53                                     0.0.0.0:*                                      
LISTEN              0                   5                                      127.0.0.1:631                                    0.0.0.0:*                                      
LISTEN              0                   128                                            *:657                                          *:*                                      
LISTEN              0                   5                                          [::1]:631                                       [::]:*   
```

## Create database with name Source and Schema mydata
db2inst1@iidr-virtual-machine:~$ db2 create database Source pagesize 32 k
DB20000I  The CREATE DATABASE command completed successfully.
db2inst1@iidr-virtual-machine:~$ db2 connect to source

   Database Connection Information

 Database server        = DB2/LINUXX8664 11.5.7.0
 SQL authorization ID   = DB2INST1
 Local database alias   = SOURCE

db2inst1@iidr-virtual-machine:~$ db2 create schema mydata
DB20000I  The SQL command completed successfully.

## Turn on Archive logs for DB2

db2inst1@iidr-virtual-machine:~$ pwd
/home/db2inst1
db2inst1@iidr-virtual-machine:~$ mkdir archlog
db2inst1@iidr-virtual-machine:~$ cd archlog/
db2inst1@iidr-virtual-machine:~/archlog$ db2 terminate
DB20000I  The TERMINATE command completed successfully.
db2inst1@iidr-virtual-machine:~/archlog$ db2 update db cfg for source using logarchmeth1 'disk:/home/db2inst1/archlog'
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
db2inst1@iidr-virtual-machine:~/archlog$ db2 update db cfg for source using logarchcompr1 on
DB20000I  The UPDATE DATABASE CONFIGURATION command completed successfully.
db2inst1@iidr-virtual-machine:~/archlog$ db2 backup db source
Backup successful. The timestamp for this backup image is : 20220323170618

## Configure DB2 for autostart
db2inst1@iidr-virtual-machine:~/archlog$ db2iauto -on db2inst1
