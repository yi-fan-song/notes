---
tags: [Notebooks/Deployment Goodies]
title: Installing GitLab
created: '2019-11-11T02:59:02.346Z'
modified: '2019-11-11T03:21:43.353Z'
---

# Installing GitLab
GitLab renamed Community Edition to GitLab FOSS and made it core, thus the ee link, additional features are at a premium.

GitLab provides a bash script to add the repository
```console
dev@ubuntu:~$ cd /tmp
dev@ubuntu:/tmp$ curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh
dev@ubuntu:/tmp$ sudo bash /tmp/script.deb.sh
```
Install using apt-get.
```console
dev@ubuntu:~$ sudo apt-get install gitlab-ee
```

## Configuring GitLab
Start editing the configuration file.
```console
dev@ubuntu:~$ sudo vi /etc/gitlab/gitlab.rb
```

Change the following to your URL, and change to `https` to enable ssl/tls.
```
external_url 'http://{mydomain}
```

The ssl key and certificate goes into `/etc/gitlab/ssl/`.
```
dev@ubuntu:~$ sudo mkdir /etc/gitlab/ssl 
dev@ubuntu:~$ sudo chmod 700 /etc/gitlab/ssl
dev@ubuntu:~$ sudo mv {yourdomain}.crt {yourdomain}.key /etc/gitlab/ssl/
```

Finish by reconfiguring GitLab.
```
dev@ubuntu:~$ sudo gitlab-ctl reconfigure
```

GitLab is not installed
More information at [GitLab docs](https://docs.gitlab.com/omnibus/settings/configuration.html)
