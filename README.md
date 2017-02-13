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

## Accessing Servers with SSH
### Configuring the SSH Client
```
# ssh user@serverIP
```

Added 'config' file in user directory's .ssh folder, i.e. `/root/.ssh/`
```
Host server1
  HostName 192.168.56.105
  User root
  Post 22 # terminal default
  
...
```

### Using Key Based Authentication
Generating rsa key
```
# ssh-keygen -t rsa
```
Transfering public key to the intended server
```
# ssh-copy-id -i id_rsa.pub serverName
```

```
# ssh-agent bash
# ssh-add
```

### Copy Files Securely
```
scp /source/file/path serverName:/destination/path
```
