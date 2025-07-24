# Gogs

[![PFM-Upstream-Sync](https://github.com/PFM-PowerForMe/gogs/actions/workflows/fork-sync.yml/badge.svg)](https://github.com/PFM-PowerForMe/gogs/actions/workflows/fork-sync.yml)

## 简介
Gogs是一个轻量的自托管Git服务。 

## 如何部署?

1. 创建数据库

2. 使用 Podman

```
nvim /etc/containers/systemd/gogs.container && \
systemctl daemon-reload && \
systemctl restart gogs
```
```
# /etc/containers/systemd/gogs.container

[Unit]
Description=The gogs container
Wants=postgresql.service
After=postgresql.service

[Container]
AutoUpdate=registry
ContainerName=gogs
Timezone=local
UserNS=host
Network=deploy
Volume=gogs-data:/data
Tmpfs=/app/gogs/log
PublishPort=127.0.0.1:6010:3000
PublishPort=2222:2222
Image=ghcr.io/pfm-powerforme/gogs:latest

[Service]
Restart=on-failure
RestartSec=30s
StartLimitInterval=30
TimeoutStartSec=900
TimeoutStopSec=70

[Install]
WantedBy=multi-user.target default.target
```