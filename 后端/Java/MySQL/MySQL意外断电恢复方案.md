# MySQL意外断电恢复方案

## 第一种：

​	

```
1.备份数据库data文件夹，删除data文件夹中的所有数据。
2.mysql/bin目录执行	mysql --initialize
3.尝试启动服务
4.若服务可以正常开启，则关闭服务，将备份的data文件夹替换过去（ib_logfile0,ib_logfile1 两个文件不要复制。
5.重启服务
```

## 第二种：

```
1.修改my.cnf文件，在my.cnf中的[mysqld]中添加：
	innodb_force_recovery = 6
	innodb_purge_threads = 1
2.使用原来的data文件
3.启动服务将数据库导出sql
4.mysql/bin目录执行  mysql --initialize
	使用初始密码登录mysql
	执行命令 set password=password('ykocs12#$');
	修改密码 update user set authentication_string = password('newpass') where use = 'root';
	修改远程访问权限 update user set host = '%' where user = 'root'; (使mysql root用户可以远程访问)
	刷新权限  flush privileger;
5.重新新建库运行sql备份文件
	执行备份文件可能出错的元婴
	1.导出的sql文件中 default null 需要替换为 null default null
	2.view_itcode 需放在view_itcode03之后
```

