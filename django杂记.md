#### 一.时间问题

在django中获取当前时间时,如果使用pyhon提供的datetime.datetime.now(),就会出现警告( RuntimeWarning: DateTimeField Org.updatedAt received a naive datetime )不是带时区的natice time,因此一般就是用django自带的timezone.now() 获取到带时区的时间,然后调用timezone.localtime()进行转换,详细代码如下:

```
from django.utils import timezone 
now = timezone.now()  # 获取到带时区的时间
timezone.localtime(now)  # 转换为本地时间 UTC+8时间
```

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


　