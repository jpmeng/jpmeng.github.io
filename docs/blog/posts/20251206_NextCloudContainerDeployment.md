---
date: 2025-12-23
tags: [Linux, Nextcloud, podman]
title: Deploy Nextcloud using podman on Rockylinux 10
summary:
categories: [Linux, Storage]
---

# Deploy Nextcloud using podman on Rockylinux 10

Nextcloud is a open source suite for deploying a cloud service like OneDrive and Dropbox at home. It also provides client versions for various platform including Linux. In this post, We will share the experience on deploy Nextcloud using podman on a small home server operating Rockylinux 10.


<!-- more -->

First, we create a `podman-compose.yml` file [1,2] for pulling the image and building the service.

```yaml
version: '3'
services:
  # Redis for caching and file locking [3]
  redis:
    image: redis:alpine
    container_name: nextcloud-redis
    restart: always
    volumes:
      - ./redis:/data
    # Replace <redis-password> with your actual password
    command: redis-server --requirepass <redis-password> --maxmemory 1G --maxmemory-policy allkeys-lru
    networks:
      - nextcloud-net
    security_opt:
      - label=disable
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 1.5G

  # MariaDB database
  db:
    image: mariadb:10.11
    container_name: nextcloud-db
    restart: always
    environment:
      # Replace these with your actual credentials
      - MYSQL_ROOT_PASSWORD=<db-root-password>
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=<db-user-password>
      - MYSQL_INITDB_SKIP_TZINFO=1
    volumes:
      - ./db:/var/lib/mysql
      - ./my.cnf:/etc/mysql/conf.d/my.cnf
    networks:
      - nextcloud-net
    security_opt:
      - label=disable
    tmpfs:
      - /tmp:size=2G

    # Optional: enable if you want Nextcloud to wait for DB readiness before automatic DB setup
    # healthcheck:
    #   test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-p<db-root-password>"]
    #   interval: 5s
    #   timeout: 3s
    #   retries: 10
    #   start_period: 10s

    deploy:
      resources:
        limits:
          cpus: '1'
          memory: 2G

  # Nextcloud application
  app:
    image: nextcloud:stable
    container_name: nextcloud-app
    restart: always

    # Optional: enable if using healthchecks
    # depends_on:
    #   db:
    #     condition: service_healthy
    #   redis:
    #     condition: service_started

    depends_on:
      - db
      - redis

    environment:
      # Replace with your actual domain/host
      - NEXTCLOUD_TRUSTED_DOMAINS=<your-domain>
      - NEXTCLOUD_OVERWRITEPROTOCOL=http
      - NEXTCLOUD_OVERWRITEHOST=<your-host>
      - NEXTCLOUD_OVERWRITEWEBROOT=/

      # Optional: enable automatic Redis integration
      # - REDIS_HOST=redis
      # - REDIS_HOST_PASSWORD=<redis-password>

      # Optional: enable automatic DB setup
      # - MYSQL_HOST=db
      # - MYSQL_DATABASE=nextcloud
      # - MYSQL_USER=nextcloud
      # - MYSQL_PASSWORD=<db-user-password>

    volumes:
      - ./nextcloud-data:/var/www/html

    networks:
      - nextcloud-net

    ports:
      - "10000:80"

    security_opt:
      - label=disable

    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8G

networks:
  nextcloud-net:
    driver: bridge
```

To start and stop the service using podman, using

```bash
podman-compose up -d
podman-command down
```

During the first `podman-compose up` call, containers will be built by pulling the image from the chosen repositories.

To check redis is enabled for Nextcloud, find the file `config.php` at the
directory `/var/www/html` inside the container or ./nextcloud-data at host,
and check the following items

```php
 'memcache.local' => '\\OC\\Memcache\\APCu',
 'memcache.distributed' => '\\OC\\Memcache\\Redis',
 'memcache.locking' => '\\OC\\Memcache\\Redis',
```

In this way, a deployment of Nextcloud is finished. We may configure further, say, to enable reverse proxy for the website. It is worthwhile to the optional settings are to be tested.

[1] https://www.geeksforgeeks.org/devops/compose-files-with-podman/

[2] https://docs.docker.com/reference/compose-file/

[3] https://docs.nextcloud.com/server/19/admin_manual/configuration_server/caching_configuration.html
