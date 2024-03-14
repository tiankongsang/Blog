---
title: "使用 Docker 安装 Miniflux"
date: 2024-03-14T16:53:09+08:00
externalUrl: ""
summary: ""
draft: false
showReadingTime: true
---

## 使用 Docker 安装 Miniflux

docker-compose.yml:

```yaml
services:
  miniflux:
    image: miniflux/miniflux:latest
    ports:
      - "8080:8080"
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

- `DATABASE_URL` 用于定义数据库连接参数。
- `RUN_MIGRATIONS=1` 自动运行 SQL 迁移。
- `CREATE_ADMIN`、`ADMIN_USERNAME`、`ADMIN_PASSWORD` 允许我们创建第一个管理员用户，完成首次初始化后可以移除这些设置。
