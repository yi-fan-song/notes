---
tags: [Notebooks/Deployment Goodies]
title: GitLab Migration Guide
created: '2019-11-11T03:22:27.664Z'
modified: '2019-11-11T03:54:48.428Z'
---

# GitLab Migration Guide
I had to migrate gitlab because I originally made it on a desktop Ubuntu version and wanted to move it to a server Ubuntu instead (facepalm).

## The General Strategy
1. Install GitLab on new server
1. Update GitLab to newest on old server
1. Backup old GitLab
1. Transfer files
1. Restore from backup

Doing everything from root user is recommended (I don't know why that's what the guide said, some has root user disabled, good luck on that).

## Installing GitLab
Instructions [here](@note/Installing GitLab)

## Updating GitLab
Just update everything.
```console
root@ubuntu:~$ apt-get update
root@ubuntu:~$ apt-get dist-upgrade
```

## Backing up GitLab

### Configurations
```console
root@ubuntu:~$ sh -c 'umask 0077; tar -cf $(date "+etc-gitlab-%s.tar") -C / etc/gitlab'
root@ubuntu:~$ ls -la etc-gitlab-1526500078.tar
root@ubuntu:~$ tar -tvf etc-gitlab-1526500078.tar
```

### Application
Find the backup path.
```console
root@ubuntu:~$ grep backup_path /etc/gitlab/gitlab.rb
```

This will create the backup.
```console
root@ubuntu:~$ gitlab-rake gitlab:backup:create
```

The default backup path will be here
```console
root@ubuntu:~$ ls -ltr /var/opt/gitlab/backups/
```

### Host
Compress to backup ssh host keys.
```console
root@ubuntu:~$ tar -cvf $(date "+hostkeys-%s.tar") $(grep HostKey /etc/ssh/sshd_config | grep -v ^'#' | awk '{print $2}')
```

## Transfering backups
Get the backup locations from
```console
dev@ubuntu:~$ ls -ltr /var/opt/gitlab/backups/
dev@ubuntu:~$ ls -la etc-gitlab-*.tar
dev@ubuntu:~$ ls -la hostkeys-*.tar
```

And compress them.
```console
root@ubuntu:~$ tar -cvzf ~/gitlab-backup.tar.gz /var/opt/gitlab/backups/1526501659_2018_05_16_10.7.3_gitlab_backup.tar /root/etc-gitlab-1526500078.tar /root/hostkeys-1526501148.tar
```

Use scp to move them to the new server.
```console
root@ubuntu:~$ scp -v root@{oldserver}:/root/gitlab-backup.tar.gz .
```

## Restoring Backups
Extract the backup.
```console
root@ubuntu:~$ mkdir gitlab-restore
root@ubuntu:~$ mv gitlab-backup.tar.gz gitlab-restore/
root@ubuntu:~$ cd gitlab-restore/
root@ubuntu:~/gitlab-restore$ tar -xzvf gitlab-backup.tar.gz
root@ubuntu:~/gitlab-restore$ ls -l
```

### Restore application data.
You need to stop sidekiq and unicorn.
```console
root@ubuntu:~/gitlab-restore$ gitlab-ctl stop unicorn
root@ubuntu:~/gitlab-restore$ gitlab-ctl stop sidekiq
root@ubuntu:~/gitlab-restore$ gitlab-ctl status
```
Make sure they're off.

Copy application backup and change owner.
```console
root@ubuntu:~/gitlab-restore$ cp -v var/opt/gitlab/backups/1526501659_2018_05_16_10.7.3_gitlab_backup.tar /var/opt/gitlab/backups/
root@ubuntu:~/gitlab-restore$ chown -v git:git /var/opt/gitlab/backups/1526501659_2018_05_16_10.7.3_gitlab_backup.tar
```

Then restore the backup.
```console
root@ubuntu:~/gitlab-restore/$ gitlab-rake gitlab:backup:restore BACKUP=1526501659_2018_05_16_10.7.3
```

Restart GitLab and verify that there is no problem.
```console
root@ubuntu:~/gitlab-restore$ gitlab-ctl restart
root@ubuntu:~/gitlab-restore$ gitlab-rake gitlab:check SANITIZE=true
```

### Restore configuration
you can compare the configurations.
```console
root@ubuntu:~/gitlab-restore$ cd root/
root@ubuntu:~/gitlab-restore/root$ tar -vxf etc-gitlab-15*
root@ubuntu:~/gitlab-restore/root$ diff -y --suppress-common-lines etc/gitlab/gitlab.rb /etc/gitlab/gitlab.rb
```

Stop GitLab.
```console
root@ubuntu:~/gitlab-restore/root$ gitlab-ctl stop
```

Move the original configs and replace with yours.
```console
root@ubuntu:~/gitlab-restore/root$ mv -v /etc/gitlab{,.original}
root@ubuntu:~/gitlab-restore/root$ mv etc/gitlab /etc/gitlab
```

Restart GitLab and check that it's functioning properly.
```console
root@ubuntu:~/gitlab-restore/root$ gitlab-ctl start
root@ubuntu:~/gitlab-restore/root$ gitlab-rake gitlab:check SANITIZE=true
```

### Restoring HostKeys
Extra the keys.
```console
root@ubuntu:~/gitlab-restore/root$ pwd
root@ubuntu:~/gitlab-restore/root$ ls -la hostkeys-1526501148.tar
root@ubuntu:~/gitlab-restore/root$ tar -xvf hostkeys-1526501148.tar
```

You will be replacing 
```console
root@ubuntu:~/gitlab-restore/root$ grep HostKey /etc/ssh/sshd_config
```
With
```console
root@ubuntu:~/gitlab-restore/root$ tar -tvf hostkeys-1526501148.tar.gz
```

Rename the existing keys.
```console
root@ubuntu:~/gitlab-restore/root$ for i in $(grep ^HostKey /etc/ssh/sshd_config | awk '{print $2}'); do sudo mv -v $i $i.bkp-$(date +%s);done
```

Place the restored one in place
```console
root@ubuntu:~/gitlab-restore/root$ tar -xvzf hostkeys-1526501148.tar.gz -C /
```

Restart GitLab and check for erros.
```console
root@ubuntu:~/gitlab-restore/root$ gitlab-ctl restart
root@ubuntu:~/gitlab-restore/root$ gitlab-rake gitlab:check SANITIZE=true
```

## Troubleshooting
Try reconfigure and restart if there are isues
```
root@ubuntu:~$ gitlab-ctl reconfigure
root@ubuntu:~$ gitlab-ctl restart
```

[source](https://pikedom.com/migrate-gitlab-instance-to-new-host/)
