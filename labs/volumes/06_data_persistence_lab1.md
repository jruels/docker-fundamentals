## Data Persistence Part 1
*Lab Objectives*  
This lab demonstrates how to create volumes. The goal is to show how a volume can allow data to persist across Docker containers by deploying two containers using the same volume definitions.

Lab Structure - Overview
1.	Define a Volume and Run a Container
2.	Run a Second Container with the Same Volume Definitions 
 
### 1. Define a Volume and Run a Container
Step by Step Guide


1.	In the command line, create a single container that defines a volume and specifies the volume mount point:  
`docker run -d --volume /var/lib/cassandra/data --name cass-shared alpine echo Data Container`

2.	Next, use the following command to start a new container called cass1 and inherit the volume definitions from the previous step:  
`docker run -d --volumes-from cass-shared --name cass1 -e MAX_HEAP_SIZE="100M" \`  
`-e HEAP_NEWSIZE="100M" cassandra:2.2`

3.	Start a container from the cassandra:2.2 image. Run a Cassandra client tool and connect it to your running server:   
*Note it may take several seconds for the connection to be established, so wait a minute and try again if it does not work immediately.*  
`docker run -it --rm --link cass1:cass --name cassnew -e MAX_HEAP_SIZE="50M" \`  
`-e HEAP_NEWSIZE="50M" cassandra:2.2 cqlsh cass`

4.	Enter the following commands when prompted by cqlsh>.  
```sql
select * from system.schema_keyspaces where keyspace_name = 'volume_persistence_lab';
```
The machine will output the following code:  
    ```
    keyspace_name | durable_writes | strategy_class | strategy_options
    ---------------+----------------+----------------+------------------ 
    ```

5.	Enter the following commands:  
```sql
sql create keyspace volume_persistence_lab with replication = {'class' : 'SimpleStrategy', 'replication_factor': 1};
```
6.	Enter the following commands again:  
```sql
 select * from system.schema_keyspaces where keyspace_name = 'volume_persistence_lab';
```
The machine will output the following code:  
    ```
    keyspace_name      | durable_writes | strategy_class                              | strategy_options
    --------------------+----------------+---------------------------------------------+----------------------------
    volume_persistence_lab |           True | org.apache.cassandra.locator.SimpleStrategy | {"replication_factor":"1"}
    ```

7.	Now exit the container. The quit command will exit, stop, and delete the container.  
`quit`

8.	Clean up the container cass1.  
`docker stop cass1`  
`docker rm -vf cass1`

### 2. Run a Second Container with the Same Volume Definitions
Step by Step Guide
1.	Use the following command to start a new container called cass2 and inherit the volume definitions from the original cass-shared volume:  
`docker run -d --volumes-from cass-shared --name cass2 -e MAX_HEAP_SIZE="100M" \`  
`-e HEAP_NEWSIZE="50M" cassandra:2.2`

2.	Start a new container from the cassandra:2.2 image. Run a Cassandra client tool and connect it to your running server: Note it may take several seconds for the connection to be established, so wait a minute and try again if it does not work immediately.  
`docker run -it --rm --link cass2:cass --name cassnew -e MAX_HEAP_SIZE="50M" \`  
`-e HEAP_NEWSIZE="50M" cassandra:2.2 cqlsh cass`

3.	Enter the following commands when prompted by cqlsh>.  
```sql
select * from system.schema_keyspaces where keyspace_name = 'volume_persistence_lab';
```
The machine will output the following code:  
    ```
    keyspace_name          | durable_writes | strategy_class                              |
    --------------------+----------------+---------------------------------------------+
    volume_persistence_lab |           True | org.apache.cassandra.locator.SimpleStrategy |
    ```

4.	Exit the container.  
`quit`

5.	Delete containers cass-shared and cass2.  
`docker rm -vf cass2 cass-shared`

### Lab Complete!

