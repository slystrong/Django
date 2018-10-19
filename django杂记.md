#### 一.时间问题

在django中获取当前时间时,如果使用pyhon提供的datetime.datetime.now(),就会出现警告( RuntimeWarning: DateTimeField Org.updatedAt received a naive datetime )不是带时区的natice time,因此一般就是用django自带的timezone.now() 获取到带时区的时间,然后调用timezone.localtime()进行转换,详细代码如下:

```
from django.utils import timezone 
now = timezone.now()  # 获取到带时区的时间
timezone.localtime(now)  # 转换为本地时间 UTC+8时间
strftime("%Y-%m-%d") # 转换为字符串格式的时间 2018-08-23
strftime("%Y%m%d") # 转换为字符串格式的时间 20180823
```

python中时间日期格式化符号：

%y 两位数的年份表示（00-99）

%Y 四位数的年份表示（000-9999）

%m 月份（01-12）

%d 月内中的一天（0-31）

%H 24小时制小时数（0-23）

%I 12小时制小时数（01-12）

%M 分钟数（00=59）

%S 秒（00-59）

#### 二.自定义模板函数

（1）在app目录里创建templatetags目录（名字唯一）

　　（2）在templatetags下创建任意py文件（xx.py）

　　（3）创建templa对象 register

　　（4）编写函数

```
from django import template
register=template.Library() #对象名必须为register

@register.simple_tag()

def funcname(a1,a2):

   return a1+a2

```

#### 三.python浮点数保留2位小数

```
number = round(number, 2)
```

#### 四.django将从数据库获取到的数据按字段排序

```
Student.objects.all().order_by('s_age') # 将学生按s_age从小到大排序
Student.objects.all().order_by('-s_age') # 将学生按s_age从大到小排序
```

#### 五.在django中保持按需求分别使用几种http请求方式POST(创建,插入),GET(查询),PUT(更新),DELETE(删除)

#### 六.通过post表单上传文件

```
<form action="xxx" method="post" enctype="multipart/form-data">
    <input id="fileup" type="file" name="file"/>
    <input name="submit" type="submit" value="上传文件"/>
</form>

*** 别忘记给form设置enctype属性为multipart/form-data
***还要配置文件存放路径
```

#### 七.GET请求通过url获取值的几种方式(不适合2.0以上的django)

```
/home/home/(?p<typeid>\d+)/(?p<cid>\d+)/(?p<sid>\d+)/  # 通过指定字段来获取对应值,位置不影响
/home/home/(\d+)/(\d+)/(\d+)/  # 通过位置来获取传参
```

#### 八.邮箱正则

###### **分析邮件名称部分：**

- 26个大小写英文字母表示为`a-zA-Z`
- 数字表示为`0-9`
- 下划线表示为`_`
- 中划线表示为`-`
- 由于名称是由若干个字母、数字、下划线和中划线组成，所以需要用到`+`表示多次出现

 根据以上条件得出邮件名称表达式：`[a-zA-Z0-9_-]+` 

###### **分析域名部分：**

 一般域名的规律为“[N级域名][三级域名.]二级域名.顶级域名”，比如“qq.com”、“www.qq.com”、“mp.weixin.qq.com”、“12-34.com.cn”，分析可得域名类似“`**` `.**` `.**` `.**`”组成。

- “**”部分可以表示为`[a-zA-Z0-9_-]+`
- “.**”部分可以表示为`\.[a-zA-Z0-9_-]+`
- 多个“.**”可以表示为`(\.[a-zA-Z0-9_-]+)+`

 综上所述，域名部分可以表示为`[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+`

###### **最终表达式：** 

 由于邮箱的基本格式为“名称@域名”，需要使用“^”匹配邮箱的开始部分，用“$”匹配邮箱结束部分以保证邮箱前后不能有其他字符，所以最终邮箱的正则表达式为： 
  `^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$`

允许名称为汉字的情况:

`^[A-Za-z0-9\u4e00-\u9fa5]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$ `

汉字区间:[\u4e00-\u9fa5] 

#### 九.Django的server是单线程的

django 原生为单线程序，当第一个请求没有完成时，第二个请求辉阻塞，知道第一个请求完成，第二个请求才会执行。

可以使用uwsgi  编程多并发的。

django 的并发能力真的是令人担忧，这里就使用 nginx + uwsgi 提供高并发。

nginx 的并发能力超高，单台并发能力过万（这个也不是绝对），在纯静态的 web 服务中更是突出其优越的地方，由于其底层使用 epoll 异步IO模型进行处理，使其深受欢迎。

做过运维的应该都知道，php 需要使用 nginx + fastcgi 提供高并发，java 需要使用 nginx + tomcat 提供 web 服务,而python则是 nginx + uwsgi 为 django 提供高并发 web 服务。
