<a name="RrIEy"></a>
# Mybatis & jdbc & mysql
1. MYSQL 登录 erro 1045  --- 忘记密码/密码错误
> **重置密码**
> - 方法一：使用 --init-file 选项
>    - 创建一个文本文件，例如 init-file.txt ，并在其中写入以下内容： **ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';**
>    - 关闭MySQL服务。
>    - 在CMD中输入以下命令： **mysqld --user=mysql --init-file=/home/user/init-file.txt --console**
>    - 这将启动MySQL服务，并执行您创建的init文件，从而更新root用户的密码。
>    - 重置密码后，请删除该文件，并停止服务器并正常启动它。
> - 方法二：使用 --skip-grant-tables 选项
>    - 关闭MySQL服务。
>    - 在CMD中输入以下命令： **mysqld --console --skip-grant-tables --shared-memory**
>    - 这将启动MySQL服务，并跳过授权表，使您可以以root身份从本机直接登录。
>    - 在另一个CMD窗口中，输入以下命令： **mysql** **FLUSH PRIVILEGES;** **ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';** **exit;**
>    - 这将连接到MySQL服务，并重置root用户的密码。
>    - 修改密码后，请停止服务器并正常启动它。

2. sql语句正确但查询不出数据/insert数据在其他应用里乱码 --- mysql编码与idea编码不一样
   1. 在idea mybatis配置里修改
   2. `url += "useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8`,修改查询传输时指定编码
3. 动态sql书写，使用标签正确，报错sql语法错误
   1. 在使用动态sql语法时也要把对应符号添加到xml的sql语句中
   2. 例如`,  ; and`等的使用
<a name="CvvJA"></a>
# [解决Illegal mix of collations (latin1_swedish_ci,IMPLICIT) and (utf8_general_ci,COER](https://www.cnblogs.com/zjoch/p/5526969.html)）

1. 更改项目排序编码和使用编码

`alter table tb_user convert to character set utf8;`

2. 修改默认字符集<br />发现关键项目已经用了utf8，但这样还不够，还要保证客户端也是用utf8的字符集来操作的。登录的时候，要用以下命令：mysql --default-character-set=utf8 -u root -p
```sql
1. 检查排序编码
SHOW VARIABLES LIKE 'collation_%';

set character_set_server =utf8;

SET collation_server = utf8_general_ci;

SET collation_database = utf8_general_ci;
# 确认修改成功
SHOW VARIABLES LIKE 'collation_%';
2. 检查字符集编码
SHOW VARIABLES LIKE 'character_set_%';
+--------------------------+----------
| Variable_name | Value |
+--------------------------+---------

| character_set_client | utf8|

| character_set_connection | utf8|

| character_set_database | latin1 |

| character_set_results | utf8|

| character_set_server | latin1 |

| character_set_system | utf8 |

| character_sets_dir | C:\Program Files\MySQL\MySQL Server 5.7\share\charsets\

3. 修改表的所有字符集为uft-8
alter table tb_user convert to character set utf8;
```
