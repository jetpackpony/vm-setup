# Setup

VM should have:
  * docker and docker-compose installed
  * be accessible from outside

Pull this repo and run:

```bash
docker-compose up -d
```

Check that 2 containers are running:

```bash
docker ps -a
```

To start an app, run:

```bash
docker run --detach \
    --name app1 \
    --expose 3000
    --env "VIRTUAL_HOST=subdomain.yourdomain.tld" \
    --env "VIRTUAL_PORT=3000" \
    --env "LETSENCRYPT_HOST=subdomain.yourdomain.tld" \
    --env "LETSENCRYPT_EMAIL=mail@yourdomain.tld" \
    docker/app1-image
```

The port app is exposed on should be listed. `subdomain.yourdomain.tld` is a 
domain for which the certificate will be requested and registered.

To skip certificate generation, omit LETSENCRYPT_* variables.
