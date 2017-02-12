# linux-essentials
Notes from LFCS on Pluralsight

## Linux Foundation Certified System Administrator (LFCS)

## Accessing the root account

See user's id
```
$ id
```

### Using su
substitue user (creates new bash shell as root in default)
```
$ su
```

These two are equivalent, but takes you to new login shell, which means all variables will be refreshed. Better use these!
```
$ su -
$ su -l
```

### Delegation with sudo
Change `/etc/sudoers` file with command
```
$ sudo visudo
```

### Restricting root access via SSH
Modify ssh daemon config file to disable SSH login as root: `PermitRootLogin no`
```
# vi /etc/ssh/sshd_config
```

then need to restart the daemon process
```
# systemctl restart sshd
```
