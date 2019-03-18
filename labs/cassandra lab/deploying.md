---
layout: default
title: Deploying Cassandra
parent: Cassandra Lab
nav_order: 0
---

# Deploying Cassandra on DC/OS

In this exercise we will experience the simplicity of deploying a fully operational Cassandra cluster with a single command.  We will then proceed to validate that the cluster is operational by creating and writing to a new table using CQL.

### Step 1
Deploy Cassandra Service from CLI

From your DC/OS CLI, install the DC/OS Cassandra package from the Catalog.

```
dcos package install --yes cassandra
```

Monitor the deployment of Cassandra.

```
watch dcos cassandra plan show deploy
```

You should see something that looks like the following:

```
deploy (serial strategy) (IN_PROGRESS)
└─ node-deploy (serial strategy) (IN_PROGRESS)
   ├─ node-0:[server] (COMPLETE)
   ├─ node-0:[init_system_keyspaces] (COMPLETE)
   ├─ node-1:[server] (COMPLETE)
   └─ node-2:[server] (PREPARED)
```        

Hit `<Ctrl-C>` to exit the watch command and return back to your prompt.

### Step 2
Cassandra CQL command line

Deploy a container where we will run `cqlsh`.  First download [cqlsh.json](https://raw.githubusercontent.com/tbaums/dcos-mandt-labs/master/labs/5%20-%20Cassandra-labs/cqlsh.json)

```
dcos marathon app add cqlsh.json
```

Run cqlsh.

```
dcos task exec -ti cqlsh cqlsh node-0-server.cassandra.autoip.dcos.thisdcos.directory 9042
```

You should see something that looks like the following

```
Connected to cassandra at node-0-server.cassandra.autoip.dcos.thisdcos.directory:9042.
[cqlsh 5.0.1 | Cassandra 3.11.3 | CQL spec 3.4.4 | Native protocol v4]
Use HELP for help.
cqlsh>
```        

Create a keyspace in Cassandra.

```
CREATE KEYSPACE workshop WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};
USE workshop;
```

Create table.
```
CREATE TABLE students (student_id int PRIMARY KEY, student_name text);
```

Insert record into table.
```
INSERT INTO students (student_id, student_name) VALUES (1, 'John Doe');
```

Verify that the record exists.
```
SELECT * FROM students;
```

You should see the result below.
```
 student_id | student_name
------------+--------------
          1 |     John Doe

(1 rows)
```
