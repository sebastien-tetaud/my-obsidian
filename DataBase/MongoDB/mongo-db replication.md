MongoDB replication is a process in which data is copied and distributed across multiple MongoDB servers to ensure high availability, data redundancy, and fault tolerance. It involves maintaining multiple copies of data across different servers, allowing for automatic failover and data recovery in case one server goes offline.

1. **Primary Node:** This is the main server that receives all write operations from clients. It manages all write operations and replicates data to secondary nodes.
2. **Secondary Nodes:** These are additional MongoDB servers that replicate data from the primary node. They act as backups and can serve read operations. They maintain a copy of the data stored in the primary node, and they sync the data continuously to stay up-to-date.
3. **Arbiter (Optional):** An arbiter is an optional component used in replica sets to facilitate the election of a primary node in case of failure. It doesn’t store any data but helps in achieving consensus for the primary node in a scenario where there is a tie.

MongoDB replication works through a process called the replica set. A replica set is a group of MongoDB servers that maintain the same data set. It uses an automatic failover mechanism where if the primary node fails, the replica set automatically promotes one of the secondary nodes as the new primary, allowing the system to continue operating without manual intervention.

The replication process in MongoDB involves the primary node recording all write operations in its oplog (operation log). Secondary nodes continuously pull and apply these operations from the oplog to replicate the data.

Benefits of MongoDB replication include:

1. **High Availability:** If the primary node fails, one of the secondary nodes can be promoted to the primary role, ensuring continuous availability of the database.
2. **Data Redundancy:** Having multiple copies of data across different servers reduces the risk of data loss in case of hardware failure or other issues.
3. **Load Distribution:** Secondary nodes can handle read operations, distributing the read workload and improving overall performance.

MongoDB replication is a fundamental feature for ensuring data reliability, availability, and scalability in distributed database systems.

# MongoDB replication Strategy

## MongoDB Replication on Different servers.

- First step is to add git credential on each server:
    
    - mars
    - plato
    - venus
- Docker Networck choosen is: KiaDvNetwork
    
    ```jsx
    docker network create KiaDvNetwork
    ```
    
- create /hdd/databases folder with:
    
    - primary db: mongodb-kia-primary [plato]
    - secondary db: mongodb-kia-secondary [mars]
    - secondary db: mongodb-kia-secondary [venus]
- Define mongo —replSet name: mongoKiaRep
    

## Plato - Primary DB

**Run the MongoDB containers**: You'll want to run one container as the master and another as the slave, both connected to the same Docker network:

podman run -d -v /hdd/databases/mongodb-kia-primary/:/data/db --userns=keep-id --name mongodb-kia-primary -p 27030:27017 --net KiaDvNetwork localhost/models-forge:latest

## Mars - secondary DB

podman run -d -v /hdd/databases/mongodb-kia-secondary/:/data/db --userns=keep-id --name mongodb-kia-secondary -p 27030:27017 --net KiaDvNetwork localhost/models-forge:latest

## Venus - secondary DB

podman run -d -v /hdd/databases/mongodb-kia-secondary/:/data/db --userns=keep-id --name mongodb-kia-secondary -p 27030:27017 --net KiaDvNetwork localhost/models-forge:latest

## Replication Setup

- Go in Plato Docker container

```bash
podman exec -it mongodb-kia-primary bash
```

- Start Mongosh

```bash
mongosh
```

```bash
rs.initiate()

```

- Add all the the IP address of the secondary DB

```bash
rs.add("10.0.0.5:27030")
rs.add("10.0.0.6:27030")
```

- Check status

```bash
rs.status()
```

- Change Name of the primary DB in the config file with it’s IP

```bash
cfg = rs.conf()
cfg.members[0].host = "10.0.0.1:27030"
rs.reconfig(cfg)
rs.status()
```

![Screenshot from 2023-12-01 13-49-37.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/161a9630-e08d-4ed7-ab36-3ce65a66bee3/8eaac9b4-0dd7-4334-9f17-ad23468a7989/Screenshot_from_2023-12-01_13-49-37.png)

podman run -d -v /hdd/databases/mongodb-kia/:/data/db --restart always --userns=keep-id --name models-forge -p 27020:27017 [docker.io/deltaxailab/base:models-forge-db](http://docker.io/deltaxailab/base:models-forge-db)