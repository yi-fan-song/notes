---
tags: [Notebooks/Deployment Goodies, Notebooks/Golang]
title: Making and Serving a Go Web Server
created: '2019-11-12T02:33:41.036Z'
modified: '2019-11-12T02:47:30.081Z'
---

# Making and Serving a Go Web Server
This guide will go over how to start and serve a web server written in go on ubuntu using nginx (or not).

## Making a server
In a brand new go project, add this main file to create the most barebone project in order to serve it
```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func (w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Hello World")
	})

	http.ListenAndServe(":9990", nil)
}
```
Build the go app with `go build main.go`. Depending on the system it will generate a different file, that file can be executed to start the server.

## Creating a Systemd Unit File
Systemd unit file lets you keep an application running in the background.
```console
$ sudo nano /lib/systemd/system/{service}.service
```

A simple example setting.
```
[Unit]
Description={service}

[Service]
Type=simple
Restart=always
RestartSec=5s
ExecStart=/path/to/main (path to the built main)

[Install]
WantedBy=multi-user.target
```

Start the service with:
```console
$ sudo service {service} start

# check status
$ sudo service {service} status
```

## Setting up a reverse proxy in nginx
You can skip this step if you don't need it.

In `/etc/nginx/sites-available`, create a new config file.
```console
$ sudo vi /etc/nginx/sites-available/{domain}.conf
```

Write this in the file:
```
server {
    server_name your_domain www.your_domain;

    location / {
        proxy_pass http://localhost:9990;
    }
}
```

Create a symlink to the new config because nginx needs the file in both directories.
```console
$ sudo ln -s /etc/nginx/sites-available/{domain}.conf /etc/nginx/sites-enabled/

# reload nginx
$ sudo nginx -s reload
```

[source](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-go-web-application-using-nginx-on-ubuntu-18-04)
