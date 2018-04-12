#### **有密码同步**

**阿里云服务器端操作：**

```
vim   /etc/rsyncd.conf
```

rsyncdv.conf内容：

```
#This is the rsync daemon configuration 

#global settings 
pid file = /var/run/rsyncd.pid
port = 873
lock file = /var/run/rsyncd.lock
log file = /var/log/rsync.log
gid = root
uid = root

#module settings 
[auth_data]
path = /web/rsync/auth_data
use chroot = no
max connections = 15
read only = yes
write only = no
list = no
ignore errors = yes
timeout = 120
auth users = root
secrets file = /etc/rsyncd.passwd
```

建立密码文件和要同步的文件夹，如果已经存在则不需要再建文件夹。

```
echo "hadoop:password123" > /etc/rsyncd.passwd
chmod 600 /etc/rsyncd.passwd
mkdir -p /web/rsync/auth_data
```

记得在服务端启动守护进程：

有可能rsync命令不在该文件夹/usr/bin/rsync,可通过which rsync来查询

```
/usr/bin/rsync --daemon --config=/etc/rsyncd.conf
```

**客户端操作：**

首先建立密码文件，然后输入同步命令，开始同步。

```
echo "password123" > /home/hadoop/rsyncd.passwd
chmod 600 /home/hadoop/rsyncd.passwd
rsync -avz --progress --password-file=/home/hadoop/rsyncd.passwd hadoop@192.168.1.98::auth_data /home/hadoop/auth_data
```

可以写一个简单的shell脚本让其自动不断同步，当然更好的方式还是采用其它软件配合rsync来使用。

```
#!/bin/bash
while :
    do  
    rsync -avz --progress --password-file=/home/hadoop/rsyncd.passwd hadoop@192.168.1.98::auth_data \
     /home/hadoop/auth_data
    sleep 1s #暂停1s  
done  
```





