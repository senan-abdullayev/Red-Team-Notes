You aren't restricted to interacting with just one port (like the MySQL 3306 example). You can interact with the entire remote network as if your computer was physically plugged into their internal switch.

```ruby
ssh -D 1080 ubuntu@10.129.202.64
```

```ruby
tail -4 /etc/proxychains.conf

# meanwile
# defaults set to "tor"
socks4  127.0.0.1 1080
```

use commands with proxychains.