# dokku-miniflux

This tutorial describes how to install the [Miniflux](https://miniflux.app/index.html) RSS feed reader on your Dokku
installation.

## Prerequisites

This requires a working installation of [Dokku](https://dokku.com/), the Postgres plugin for Dokku
[dokku-postgres](https://github.com/dokku/dokku-postgres) and the
[dokku-letsencrypt](https://github.com/dokku/dokku-letsencrypt) plugin.

## Installation

Run these commands on your Dokku server.

### Prepare Dokku

Setup application and database.

```
# Create application and database
dokku apps:create miniflux
# Keep the Postgres DSN output for the Miniflux configuration settings later
dokku postgres:create miniflux
dokku postgres:link miniflux miniflux

# Get latest Miniflux image and create Docker tag
docker image pull miniflux/miniflux:latest
docker image tag miniflux/miniflux:latest dokku/miniflux:latest
```

### Configure Dokku application settings

```
# Set the DATABASE_URL from the previous DSN `dokku postgres:create miniflux` output
# dokku config:set miniflux DATABASE_URL=postgres://postgres:5c16cb123123cc@dokku-postgres-miniflux:5432/miniflux
dokku config:set miniflux RUN_MIGRATIONS=1
dokku config:set miniflux CREATE_ADMIN=1
# Configure your custom username here
dokku config:set miniflux ADMIN_USERNAME=<SET_YOUR_USERNAME>
# Configure your custom password here
dokku config:set miniflux ADMIN_PASSWORD=<SET_YOUR_USERNAME>
```

### Configure Dokku port settings

```
dokku proxy:ports-set miniflux http:80:8080
```

### Deploy

```
dokku tags:deploy miniflux latest
```

You should be now able to open your Miniflux app via: http://miniflux.example.com (check dokku output).  
If it didn't succeed, check `dokku logs miniflux`.

### Configure letsencrypt

```
dokku letsencrypt miniflux
```

Now Miniflux should be reachable via https://miniflux.example.com. If that's not the case, 
check the `letsencrypt` plugin output.