#### 有关sql语句的使用记录

1.select user,host from mysql.user(查看此数据库中有的用户)

host 显示user可以从哪里连接mysql server     %意味着任意ip都可以，localhost意味着只能从本地连接。

2.create USER ‘xxx@localhost’ identified by ‘yourpassword’;(创建用户，名字，密码)

3.grant all privileges on *.* to 'xxx' with grant option;(给予xxx用户 所有权限)

4.flush privileges(刷新用户的拥有的权限)

