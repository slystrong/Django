### 一.关于cookis和session

cookie的工作原理是：由服务器产生内容，浏览器收到请求后保存在本地；当浏览器再次访问时，浏览器会自动带上cookie，这样服务器就能通过cookie的内容来判断这个是“谁”了。

我们可以给每个客户端的cookie分配一个唯一的id，这样用户在访问时，通过cookie，服务器就知道来的人是“谁”。然后我们再根据不同的cookie的id，在服务器上保存一段时间的私密资料，如“账号密码”等等。

总结而言：cookie弥补了http无状态的不足，让服务器知道来的人是“谁”；但是cookie以文本的形式保存在本地，自身安全性较差；所以我们就通过cookie识别不同的用户，对应的在session里保存私密的信息以及超过4096字节的文本。

另外，上述所说的cookie和session其实是共通性的东西，不限于语言和框架

用户登录成功后，服务器下发一个（通常是加密了的）Cookie 文件。
客户端（通常是网页浏览器）将收到的 Cookie 文件保存起来。

![5.1](C:\Users\13677\Desktop\第三阶段\res\5.1.png)

**cookie具体实现**

**session具体实现**

二:防止跨站域请求伪造

在表单form里面添加csrf_token认证.{% csrf_token %},

原理:在访问页面时,服务器会返回一个随机数,当表单提交时,服务器会验证这个随机数是否正确,从而防止请求伪造.

三.django自带的管理后台登录,注销

```
在setting里面配置
LOGIN_URL = '/blogs/login'
如果没有登录直接访问在url里面配置的,则跳转到登录页.
例如: url(r'index/', login_required(views.index), name='index'),
不登录,直接访问index,则会跳转到login

# 验证用户名或密码是否正确
user = auth.authenticate(request, username=name, password=password)
# 登录
auth.login(request, user)
# 注销
auth.logout(request)
# 判断密码是否匹配,返回boolean
check_password(oldpwd, user.password)
# 按格式加密密码
user.password = make_password(newpwd, None, 'pbkdf2_sha256')
# 创建django密码， 第二个参数为None是每次产生的密码都不同，第三个参数为算法， 后面两个参数可以忽略,默认的Ddjango使用pbkdf2_sha256方式来存储和管理用的密码，当然是可以自定义的。
django提供的算法有:
pbkdf2_sha256
pbkdf2_sha1
bcrypt_sha256
bcrypt
sha1
unsalted_md5
crypt
# 更新数据库密码
user.save()
```

