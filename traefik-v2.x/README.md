## Traefik v2.x with HTTPS support
This recipe allows you to run Traefik v2.x and a hello world containers as example in Docker Swarm.

Project structure:
```
.
├── traefik.yml
└── hello_world.yml
└── README.md
```

[_traefik.yml_](traefik.yml)
``` 
version: "3.3"

services:
  traefik:
    image: "traefik:latest"
    command:
      - --entrypoints.web.address=:80
      - --entrypoints.websecure.address=:443
      - --providers.docker=true
      - --providers.docker.swarmMode=true
      - --providers.docker.exposedbydefault=false
      - --providers.docker.network=publica
      - --api
      - --log.level=DEBUG
      - --certificatesresolvers.leresolver.acme.httpchallenge=true
      - --certificatesresolvers.leresolver.acme.email=<tu-email>
      - --certificatesresolvers.leresolver.acme.storage=.acme/acme.json
      - --certificatesresolvers.leresolver.acme.httpchallenge.entrypoint=web
    ports:
      - "80:80"
      - "443:443"
    networks:
      - publica
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "./acme.json:/acme.json"
    deploy:
      labels:
      # global redirect to https
      - "traefik.http.routers.http-catchall.rule=hostregexp(`{host:.+}`)"
      - "traefik.http.routers.http-catchall.entrypoints=web"
      - "traefik.http.routers.http-catchall.middlewares=redirect-to-https"
      - "traefik.http.routers.http-catchall.middlewares=redirect-to-https@docker"

      # middleware redirect
      - "traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https"

networks:
  publica:
    external: true

```
# How to deploy this recipe

First, you need to create a overlay network
```
$ docker network create --driver=overlay publica
```

```
$ docker stack deploy -c traefik.yml traefik
Creating service traefik_traefik
```

## Expected result

Check containers are running and the port mapping:

```
$ docker service ls

ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
n2rcb0t4365p        traefik_traefik     replicated          1/1                 traefik:latest      *:80->80/tcp, *:443->443/tcp
```

Stop and remove the containers

```
$ docker service rm <id-container>
```