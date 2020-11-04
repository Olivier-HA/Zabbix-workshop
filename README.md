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
- Why ? 
- How ? 
- Carefull to having no more synced nodes.


## Migration from 4.2 to 5.2

- Why this process vs direct server upgrade
- Isolate one node
- Bootstrap a new cluster
- Start a new server
- Upgrade the isolated node
- Check that it's working fine
- Upgrade the whole.

## Limits and drawbacks
## What should we monitor / pay attention to.
## Performance in production.


List the services
docker-compose config --services
docker-compose up -d --build [service]
docker-compose logs [service]
docker-compose stop [service]




