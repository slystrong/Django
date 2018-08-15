一.使用django自带管理后台时:

可以通过获取管理员对象来进行对管理信息的修改

```
# 导入User类
from django.contrib.auth.models import User
# 导入检查密码是否匹配以及加密密码的方法
from django.contrib.auth.hashers import make_password, check_password
user = User.objects.all().first()
check_password(oldpwd, user.password)
make_password(newpwd, None, 'pbkdf2_sha256')

```

二.

```
模板变量{{}}的内容，如果含html的话，django的模板系统默认会对输出进行转义，比如把<p>转义成了&lt;p&gt; ，然后再显示出来的时候就如实地显示为<p>。要解决这个问题只要把默认的 转义去掉就好了。比如原本我们的模板代码是这样的：{{post.content}}   
现在我们把它变成这样：  
{% autoescape off %}   
{{post.content}}   
{% endautoescape %}  
```

**注:在django的templates里面的所有html文件的编码必须要先转成utf-8的格式,注意是文件的编码格式**

三.

当想要设置主键从指定数开始增长时,可以先用代码手动添加一个对象并设置其主键为想要设置的指定数,执行后就可以了.