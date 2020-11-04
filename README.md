# Zabbix-workshop
## Requirements
- Install docker and docker-compose.
- Docker 19, Docker-compose 1.18, possibly be familiar with it.
( https://docs.docker.com/get-docker/ + https://docs.docker.com/compose/install/ )
- Could work with other versions.
- Clone this repository.

## Let's explain and setup the platform: 

- First SQL-node to bootstrap the server
- Second SQL-node to join him
- Third SQL-node to join him
- Add the HAPROXY.
- Server + Frontend + Agent 
- Platform up.

## Crash, failover, and restore a node.

- Stop one node and check the failover.
- Restore the node.
- Go back to first node.

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




