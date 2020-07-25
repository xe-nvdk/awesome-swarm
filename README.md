# awesome-swarm
![Swarm Logo](https://github.com/xe-nvdk/awesome-swarm/blob/master/logo.png)
> A curated list of compose samples to run in Docker Swarm instances.

These samples provide a starting point for how to integrate different services using in Docker Swarm. You're invited to contribute.

## Index of samples in this repository
If you're contributing, please, don't forget to modify this table and add info about your contribution. 

| Applications         | Description | Author |
|----------------------|-------------|:------:|
| [Portainer](portainer/) | Portainer is a solution to manager Docker, Swarm and Kubernetes clusters | [Ignacio Van Droogenbroeck](https://github.com/xe-nvdk) |
| [Swarmpit](swarmpit/) | Swarmpit is a UI for Docker Swarm cluster management | [Ignacio Van Droogenbroeck](https://github.com/xe-nvdk) |
| [Traefik v2.x](traefik-v2.x/) | This recipe is good for run Traefik v2.x with HTTPS support and redirection | [Ignacio Van Droogenbroeck](https://github.com/xe-nvdk) |
  
## How start with Docker Swarm.
Docker Swarm is include in Docker. The only thing you need to "deploy" Docker Swarm is execute the following command.

```console
docker swarm init
```

The above command will create a manager docker Swarm node. If this your first time playing with Docker Swarm, it's ok have one instance of Swarm running, but if you want to explode all their capabilities it's recommended create a cluster with at least one manager and two worker nodes.

If you want add more workers to the cluster. You need to execute the following in each node:

```
$ docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377
```
## Contribute

To submit a new template, see our [contributing guide] (In Construction)

## Support

If you need some help with any of this compose files, please, open a issue. See our [openning a issue guide] (In Construction)
