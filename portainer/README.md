## Swarmpit 
This recipe allows you to run Swarmpit to manage a Docker Swarm cluster

Project structure:
```
.
├── swarmpit.yml
└── README.md
```

[_swarmpit.yml_](swarmpit.yml)
``` 
version: '3.3'

services:
  app:
    image: swarmpit/swarmpit:latest
    environment:
      - SWARMPIT_DB=http://db:5984
      - SWARMPIT_INFLUXDB=http://influxdb:8086
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    ports:
      - 888:8080
    networks:
      - swarmpit_net
    deploy:
      resources:
        limits:
          cpus: '0.50'
          memory: 1024M
        reservations:
          cpus: '0.25'
          memory: 512M
      placement:
        constraints:
          - node.role == manager

  db:
    image: couchdb:2.3.0
    volumes:
      - ./couchdb:/opt/couchdb/data
    networks:
      - swarmpit_net
    deploy:
      resources:
        limits:
          cpus: '0.30'
          memory: 256M
        reservations:
          cpus: '0.15'
          memory: 128M

  influxdb:
    image: influxdb:1.8.0
    volumes:
      - ./influxdb:/var/lib/influxdb
    networks:
      - swarmpit_net
    deploy:
      resources:
        limits:
          cpus: '0.60'
          memory: 512M
        reservations:
          cpus: '0.30'
          memory: 128M

  agent:
    image: swarmpit/agent:latest
    environment:
      - DOCKER_API_VERSION=1.35
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - swarmpit_net
    deploy:
      mode: global
      labels:
        swarmpit.agent: 'true'
      resources:
        limits:
          cpus: '0.10'
          memory: 64M
        reservations:
          cpus: '0.05'
          memory: 32M

networks:
  swarmpit_net:
    external: true

```
# How to deploy this recipe

First, you need to create a overlay network
```
$ docker network create --driver=overlay swarmpit_net
```

```
$ docker stack deploy -c swarmpit.yml swarmpit
Creating service swarmpit_influxdb
Creating service swarmpit_agent
Creating service swarmpit_app
Creating service swarmpit_db
```

## Expected result

Check containers are running and the port mapping:

```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE                      PORTS
a2z3rlw1spyn        swarmpit_agent      global              1/1                 swarmpit/agent:latest      
u696qiskvtjp        swarmpit_app        replicated          1/1                 swarmpit/swarmpit:latest   *:888->8080/tcp
rnqjpissl16s        swarmpit_db         replicated          1/1                 couchdb:2.3.0              
jkr1z6jddbyu        swarmpit_influxdb   replicated          1/1                 influxdb:1.8.0        

```

Stop and remove the containers

```
$ docker stack rm swarmpit
```
