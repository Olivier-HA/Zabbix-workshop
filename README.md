# Zabbix-workshop
Requirements
Install docker and docker-compose.
Docker 19, Docker-compose 1.18, possibly be familiar with it.
( https://docs.docker.com/get-docker/ + https://docs.docker.com/compose/install/ )
Could work with other versions.
Clone this repository.

Let's setup our platform : 


-- First SQL-node to bootstrap the server
-- Second SQL-node to join him
-- Third SQL-node to join him
-- Add the HAPROXY.
-- Server + Frontend + Agent 
-- Platform up.

Test doing failover in case of crash : 


-- Stop one node and check the failover.
-- Restore the node.
-- Go back to first node.

Desync a node for specific purposes:
-- Why ? 
-- How ? 
-- Carefull to having no more synced nodes.

- Migration from 4.2 to 5.2

Limits and drawbacks
What should we monitor / pay attention to.
Performance in production.


List the services
docker-compose config --services
docker-compose up -d --build [service]
docker-compose logs [service]
docker-compose stop [service]




