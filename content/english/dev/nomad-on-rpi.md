---
title: Nomad on Raspberry Pi
summary: TODO
date: 2024-10-26
draft: true
---

## Installing Docker

- https://docs.docker.com/engine/install/debian/#installation-methods
- https://docs.docker.com/engine/install/linux-postinstall/

## Installing Nomad

- https://developer.hashicorp.com/nomad/docs/install

## Sources and good reads

https://medium.com/swlh/running-hashicorp-nomad-consul-pihole-and-gitea-on-raspberry-pi-3-b-f3f0d66c907

## Notes

```bash
dpkg --print-architecture
arm64
```

```bash
id -u
1001
whoami
testuser
grep ^$(whoami): /etc/subuid
testuser:231072:65536
grep ^$(whoami): /etc/subgid
testuser:231072:65536
```

```bash
systemctl list-unit-files | grep enabled
```

See disk usage:

```bash
df -h
```

## Container registry on RPi

- https://hub.docker.com/_/registry
- https://distribution.github.io/distribution/about/configuration/

```txt
[Unit]
Description=Container registry service
After=docker.service
Requires=docker.service
[Unit]
Description=Container registry service
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
Restart=always
ExecStartPre=-/usr/bin/docker stop %n
ExecStartPre=-/usr/bin/docker rm %n
ExecStartPre=/usr/bin/docker pull registry:2
ExecStart=/usr/bin/docker run --rm -p 5000:5000 --name %n registry:2

[Install]
WantedBy=multi-user.target
```
