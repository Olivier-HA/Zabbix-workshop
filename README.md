# Zabbix-workshop : Zabbix + Percona XTRADB Cluster.
## Requirements
- Install docker and docker-compose.
- Docker 19, Docker-compose 1.18, possibly be familiar with it.
( https://docs.docker.com/get-docker/ + https://docs.docker.com/compose/install/ )
- Could work with other versions.
- Clone this repository.
- Suggested while working, after building the first container.

``` 
docker-compose logs -f

```

## Introduction

Percona Server + Xtrabackup + Galera librairies. 

1. What to expect : Resiliency + read scalability  ( to manage ! ) + flexibility and Zabbix high load capable. ( currently 35-40k nvps ) 
2. What not to expect: Write scalability ( Data written everyhwere, slower node can slow the others ) 
3. Usage of Docker here : lab purpose to make everyone able to follow during / after the workshop.


## Let's explain and setup the platform: 

1. First SQL-node to bootstrap the server

```
docker-compose config --services
docker-compose up -d --build db-sql-node-1
```

2. Second SQL-node to join him

```
docker-compose up -d --build db-sql-node-2
```

3. Third SQL-node to join him

```
docker-compose up -d --build db-sql-node-3
```

4. Add the HAPROXY.

```
docker-compose up -d --build proxy

Check http://ip:9000/haproxy_stats
```


5. Server 4.2 time

```
docker-compose up -d --build zabbix-server-42

```
6. Frontend 4.2 now

```
docker-compose up -d --build zabbix-frontend-42

```
7. Agent is optional here.

We should have the platform up!
Check that you have the proper acces to the haproxy stat page http://ip:9000/haproxy_stats and the zabbix frontend http://ip:8081/



## Crash, failover, and restore a node.

1. Stop one node and check the failover.

```
docker-compose stop db-sql-node-1
```


2. Restore the node.

```
      CLUSTER_JOIN: 192.168.13.42,192.168.13.43
      #CLUSTER_JOIN: 
      docker-compose up -d --build db-sql-node-1
```

3. Go back to first node.

```
docker-compose restart proxy
```

## Desync a node for specific purposes:
1.  Why ? Slower node or specific select requests needs, avoid flow control, replication ON, no more write requests.
```
mysql --user=root --password=murloc --host=127.0.0.1 -P 3306  zabbix -e "SHOW variables LIKE 'wsrep_provider_options';"
mysql --user=root --password=murloc --host=127.0.0.1 -P 3306  zabbix -e "SHOW GLOBAL STATUS LIKE 'wsrep_%';"
```
2.  How ? 
```
mysql --user=root --password=murloc --host=127.0.0.1 -P 3306  zabbix -e "SHOW GLOBAL STATUS LIKE 'wsrep_%';"
mysql --user=root --password=murloc --host=127.0.0.1 -P 23306  zabbix -e "set global wsrep_desync=ON;"
mysql --user=root --password=murloc --host=127.0.0.1 -P 23306  zabbix -e "SHOW variables LIKE 'wsrep_des%';"
mysql --user=root --password=murloc --host=127.0.0.1 -P 23306  zabbix -e "set global wsrep_desync=OFF;"
```
3. Carefull to having no more synced nodes.


## Migration from 4.2 to 5.2

1. Why this process vs direct server upgrade

Prepare for upgrade. 
Keep the service running : monitoring still available.
Avoid upgrade failure.
Pre-test the upgrade.

2. Isolate one node

```
docker-compose stop db-sql-node-3
docker ps -a | grep node-3
docker inspect container-id | grep /var/lib/mysql -B 1
vim /var/lib/docker/volumes/e6409284da5abe2436dd72d4fcc44d59d8deffdd6f0da0b6b535a1781f06ffa0/_data/grastate.dat
safe to bootstrap to 1
Change the cluster name to CLUSTER_NAME: 'zabbix-db-cluster-52'
empty cluster join
```

3. Bootstrap a new cluster

```
docker-compose up -d --build db-sql-node-3
```


4. Start a new server

```
docker-compose up -d --build zabbix-server-52
```

5. Upgrade the isolated node

```
Automated step at the start of the server.
```

6. Check that it's working fine

``` 
Edit DB host for frontend 52
docker-compose up -d --build zabbix-frontend-52
Check the frontend 52 and find back your data
```


7. What is next is your choice : 

Make another node join the new cluster ? ( consider clearing the data first for fresh configuration ) 
Use another Haproxy and failover to the second server so your agents will communicate with the new one ? 
In our use habits, we just use the second server to upgrade the DB, then upgrade our usual server + point to the fresh upgraded DB and start making the other nodes join.

## Additional subjects
1. ProxySQL could nice features instead of Haproxy ( not tested for now ) : detection of nodes state, point writes to synced or specific node, balande the read requests, manage the failover better. Not tested for production in our case but we would.
2. Bufferpool + innodblogfile = eat as much RAM as possible but for good reasons.
3. Possible upgrade for the future : timescale DB when the multi-node version is out ? 
4. Look at your queues ! 
5. For now how it is going in production : All Fine for 35-40k nvps with 32 core / 256 GB of ram and io1 10k iops volumes for now and for a while.





