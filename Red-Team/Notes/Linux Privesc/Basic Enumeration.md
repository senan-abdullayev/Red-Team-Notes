
### Listing Processes

```
ps aux
```


### User Home Directories

```
ls /home
```


### Passwd & Shadow file

```
cat /etc/passwd ; cat /etc/shadow
```


### Groups

```
cat /etc/group
```


The `/etc/group` file lists all of the groups on the system. We can then use the [getent](https://man7.org/linux/man-pages/man1/getent.1.html) command to list members of any interesting groups.


```
getent group sudo
```

### History

```
cat ~/.bash_history
history
```


### Listing User Privileges 

```
sudo -l
```


### Cron jobs

```
ls -la /etc/cron.d
```

```
crontab -l
```


### File Systems & Additional Drives

```
lsblk
```


### Find Writable Directories

```
find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null
```


We'll also want to check the arp table to see what other hosts the target has been communicating with.

```
arp -a
```


We can also check out all environment variables that are set for our current user, we may get lucky and find something sensitive in there such as a password. We'll note this down and move on.


```
env
```



#### Unmounted File Systems

```
cat /etc/fstab | grep -v "#" | column -t
```


### Searching All hidden files

```
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student
```

