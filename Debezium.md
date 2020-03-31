# Debezium
Debezium is a distributed platform that turns your existing databases into event streams. Applications can monitor respond immediately to each row-level change in the database.
## 1. Starting the services
1. Start Zookeeper
2. Start Kafka
3. Start a MySQL database
4. Start a MySQL command line client
5. Start Kafka Connect

## 2. Deploying MySQL connector
1. use curl command to register the Debezium MySQL connector monitoring the MySQL binlog. 
Request: POST
endpoint: /connectors
data:    json describes new connector
2. Watch starting up

## 3. Viewing change events
1. Viewing a create event:
compare before and after structures
2. Updating the database and viewing the update event
3. Deleting a record in the database and viewing the delete event
4. Restarting Kafka Connect and changing the database
