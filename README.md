# Setup

This setup uses traefik as a reverse-proxy. For detailed steps go to
[docs](https://docs.traefik.io/user-guide/docker-and-lets-encrypt/).
VM should have ready:

- docker and docker-compose installed
- be accessible from outside

Pull this repo and cd into it's directory.
Create a file acme.json, which will store the certificates and chmod it:

```bash
$ mkdir letsencrypt && touch letsencrypt/acme.json && chmod 600 letsencrypt/acme.json
```

Create a directory for access logs:

```bash
$ mkdir accessLogs
```

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

To start an app, you need to attach labels to the container, which setup the routing rules. [Docs](https://doc.traefik.io/traefik/routing/overview/)

```yml
version: "3.7"
services:
  app:
    image: ${APP_IMAGE_NAME}
    container_name: ${APP_CONTAINER_NAME}
    restart: always
    expose:
      - ${APP_PORT}
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.mahrusha.rule=(Host(`${VIRTUAL_HOST}`) && (PathPrefix(`/api`) || PathPrefix(`/image`)))"
      - "traefik.http.routers.mahrusha.entrypoints=websecure"
      - "traefik.http.routers.mahrusha.tls.certresolver=myresolver"
    env_file:
      - .env
    environment:
      - DATA_VOLUME=/data/app
    volumes:
      - app:/data
    command: npm run start:prod
    depends_on:
      - mongo
    networks:
      - web
      - default
volumes:
  app:

networks:
  web:
    external: true
```

The app should expose a port and list it in the labels. Docker network should
be the same one that you've created in the begining.
The certificate for `VIRTUAL_HOST` will be automatically requested.
