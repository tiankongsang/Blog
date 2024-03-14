---
title: "Miniflux Installation With Docker"
date: 2024-03-14T16:53:09+08:00
externalUrl: ""
summary: ""
draft: false
showReadingTime: true
---

## Miniflux Installation With Docker

docker-compose.yml:

```yaml
services:
  miniflux:
    image: miniflux/miniflux:latest
    ports:
      - "80:8080"
    depends_on:
      db:
        condition: service_healthy
    environment:
      - DATABASE_URL=postgres://miniflux:secret@db/miniflux?sslmode=disable
      - RUN_MIGRATIONS=1
      - CREATE_ADMIN=1
      - ADMIN_USERNAME=admin
      - ADMIN_PASSWORD=test123
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=miniflux
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=miniflux
    volumes:
      - miniflux-db:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "miniflux"]
      interval: 10s
      start_period: 30s
volumes:
  miniflux-db:
```

- `DATABASE_URL` is used to define the database connection parameters
- `RUN_MIGRATIONS=1` runs the SQL migrations automatically
- `CREATE_ADMIN`, `ADMIN_USERNAME`, `ADMIN_PASSWORD` allows us to create the first admin user, and it can be removed after the first initialization.
