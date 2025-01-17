+++
title = "Docker"
chapter = false
weight = 1
+++

## 🐳

The easiest way to get started is to use [docker](https://www.docker.com/). This guide won't cover installing docker, so make sure you have the docker daemon installed and running. Since Ogmios requires the presence of a Cardano node, we'll be using [docker-compose](https://docs.docker.com/compose/) to orchestrate both services. A compose file is available on the Ogmios repository, get it via:

```console
$ git clone --depth 1 git@github.com:KtorZ/cardano-ogmios.git
$ cd cardano-ogmios
```

Then, starts the components stack using:

```console
$ docker-compose up
```

👆This will run and connect together:

- A [Cardano node](https://github.com/input-output-hk/cardano-node/), connected to mainnet.
- An Ogmios server using the [latest Dockerhub build](https://hub.docker.com/r/ktorz/ogmios), listening to localhost on port :1337.

To build the Ogmios image from sources, pass the `--build` flag. This is useful if you need a different version than the latest one available on Dockerhub.  

{{% notice tip %}}
If you're building locally using `docker build`, make sure to leverage existing cache steps from Docker Hub setting the env `DOCKER_BUILDKIT=1` and passing `--cache-from ktorz/ogmios:latest`. For `docker-compose build` or `docker-compose up --build`, **also** set`COMPOSE_DOCKER_CLI_BUILD=1`. A full build of Ogmios without cache may take up to 45 minutes!
{{% /notice %}}

### Configuration

The compose file allows for minimal (albeit useful) configuration parameters via environment variables:

Variable      | Description                                                                                    | Values                 | Default   
---           | ---                                                                                            | ---                    | ---        
`NETWORK`     | Which Cardano network to connect to. This impacts both Ogmios and the underlying Cardano node. | `mainnet`, `testnet`   | `mainnet`    
`OGMIOS_PORT` | Which ports to listen to (both for WebSockets and health endpoints)                            | Any valid port number. | `1337`    

{{% notice info %}}
Ogmios doesn't use any form of persistent storage, but cardano-node does. The mainnet and testnet databases are not compatible, so it is recommended to instrument docker-compose to use different namespaces for different networks (so that you can switch from one another without risking any database conflicts). Compose can do this easily by passing an extra flag: `--project-name`.
{{% /notice %}}

For example, for running cardano-node + ogmios on the testnet, listening to tcp/1338, do:

```console
$ NETWORK=testnet OGMIOS_PORT=1338 docker-compose --project-name cardano-ogmios-testnet up
```
