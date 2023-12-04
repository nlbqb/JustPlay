# java后端

## java类型转换

### 1.Date转String

```java
new SimpleDateFormat("yyyy-MM-dd").format(#)  //#:Date类型的变量
```

### 2.String转Date

```java
new SimpleDateFormat("yyyy-MM-dd").parse(#)  //#:String类型的变量
```

### 3.JsonObject转java对象

```java
MsFinishProject msFinishProject = JSON.toJavaObject(formData, MsFinishProject.class);
```

### 4.java对象转JsonObject

```java
JSONObject jsonObject = JSONObject.parseObject(JSON.toJSONString(detail));
```



## java类型转换

# Linux

## 部署

### Linux架构

![img](https://img-blog.csdnimg.cn/4c458d36fd4848a48dc811da4652089a.png)

### cd命令



### 1.前台部署

```
java -jar xxx.jar
```

### 2.后台部署

```
nohup java -jar /appdata/dtsum/aoya/aoya-3.2.4-7776.jar 2>&1 &

后台模式启动时2>&1 &的含义

“2”：代表标准错误输出（stderr）
“1”：代表标准输出（stdout）
“>&”：代表合并输出文件 （参考1、参考2）
最后的"&"：代表把命令放到后台执行
2>&1含义：将标准错误输出重定向到标准输出
```



## 常用命令

### 1.cd命令

[cd命令详解](https://blog.csdn.net/bk_hyj/article/details/94629845)

### 2.删除命令

```
rm sql      //删除文件
rm -f sql   //强制删除文件

rm -r backup      //删除文件夹，及其子目录和文件
rm -rf backup     //强制删除文件夹，及其子目录和文件
```

### 3.创建文件夹

```
mkdir backup    //创建一个文件夹

mkdir -p backup/sql     //创建文件夹及其子文件夹
```

### 4.编辑文件

```
vi/vim backup.sh    //编辑一个文件
```

#### 4.1编辑完后的操作

```
按Esc退出编辑模式

:q    //退出
:q!   //强制退出
:wq   //保存并退出

```

### 5.查看文件大小

```
ls -lh
```



## 数据库定时备份

**1.新建shell文件(脚本)**

目录结构：liufeng ---- backup ---- sql

​													 ---- backup.sh

 							    ---- web

​							     ---- liufeng-8801-1.04.jar

```
1、新建shell文件
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# vim backup.sh
#...进入文件编辑界面，增加以下内容：
{
#!/bin/sh
mysqldump -uroot -pdtsum123456 liufengerp | gzip > /home/liufeng/backup/sql/liufeng_`date +%Y%m%d%H%M%S`.sql.gz
find /home/liufeng/backup/sql/ -mtime +10 -type f | xargs rm -f 
}
```

**2.crontab开启定时任务，执行shell脚本**

```
#查看是否开启了crontab
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# crontab -l
no crontab for root

#创建一个crontab，每分钟执行
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# crontab -e
#...进入文件编辑界面，增加以下内容：
{
#定期执行数据库和文件的备份工作
0 1 * * * sh /home/liufeng/backup/backup.sh    //0 1 * * *表示每天凌晨1点执行一次
}
```

**3.查看crontab服务是否启动，启动crontab服务**

```
[root@ecs-d532-1228628 backup]# service crond status    //查看crontab服务状态
[root@ecs-d532-1228628 backup]# service crond restart   //重启crontab服务
```



# 数据库

## mysql

### 1.insert

```mysql
INSERT INTO user (id,name,age,email) 
VALUES(1,'Jone',18,'test1@baomidou.com'),
	  (,,,)
```

### 2.insert(整行)

```mysql
INSERT INTO user 
select 1,'Jone',18,'test1@baomidou.com'

union

select 2,'Jo',19,'sest1@basmidou.com'
```

### 3.insert(批量)

```mysql
<insertid="batchInsert"parameterType="java.util.List">    //传的是list
    insert into USER (id,name)
    values 
        <foreachcollection="list" item="model" index="index" separator=","> 
        (#{model.id},#{model.name}) 
        </foreach> 
</insert>
```

### 4.insert(若与**唯一** unique_code索引一样，则按unique_code更新)

```mysql
insert into book(
    unique_code,
    book_name
) values (
    'zs-001',
    '中文'
) on duplicate key update book_name='数学';
```

# Redis

## 基础命令

### 0.应用场景

![img](https://img2018.cnblogs.com/blog/798174/201903/798174-20190321203330649-499029266.png)

### 1.客户端登录

```
redis-cli -h 127.0.0.1 -p 6379
```

### 2.查询所有的keys

```
keys *
```

# GIT

## 1.新建项目

1.1 GitHub上新建仓库、分支

1.2 运行git clone https://github.com/xxx/xxx.git，将远程的项目同步到本地电脑

## 2.常用命令

```xml-dtd
git init: 使文件夹被git管理起来

git branch 查看本地分支
git branch -r 查看远程分支
git branch -a 查看本地和远程所有分支

git checkout java 切换分支
```

