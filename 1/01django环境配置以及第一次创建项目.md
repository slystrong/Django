一.公司类型介绍

​	1.业务公司
	2.技术公司	知道创宇	云和恩墨
	3.销售型公司

二.教学大纲

​	Django 3周	前两周基础+项目1周
	flask 2周	第一周基础+1周项目
	爬虫 2周
三.主流框架
	Django Flask tornado web.py twisted sanic
四.MVC
	M:
	C:业务逻辑
	V:视图
五.Django优缺点
	采用的是MVT模式
	M:负责业务与数据库的对象
	V:负责业务逻辑并适当调用Model和Template
	T:把页面渲染展示给用户
六.安装django
	安装virtualenv	pip install virtualenv
	创建虚拟环境 	virtualenv --no-site-package venv

![1.0](C:\Users\13677\Desktop\第三阶段\res\1.0.png)	安装django	pip install django==1.11
	安装pymysql	pip install pymysql
	创建新项目	django-admin startproject day1

七.在pycharm可以自定义运行方式

1.用命令启动:

```
python manage.py runserver[[127.0.0.1]8080]
```

2.配置pycharm,快捷运行

![01](C:\Users\13677\Desktop\第三阶段\res\1.1.png)

如果返回字符串,运行时报str对象没有get方法,是因为没有导入httpResponse,返回应该通过httpResponse方法返回

八.添加mysql(使用django自带的管理后台实验)

在项目里面的init.py文件里面添加

```
import pymysql
pymysql.install_as_MySQLdb()
```

在setting里面配置mysql数据库链接(最后新创建一个数据库)![2](C:\Users\13677\Desktop\第三阶段\res\1.2.png)

创建表

```
 python manage.py migrate(将需要的表创建到配置的数据库里面)
```

创建超级管理员

```
python manage.py createsuperuser(创建超级管理员用户)
```

第一次迁移

```shell
python manage.py migrate
```

后面再次迁移

```shell
python manage.py makemigrations
python manage.py migrate
```

