# linux-essentials
Notes from LFCS on Pluralsight

## Linux Foundation Certified System Administrator (LFCS)

## Installing CentOS 7
### CentOS Downloads
### VirtualBox Networking
Got to File->Preferences->Network and add a NAT network
Don't forget to add Host-only Adapter as your Adapter 2. I don't know why...

### Configure CentOS 7 Networking
```
# ip address show
# ip a s
```

Bring network up
```
# nmcli conn show
# nmcli conn up enp0s3
# nmcli conn up enp0s8
```

```
# sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/ifcfg-enp0s3
# sed -i s/ONBOOT=no/ONBOOT=yes/ /etc/sysconfig/network-scripts/ifcfg-enp0s8
# grep ONBOOT !$
```

### Installing X
Before install, update yum
```
# yum update
```

Common packages
```
# yum install -y redhat-lsb-core net-tools epel-release kernel-headers kernel-devel
```

Group packages
```
# yum groupinstall -y "Development Tools"
```

GUI
```
# yum groupinstall -y "X Windows System" "MATE Desktop"
```

Service management tool, `systemctl`, set run level and switch to GUI
```
# systemctl set-default graphical.target
# systemctl isolate graphical.target
```

### Add Guest Addtion
Enhancing VirtualBox experience
```
# mount
# /run/media/username/VBOXADDITIONS_XXXX/VBoxLinuxAdditions.run
```
Those commmon packages above will be useful when installing guest additions, and update, and reboot.

## Working at the Command Line
### Accessing Consoles
Right ctr + F1 takes you to the first tty, which is the GUI

Right ctr + F2 takes you to the second tty, which is the physical terminal
```
$ tty
$ who
```

### Listing Files
See `ls` with alias
```
$ type ls
```

r-reverse; t-timestamp; h-human readable; d-directory
```
$ ls -lrth
```

### File Types
Charactor device/tty
```
$ ls -l $(tty)
```

Block device, disk and partitions
```
$ lsblk
$ ls -l /dev/sda*
$ ls -l /dev/sda?
```

Link
```
$ ls -l /etc/centos-release /etc/redhat-release
```

Executable(package manager queries file)
```
$ ls -l $(which lsb_release)
$ rpm -qf $(which lsb_release)
```

### Working with Files
Prevent overwriting file when exists(with interactive mode)
```
cp -i /source/file /destination/file
rm -i /file/path
```

### Working with Directories
Make parent directory before make the designated child directory
```
$ mkdir -p /parent/child
```

Make directory with specific mode
```
$ mkdir -m 777 /dir
```

Remove directory
```
$ rmdir /directory
$ rm -rf /directory
```

Copying directory, or files in directory
```
$ cp -R /source/dir /destination/dir
```

Install tree helps visualize directories
```
# yum install tree
$ tree /dir
```

### Working with links
Check meta-data: the entry of a file
```
$ ls -i /file
```

At least two hard links for one dir
```
$ ls -ldi /dir
```

And dot dir links to dir itself
```
$ ls -ldi /dir /dir/.
```

Hard link
```
$ ln /source/file /destination/file
```

Symbolic link, can cross file system boundries
```
$ ln -s /source/file /destination/file
```

## Reading Files
### Reading from Files
```
$ head -n 3 /etc/services
$ tail -n 3 /etc/passwd
$ cat /file
$ less /file
```

### Regular Expressions and grep
```
$ yum installed | grep ^kernel
$ grep '\bserver\b' ntp.conf
$ grep -E 'ion$' /usr/share/dict/words
$ grep -E '^po..ute$' /usr/share/dict/words
$ grep -E '[aeiou]{5}' /usr/share/dict/words
```

### Using sed to Edit Files
```
$ sed -i '/^#/d ; /^$/d' ntp.conf
```

```
$ function clean_file {
> sed -i '/^#/d;/^$/d' $1
> }
$ clean_file ntp.conf
```

### Comparing Files
```
$ diff file1 file2
$ md5sum /usr/bin/passwd
```

### Finding Files
```
$ find /usr/share/doc -name '*.pdf' -exec cp {} . \;
$ find -name '*.pdf' -delete
$ find /etc -maxdepth 1 -type l
$ df -h /boot
$ find /boot -size +20000k -type f -exec du -h {} \;
```

## Using the vim Text Editor
### Using the nano Text Editor
```
$ sudo yum install -y nano
```

### Learning vim with vimtutor
```
$ vimtutor
```

### Setting Defaults with .vimrc
```
:set showmode
:set number
:set nonumber
:set invnumber
```

Set no highlight search and auto indent
```
$ vi .vimrc
set showmode nonumber nohlsearch
set ai ts=4 expandtab
abbr _sh #!/bin/bash
nmap <C-N> :set invnumber<CR>
:wq
```

Return to last saved change
```
:e!
```

### Editing Files with vim
Lower case `o` inserts below current line

Upper case `O` inserts above current line

Upper case `I` inserts start of line

Upper case `A` inserts end of line
```
G
2G
```
After `2yy`, `p` pastes below current line; `P` pates above current line.

`u` undo

`^` moves to the beginning of line

`$` moves to the end of line

`dG` deletes to the end of file

`d$` deletes to the end of line

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

Caching credentials and login with keys, not password anymore.
```
# ssh-agent bash
# ssh-add
```

### Copy Files Securely
```
scp /source/file/path serverName:/destination/path
```
