# Docker compose 

```yml [5 | 9-11 | 13-21 | 23-24 | 26 | 33-34 | 28-30]
version: "3.3"

services:
  traefik:
    image: traefik:v2.0
    restart: always
    container_name: traefik
    ports:
      - "80:80" # <== http
      - "8080:8080" # <== :8080 is where the dashboard runs on
      - "443:443" # <== https
    command:
      - --api.insecure=true # <== Enabling insecure api, NOT RECOMMENDED FOR PRODUCTION
      - --api.dashboard=true # <== Enabling the dashboard to view services, middlewares, routers, etc...
      - --api.debug=true # <== Enabling additional endpoints for debugging and profiling
      - --log.level=DEBUG # <== Setting the level of the logs from traefik
      - --providers.docker=true # <== Enabling docker as the provider for traefik
      - --providers.docker.exposedbydefault=false # <== Don't expose every container to traefik, only expose enabled ones
      - --providers.file.filename=/dynamic.yaml # <== Referring to a dynamic configuration file
      - --providers.docker.network=web # <== Operate on the docker network named web
      - --entrypoints.web.address=:80 # <== Defining an entrypoint for port :80 named web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock # <== Volume for docker admin
      - ./dynamic.yaml:/dynamic.yaml # <== Volume for dynamic conf file, **ref: line 27
    networks:
      - web # <== Placing traefik on the network named web, to access containers on this network
    labels:
      - "traefik.enable=true" # <== Enable traefik on itself to view dashboard and assign subdomain to view it
      - "traefik.http.routers.api.rule=Host(`monitor.localhost`)" # <== Setting the domain for the dashboard
      - "traefik.http.routers.api.service=api@internal" # <== Enabling the api to be a service to access

networks:
  web:
    external: true
```
