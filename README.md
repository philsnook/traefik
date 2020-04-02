# traefik

Traefik 2.2 with TLS >= 1.2

## Docker Example

```
version: "3.7"

networks:
  traefik-private:
    external: true
  traefik-public:
    external: true

services:
  load-balanced-reverse-proxy:
    image: phsnook/traefik:1.0
    command:
      - "--log.level=error"

      - "--api.debug=false"
      - "--api.insecure=false"
      - "--api.dashboard=false"

      - "--providers.docker.endpoint=unix:///var/run/docker.sock"
      - "--providers.docker.swarmMode=true"
      - "--providers.docker.exposedbydefault=false"

      - "--providers.file.filename=traefik_conf.yaml"
      - "--providers.docker=true"
      - "--providers.docker.network=traefik-private"

      - "--entryPoints.http.address=:80"
      - "--entryPoints.https.address=:443"

    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - traefik-public
      - traefik-private
    deploy:
      replicas: 1
      placement:
        constraints:
          - node.role == manager
```
