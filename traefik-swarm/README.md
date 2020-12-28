# Traefik Swarm

The files here demonstrate how to use [traefik](https://github.com/traefik/traefik) and [swarm mode](https://docs.docker.com/engine/swarm/) to put your lucky app in production. 

Traefik (pronounced traffic) is a reverse proxy that can automatically discover docker services and route traffic to them based on request rules such as "if path is example.com/api forward this request to the api service". That's pretty awesome but what's more is that Traefik can handle automatically generating and renewing SSL certs too via [Let's Encrypt](https://letsencrypt.org/).

We can use Docker in swarm mode to handle container orchestration, sorta like Kubernetes except we can use normal docker-compose files. By specifying at least 2 replicas (like the included example) we ensure that even while deploying a new image the service doesn't go down since the new image will roll out one replica at a time.

## Usage

### Background

The first step is to have an image that runs your lucky app. You can start with the `minimal-executable` example for this if you don't already have one.

Then we'll add some files for configuring the `lucky` and `traefik` services. Once you have this many extra files for production/deployment in your repo, I recommend you put them in a special location. I usually opt for a `Docker` directory in the root of my app.

Most of these files are for configuring traefik. Traefik supports a lot of different use cases and can be configured in a large number of ways. This example actually uses three of them partially because I think the docs around this are confusing so it's nice to have some examples, and partially because the docs around this are confusing so this is what I came up with.

### Env Vars

One thing that's kind of a pain with demonstrating production set ups is all of the special values. I've tried to capture those as "environment variables" but even that can get complex and application specific. In any case, if you can replace the following `ALL_CAPS` words below with values in these example files then this demo should just work for you.

```
LUCKY_IMAGE=path/to/docker/repo/image_name
LUCKY_TAG=tag_of_image_to_deploy
APP_DOMAIN=luckyapp.com
ADMIN_USER=traefik_admin
HASHED_PASSWORD=$apr1$szNHRdn0$AFBgkBL6En/b0NUBVKv0R1
SWARM_NAME=
*SWARM_NETWORK_NAME=SWARM_NAME_internal
*EMAIL_ADDRESS=me@email.com
```

* `SWARM_NETWORK_NAME` and `EMAIL_ADDRESS` are just in `traefik.yml` and that file doesn't actually accept env variables. The others though you can add to your env and docker should do the right thing regarding variable interpolation.

The admin user and password above let you access the traefik dashboard in production. You can generate the hash of your password using `openssl passwd -apr1 password_here`

### Running

If you have an image and you've tailored these example files to match your project and they are in a `Docker` top-level directory of your lucky app then you can spin the whole thing up like so (using your actual IP address in palce of `$IP_ADDRESS`):

```
docker swarm init --advertise-addr $IP_ADDRESS
docker stack deploy -c Docker/docker-compose.lucky.yml -c Docker/docker-compose.traefik.yml SWARM_NAME --with-registry-auth
```

The first step puts docker into swarm mode and the second deploys multiple swarm services using docker-compose files.

## Futher Reading

If you're into the above but looking for more details you might want to check out: https://github.com/KCErb/lucky-hasura-docker. That's a repo that goes into painstaking detail of doing things like the above but more and including an awesome GraphQL engine called "Hasura".