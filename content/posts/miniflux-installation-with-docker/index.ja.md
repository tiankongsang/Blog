---
title: "Dockerを使用してMinifluxをインストールする"
date: 2024-03-14T16:53:09+08:00
externalUrl: ""
summary: ""
draft: false
showReadingTime: true
---

Docker を使用して Miniflux をインストールする

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

- `DATABASE_URL`はデータベース接続パラメータを定義するために使用されます。
- `RUN_MIGRATIONS=1`は SQL マイグレーションを自動的に実行します。
- `CREATE_ADMIN`、`ADMIN_USERNAME`、`ADMIN_PASSWORD`は、最初の管理者ユーザーを作成するために使用され、初期化が完了したらこれらの設定を削除できます。
