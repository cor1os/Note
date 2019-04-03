#### 流程简介

挂载上你的NTFS硬盘，查看硬盘名称  
编辑`/etc/fstab`文件，使其支持NTFS写入  
将`/Volumes`中的NTFS磁盘快捷方式到Finder

#### 详细流程

插上U盘 查看硬盘名  
命令行写入`/etc/fstab`文件 内容为`LABEL=AngleDisk none ntfs rw,auto,nobrowse`  
退出U盘 重新插入