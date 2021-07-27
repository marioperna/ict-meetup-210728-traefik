
# Deploy a wordpress 
```yml
version: '3.7'

services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    environment:
      WORDPRESS_DB_HOST: XXX
      WORDPRESS_DB_NAME: XXX
      WORDPRESS_DB_USER: XXX
      WORDPRESS_DB_PASSWORD: XXX
    volumes:
      - ./wp-data:/var/www/html/wp-content
    networks:
      - web
    restart: always
    labels:
      - "traefik.enable=true" # <== Enable traefik on itself to view dashboard and assign subdomain to view it
      - "traefik.http.routers.api.rule=Host(`wp1.localhost`)" # <== Setting the domain for the dashboard
      - "traefik.http.routers.api.service=api@internal" # <== Enabling the api to be a service to access
volumes:
  db-data:
    name: wp-db-data
networks:
  web:
    external: true
```