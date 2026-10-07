
Sender -> 
```bash
nc [Target IP] [Target Port] < input.txt
```



Receiver ->
```bash
nc -l -p [Target Port] > output.txt 
```

