# Setup

This setup uses traefik as a reverse-proxy. For detailed steps go to
[docs](https://docs.traefik.io/user-guide/docker-and-lets-encrypt/).
VM should have ready:
  * docker and docker-compose installed
  * be accessible from outside

Pull this repo and cd into it's directory. Create a file acme.json, which will
store the certificates:

```bash
$ touch acme.json
```

Cp `traefik.toml.template` file to `traefik.toml` and replace all YOUR_* values
with the proper ones.

Create a docker network for web projects:
```bash
$ docker network create web
```

Run the container:

```bash
$ docker-compose up -d
```

Check that the container is running:

```bash
$ docker ps
```

To start an app, you need to attach labels to the container:

```yml
version: '3'
services:
  app:
    image: jetpackpony/IMAGE_NAME
    container_name: CONTAINER_NAME
    restart: always
    networks:
      - web
      - default
    expose:
      - 3000
    labels:
      - "traefik.docker.network=web"
      - "traefik.enable=true"
      - "traefik.frontend.rule=Host:subdomain.yourdomain.tld"
      - "traefik.port=3000"
      - "traefik.protocol=http"
    env_file:
      - .env.prod
    command: npm run start:prod

networks:
  web:
    external: true
```

The app should expose a port and list it in the labels. Docker network should
be the same one that you've created in the begining.
The certificate for `subdomain.yourdomain.tld` will be automatically requested.