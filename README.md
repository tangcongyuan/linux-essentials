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
ctrl + r to search commands reversely

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

## Piping and Redirection
### Redirecting STDOUT
1 for STDOUT; `>>` for append
```
$ df -h > fileName
$ df -h 1> fileName
$ df -h 1>> fileName
```

### Using the noclobber Option
Disable overwritting existing file using piping
```
$ set -o
$ set -o noclobber
$ set -o | grep noclobber
$ set +o noclobber
```

Force overwrite
```
$ ls >| file
```

Make this setting to login script
```
$ vi .bashrc
set -o noclobber
```

### Redirecting STDERR
```
$ find /etc -type l 2> err.txt
$ find /etc -type l &> err.txt
```

### Reading into STDIN
```
$ mail
$ df -hlT > diskfree
$ mail -s "Disk Free" eric < diskfree
$ mail
& d
& q
```

### Using HERE documents
```
$ cat > file <<END
> this is a little
> file that we can create
> enven with scripts
> END
```

### Command Pipelines
`|` unnamed pipe
```
$ ls | wc -l
$ head -n1 /etc/passwd
$ cut -f7 -d: /etc/passwd | sort | uniq | wc -l
```

### Named Pipes
Pipe files, inter-process communication
```
$ ls -l $(tty)
$ ls -l /dev/sda
$ mkfifo mypipe
$ ls -l !$
$ls -F !$
```

From process 1:
```
$ ls > mypipe
```

From process 2
```
$ wc -l < mypipe
```

### Using the Command tee
Send the output to two locations, both file and STDOUT
```
$ ls > file
$ ls | tee file
```

## Archiving Files
### Using the tar Command
The -s (for suppress or summarize) option tells `du` to report only the total disk space occupied by a directory tree and to suppress individual reports for its subdirectories.

`c`: create, `v`: verbose, `f`: file, `t`: test, `x`: extract
```
$ du -sh .
$ tar -cvf /tmp/$USER.tar $HOME
$ tar -tf /tmp/$USER.tar
$ tar -xvf /tmp/$USER.tar /destination/path
```

### Using Compression
```
$ gzip /tmp/USER.tar
$ gunzip /tmp/USER.tar
$ bzip2 /tmp/USER.tar
$ bunzip2 /tmp/USER.tar
```

`z`: gzip, `j`: bzip2. gzip is faster.
```
$ tar -cvzf /tmp/$USER.tar.gz
$ tar -cvjf /tmp/$USER.tar.bz2
```

### Working with cpio
```
$ find /usr/share/doc -name '*.pdf' | cpio -o > /tmp/pdf.cpio
$ cpio -id < /tmp/pdf.cpio
$ cpio -id 
```

### Imaging with dd
Disk Duplicator
```
$ dd if=/dev/sr0 of=image.iso
$ sudo dd if=/dev/sda1 of=boot.iso
```

Erase your disk with `dd`
```
# fdisk -l
# dd if=/dev/sda of=sda.mbr count=1 bs=512
# dd if=/dev/zero of=/dev/sda count=1 bs=512
# dd if=sda.mbr of=/dev/sda
```

## Understand File Permissions
### Managing File ownership
Default group will be `$ id -gn` and used when creating files

Secondary group `$ id -Gn` related to accessing files
```
$ chgrp wheel fileName
$ newgrp wheel
$ touch newFile
# chown eric.eric newFile
```

Remain files ownership and timestamp
```
# cp -a fileName /destination/path
```

## Accessing the root account
See user's id
```
$ id
$ id -u
$ id -un
$ id -gn
$ id -Gn
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

## Using screen and script
### Using script as a Collaboration Tool
Default file created called: *typescript*
```
$ script
... recording starts until exit
```

Monitor the pipe, enables others see your action.
```
$ script -f /tmp/mypipe
```

### Running Commands Across Your Estate with screen
`t`: title
```
# yum install -y screen
$ vi .screenrc
  screen -t master 0 bash
  screen -t s1 1 ssh server1
  screen -t s2 2 ssh server2
:wq
```
`ctrl + a n`: Next screen

`ctrl + a p`: Previous screen

`ctrl + a "`: List all screen

`ctrl + a :` then
```
at "#" stuff "yum install -y zsh^M"
```
will run `yum install -y zsh` in all screens.
