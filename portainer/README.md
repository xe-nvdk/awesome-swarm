## Portainer 
This recipe allows you to run Portainer to manage your Docker Swarm Cluster.

Project structure:
```
.
├── portainer.yml
└── README.md
```

[_portainer.yml_](portainer.yml)
``` 
version: '3.2'

services:
  agent:
    image: portainer/agent
    environment:
      # REQUIRED: Should be equal to the service name prefixed by "tasks." when
      # deployed inside an overlay network
      AGENT_CLUSTER_ADDR: tasks.agent
      # AGENT_PORT: 9001
      # LOG_LEVEL: debug
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
    networks:
      - agent_network
    deploy:
      mode: global
      placement:
        constraints: [node.platform.os == linux]

  portainer:
    image: portainer/portainer
    command: -H tcp://tasks.agent:9001 --tlsskipverify
    ports:
      - "9000:9000"
      - "8000:8000"
    volumes:
      - data:/data
    networks:
      - agent_network
    deploy:
      mode: replicated
      replicas: 1
      placement:
        constraints: [node.role == manager]

networks:
  agent_network:
    driver: overlay

volumes:
   data:

```
# How to deploy this recipe

```
$ docker stack deploy portainer -c portainer.yml
Creating network portainer_agent_network
Creating service portainer_agent
Creating service portainer_portainer
```

## Expected result

Check containers are running and the port mapping:

```
$ docker service ls

ID                  NAME                  MODE                REPLICAS            IMAGE                        PORTS
ngvbdywoej8o        portainer_agent       global              2/2                 portainer/agent:latest
uq7zsmd5badq        portainer_portainer   replicated          1/1                 portainer/portainer:latest   *:8000->8000/tcp, *:9000->9000/tcp

```

Stop and remove the containers

```
$ docker stack rm portainer
```
