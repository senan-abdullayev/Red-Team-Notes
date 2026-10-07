
##### To identify Linux machine is connected to active directory domain or not -> 

```
realm list
```


##### If realm is not found on machine , Use -> 

```
ps -ef | grep -i "winbind\|sssd"
```


