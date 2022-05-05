# Verification of the running services and open ports


## Use the folowing commands to enable the service startup after reboot


root@iidr-virtual-machine:/usr/bin# sudo systemctl enable confluent-zookeeper

root@iidr-virtual-machine:/usr/bin# sudo systemctl enable confluent-kafka

root@iidr-virtual-machine:/usr/bin# sudo systemctl enable confluent-schema-registry

Validate the files in the /etc/systemd/system


## Use the folowing commands to check for the services running


root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-zookeeper

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-zookeeper

root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-kafka

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-kafka

root@iidr-virtual-machine:/usr/bin# sudo systemctl start confluent-schema-registry

root@iidr-virtual-machine:/usr/bin# sudo systemctl status confluent-schema-registry