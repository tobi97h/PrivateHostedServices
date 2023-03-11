# Project

This contains the compose definition for my private website stack. It follows the same design patterns as [JitcomHostedServices](https://github.com/tobi97h/JitcomHostedServices).

The following services are contained in this repository:

* Loki
* Grafana
* Personal portfolio page
* Ghost blog
* Personal Api
* Wiki
* Nexus
* Gitea
* Drone
* Hedgedoc
* Personal Nginx Webserver

# Init

In this project I am using a loki instance for logging, which uses basic http auth. To generate this file we use the `htpasswd` command line util like so:

`sudo htpasswd -c vnginx/htpasswd/loki lokilog`, then enter the password you want to use in your logging sinks.

You might need to do `sudo apt-get install apache2-utils` to get the util.

The compose file contains various secrets and config that you want to change. For privacy reasons all secrets are removed and replaced with references to env variables.

To generate the `docker-compose.yaml` set the following env variables and then run `envsubst < docker-compose.template.yaml > docker-compose.yaml`.

```
export WIKI_DB_ROOT_PW=
export GITEA_DB_ROOT_PW=
export GITEA_CLIENT_ID=
export GITEA_SERVER=
export GITEA_CLIENT_SECRET=
export DRONE_RPC_SECRET=
export DRONE_SERVER_HOST=
export DOC_DB_PW=
export DOC_DOMAIN=
```

## Docker loki sink

Loki provides a driver for docker, so you can pump everything to it, directly on container level.

You can install the driver to your docker with:
```bash
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

After that you can use it in your compose files:

```
services:
  mysql:
    restart: always
    image: mysql:8.0.26
    logging:
      driver: "loki"
      options:
        loki-url: ${loki_sink}
        loki-retries: 3
```

Where `${loki_sink}` is an environmental variable that contains the url to your loki instance, including the access credentials.

The url looks like this for example `https://lokilog:123password@loki.tobias-huebner.tech/loki/api/v1/push`

# PS

Personal note: The services in this compose depend on each other, if you cant launch docker-compose up, do the following

```
docker-compose up -d nginx
docker-compose up -d nexus

docker-compose up -d 
```


