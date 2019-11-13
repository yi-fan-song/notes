---
tags: [Notebooks/Deployment Goodies]
title: Systemd Service
created: '2019-11-12T02:47:50.994Z'
modified: '2019-11-12T02:50:49.991Z'
---

# Systemd Service
Systemd Service Unit files lets you start programs on startup and keep programs running in the background.

```console
$ sudo vi /lib/systemd/system/{service}.service
```

Example configuration:
```
[Unit]
Description={service}

[Service]
Type=simple
Restart=always
RestartSec=5s
ExecStart=/path/to/service 

[Install]
WantedBy=multi-user.target
```

## Starting and stopping the service
You can start the service with:
```console
$ sudo service {service} start
```

You can check the service status with:
```console
$ sudo service {service} status
```

You can stop the service with:
```console
$ sudo service {service} stop
```
