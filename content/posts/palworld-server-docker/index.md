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
      - 25575:25575
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

# 先创建文件，避免被识别为目录
touch /root/docker-hub/palworld/pst.db

docker run -d --name pst \
-p 8080:8080 \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /root/docker-hub/palworld/pst.db:/app/pst.db \
-e SAVE__PATH="docker://palworld-server:/palworld/Pal/Saved" \
-e WEB__PASSWORD="Ayaya" \
-e RCON__ADDRESS="10.0.0.203:25575" \
-e RCON__PASSWORD="DIYcat" \
-e SAVE__SYNC_INTERVAL=120 \
jokerwho/palworld-server-tool:latest

docker run -d --name pst \
-p 8080:8080 \
-v /root/docker-hub/palworld/palworld/Pal/Saved:/game \
-v /root/docker-hub/palworld/pst.db:/app/pst.db \
-e WEB__PASSWORD="yourpassword" \
-e RCON__ADDRESS="10.0.0.102:25575" \
-e RCON__PASSWORD="DIYcat" \
-e SAVE__PATH="/game" \
-e SAVE__SYNC_INTERVAL=120 \
jokerwho/palworld-server-tool:latest

