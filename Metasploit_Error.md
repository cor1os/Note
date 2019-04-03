# Metasploit Error

`[-] Failed to connect to the database: FATAL:  no pg_hba.conf entry for host "127.0.0.1", user "msf", database "postgres", SSL off`

这类报错通常因为所要连接的用户以及数据库没有在`postgresql`数据库配置文件的允许范围内`pg_hba.conf`

解决方法：使需要连接的用户及数据库配置在`pg_hba.conf`允许的范围内就可以了

```
#TYPE   DATABASE      USER       ADDRESS                METHOD
host    "msf"         "msf"      127.0.0.1/32           md5
host    "msftest"     "msftest"  127.0.0.1/32           md5
host    "postgres"    "msftest"  127.0.0.1/32           trust
host    "template1"   all        127.0.0.1/32           trust
local   all           all                               trust
```