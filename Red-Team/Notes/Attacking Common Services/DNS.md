
### this reveals all the subdomain of the inlanefreight.htb:

```ruby
dig AXFR @ns1.inlanefreight.htb(IP) inlanefreight.htb
```



#### Subdomain Enumeration:

```ruby
./subfinder -d inlanefreight.com -v
```


#### Subbrute

```ruby
git clone https://github.com/TheRook/subbrute.git
```
```ruby
cd subbrute
```
```ruby
echo "ns1.inlanefreight.com" > ./resolvers.txt
```
```ruby
./subbrute.py inlanefreight.com -s ./names.txt -r ./resolvers.txt
```




