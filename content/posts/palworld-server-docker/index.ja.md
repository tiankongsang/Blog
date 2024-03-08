---
title: "Palworld Server Docker"
date: 2024-02-26T13:06:10+08:00
externalUrl: ""
summary: ""
draft: true
showReadingTime: true
---

```shell
apt update && apt upgrade -y

apt install curl -y

curl -fsSL https://get.docker.com -o get-docker.sh

sudo sh ./get-docker.sh --mirror Aliyun

mkdir -p docker-hub/palworld && cd docker-hub/palworld

nano docker-compose.yml

docker compose up -d
docker compose logs -f
docker exec -it palworld-server rcon-cli "Save"
docker compose stop
```

```yml
services:
  palworld:
    image: thijsvanloef/palworld-server-docker:latest
    restart: unless-stopped
    container_name: palworld-server
    ports:
      - 8211:8211/udp
      - 27015:27015/udp
    environment:
      - PUID=1000
      - PGID=1000
      - PORT=8211
      - PLAYERS=16
      - SERVER_PASSWORD=Ayaya
      - MULTITHREADING=true
      - RCON_ENABLED=true
      - RCON_PORT=25575
      - TZ=Asia/Shanghai
      - ADMIN_PASSWORD=DIYcat
      - COMMUNITY=false
      - SERVER_NAME=World of Pals
      - SERVER_DESCRIPTION=palworld-server-docker by Thijs van Loef
      #- TARGET_MANIFEST_ID=1354752814336157338
    volumes:
      - ./palworld:/palworld/
```