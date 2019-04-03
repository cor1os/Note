# Nessus

#### Update

> 家庭版不支持在线更新

关闭nessus程序

`nessusd stop`

获取Challenge code

`nessuscli fetch --challenge`

获取Active code

`http://www.tenable.com/products/nessus-home`

打开

`https://plugins.nessus.org/offline.php`

输入Challenge code和Active code

下载all-2.0.tar.gz与nessus.license文件

`nessuscli update all-2.0.tar.gz`

`nessuscli fetch --register-offline nessus.license`

`nessusd restart`

