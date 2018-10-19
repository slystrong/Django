[TOC]



#### 一.model

#### 二.form

#### 三.跨站请求伪造csrf

##### 3.1 csrf攻击说明

```
1.用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A;

2.在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A;

3.用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B;

4.网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A;

5.浏览器在接收到这些攻击性代码后，根据网站B的请求，在用户不知情的情况下携带Cookie信息，向网站A发出请求。网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。
```

csrf的攻击之所以会成功是因为服务器端身份验证机制可以通过Cookie保证一个请求是来自于某个用户的浏览器，但无法保证该请求是用户允许的。因此，预防csrf攻击简单可行的方法就是在客户端网页上添加随机数，在服务器端进行随机数验证，以确保该请求是用户允许的。Django也是通过这个方法来防御csrf攻击的。

理解如下图：

![3-1](res\进阶\3-1.png)

Django中防止跨站请求伪造使用CSRF, 即：客户端访问服务器端，在服务器端正常返回给客户端数据的时候，而外返回给客户端一段字符串，等到客户端下次访问服务器
端时，服务器端会到客户端查找先前返回的字符串，如果找到则继续，找不到就拒绝。

##### 3.2 配置以及使用

Django中配置：

1. 在表单中添加
   {% csrf_token %}
2. 在settings中的中间件MIDDLEWARE中配置打开
   ‘django.middleware.csrf.CsrfViewMiddleware’

#### 四.Cookie

##### 4.1 描述

```
浏览器端的回话技术
cookie本身由浏览器生成，通过Response将cookie写在浏览器上，下一次访问，浏览器会根据不同的规则携带cookie过来
```

##### 4.2 cookie方法

```
设置：response.set_cookie(key, value, max_age=None, exprise=None)

获取：request.GET.get(key)

删除：request.delete_cookie(key)
```

注意：cookie不能跨浏览器

参数定义：
	
max_age和exprise时间：

```
max_age :  整数，指定cookie过期时间，以秒为单位

exprise： 整数，指定过期时间，还支持是一个datetime或者timedelta，可以指定一个具体日期时间
```

​    
设置10天后过期：

```
exprise=datetime.datetime.now() + timedelta(days=10) 10天后过期
```

永不过期：

```
exprise设置为None表示为永不过期
```

#### 五.Session

##### 5.1 描述

```
服务端会话技术，依赖于cookie
```

##### 5.2 开启session设置

1）django中启用SESSION

在settings中修改如下地方

```
INSTALLED_APPS:
    ‘django.contrib.sessions’

MIDDLEWARE:
    ‘django.contrib.sessions.middleware.SessionMiddleware’
```

每个HttpResponse对象都有一个session属性，也是一个类字典对象

##### 5.3 常用操作

```
request.session[‘user’] = username 设置数据

request.session.get(key, default=None) 根据键获取会话的值

request.session.flush() 删除当前的会话数据并删除会话的cookie，django.contrib.auth.logout() 函数中就会调用它。

request.session.session_key  获取sessionid值

request.session.delete(request.session.session_key) 删除当前用户的所有Session数据

del request.session['key'] 删除session中的key值
```

```
数据存储到数据库中会进行编码使用的是base64
```

代码如下：

```
from django.http import HttpResponseRedirect
from django.shortcuts import render
from django.urls import reverse
```

​	

```
def index(request):
    if request.method == 'GET':
        username = request.session.get('username')
        # username = request.session['username']
        print(username)
        # 获取session_key
        session_key = request.session.session_key
        print(session_key)
        # 删除session整条记录
        request.session.delete(session_key)
        # 删除session中的数据
        del request.session['username']
        username = request.session.get('username')
        print(username)
        return render(request, 'index.html')
```

​	

```
def setCookie(request):
    if request.method == 'GET':
        res = HttpResponseRedirect(reverse('myapp:index'))
        # 设置cookie值
        # set_cookie(key, value, max_age, expires)

        # 删除：delete_cookie(key)  set_cookie(key, value, max_age=0)
        # res.set_cookie('session_id', '12t861ihafiagdfi', max_age=3)
        # res.delete_cookie('session_id')

        # 设置cookie中的值，并且设置session中的值
        # 登录的时候，进行验证用户的用户名和密码是否正确，如果正确
        request.session['login'] = True
        request.session['username'] = '张三'
        request.session['password'] = '123456'
        return res
```

注意:

```
Session 支持中文  cookie不支持中文   token自己维护
```

可以自定义session

![5-1](res\进阶\5-1.png)

然后使用中间件来自定义session处理

此部分详细可以参考 **中间件** 部分

#### 六.分页

##### 6.1 安装dj-pagination  

```
pip install dj-pagination  
```

##### 6.2 加入app

```
INSTALLED_APPS += (
    'dj_pagination' ，
)
```

##### 6.3 加入中间件

Django 版本低于1.10 使用 MIDDLEWARE_CLASSES

```
MIDDLEWARE += ( 
    'dj_pagination.middleware.PaginationMiddleware' ，
)
```

##### 6.4 分页相关语法

###### 6.4.1 分页库Paginator的基本语法

django提供了分页的工具，存在于django.core中

```
Paginator： 数据分页工具
Page：具体的某一页
```

Paginator：

```
对象创建： Paginator(数据集，每一页数据)
```

属性：

```
count  计算和

num_pages: 页面总和

page_range: 页码列表，从1开始
```

方法：

```
page(页码)：获取的一个page对象，页码不存在则抛出invalidPage的异常
```

###### 6.4.2 常见错误

```
invalidPage：page()传递无效页码

PageNotAnInteger：Page()传递的不是整数

Empty:page()传递的值有效，但是没有数据
```

###### 6.4.3 page对象

page：

```
对象获取，通过Paginator的page()方法获得
```

属性：

```
object_list: 当前页面上所有的数据对象
number： 当前页的页码值
paginator: 当前page关联的Paginator对象
```

方法：

```
has_next()   判断是否有下一页
has_previous():  判断是否有上一页
has_other_pages():  判断是否有上一页或下一页
next_page_number();  返回下一页的页码
previous_page_number(): 返回上一页的页码
len(): 返回当前也的数据的个数
```

###### 6.4.4 简单使用代码如图

![6-1](res\进阶\6-1.png)

可以选择使用默认模板以及自定义模板

#### 七.缓存

#### 八.序列化

#### 九.信号

#### 十.动态url

#### 十一.中间件

##### 11.1. 中间件Middleware

中间件：

a) 是一个轻量级的，底层的插件，可以介入Django的请求和响应的过程（面向切面编程)

b) 中间件的本质就是一个python类

c) 面向切面编程(Aspect Oriented Programming)简称AOP，AOP的主要实现目的是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获取逻辑过程中各部分之间低耦合的隔离效果

思考：

什么是中间件，在settings.py中有很多的中间件，主要是用来做什么功能的呢，他们处理请求的url的过程在那些阶段呢，一般用来做那些数据的处理呢

##### 11.2 中间件的处理函数

```
__init__：没有参数，在服务器响应的第一个请求的时候自动调用，用户确定时候启动该中间件

process_request(self, request): 在执行视图前被调用，每个请求上都会被调用，不主动进行返回或返回HttpResponse对象

process_view(self, request, view_func,view_args, view_kwargs):调用视图之前执行，每个请求都会调用，不主动进行返回或返回HttpResponse对象

process_template_response(self, request, response)：在视图刚好执行完后进行调用，每个请求都会调用，不主动进行返回或返回HttpResponse对象

process_response(self, request, response):所有响应返回浏览器之前调用，每个请求都会调用，不主动进行返回或返回HttpResponse对象

process_exception(self, request, exception):当视图抛出异常时调用，不主动进行返回或返回HttpResponse对象
```

##### 11.3 处理流程

![11-1](res\进阶\11-1.png)

##### 11.4 自定义中间件流程

1. 在工程目录下创建middleware目录
2. 目录中创建一个python文件
3. 在根据功能需求，创建切入需求类，重写切入点方法
4. 定义如图:

![11-2](res\进阶\11-2.png)

5. 启动中间件，在settings中进行配置，MIDDLEWARE中添加middleware.文件名.类名

![11-3](res\进阶\11-3.png)

#### 十二.日志

#### 十三.文件上传以及图片处理

##### 0. 安装处理图片的库

```
pip install Pillow
```

##### 1. 修改配置信息

在工程目录中，修改setting.py文件，在最后面加入一下配置信息：

![13-1](res\进阶\13-1.png)

在urls.py文件中加入信息
	urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

在工程目录下的urls.py文件中配置media的目录为静态目录

先导入static的包：

![13-1-2](res\进阶\13-1-2.png)

设置media：

![13-1-3](res\进阶\13-1-3.png)

##### 2. 定义模型

在模型中加入ImageFiled字段，并且指定上传的图片的![13-2](res\进阶\13-2.png)保存路径

##### 3. 在页面中传递图片信息，和用户名信息

![13-3](res\进阶\13-3.png)

##### 4. 在服务端接受请求，并且保存图片信息

![13-4](res\进阶\13-4.png)

##### 5. 页面展示

![13-5](res\进阶\13-5.png)

#### 十四.编辑器添加图片(kindeditor编辑器)

##### 1.下载资源

​	下载kindeditor相关的资源包(https://pan.baidu.com/s/1Oejei8YSC4DbAhqbJ5HCGQ),在项目中配置好static路径后,将资源包放在static目录下。

##### 2.在项目中编写功能函数

​	先创建utils文件夹,然后创建一个upload_image.py(文件名随意,后面配置需要用)文件,

```python
# -*- coding: utf-8 -*-
from django.http import HttpResponse
from django.conf import settings
from django.views.decorators.csrf import csrf_exempt
import os
import uuid
import json
import datetime as dt


@csrf_exempt
def upload_image(request, dir_name):
    ##################
    #  kindeditor图片上传返回数据格式说明：
    # {"error": 1, "message": "出错信息"}
    # {"error": 0, "url": "图片地址"}
    ##################
    result = {"error": 1, "message": "上传出错"}
    files = request.FILES.get("imgFile", None)
    if files:
        result =image_upload(files, dir_name)
    return HttpResponse(json.dumps(result), content_type="application/json")


#目录创建 可以自定义编辑器上传的图片路径
def upload_generation_dir(dir_name):
    today = dt.datetime.today()
    dir_name = dir_name + '/%d/%d/' %(today.year,today.month)
    if not os.path.exists(settings.MEDIA_ROOT + dir_name):
        os.makedirs(settings.MEDIA_ROOT + dir_name)
    return dir_name

# 图片上传
def image_upload(files, dir_name):
    #允许上传文件类型
    allow_suffix =['jpg', 'png', 'jpeg', 'gif', 'bmp']
    file_suffix = files.name.split(".")[-1]
    if file_suffix not in allow_suffix:
        return {"error": 1, "message": "图片格式不正确"}
    relative_path_file = upload_generation_dir(dir_name)
    path=os.path.join(settings.MEDIA_ROOT, relative_path_file)
    if not os.path.exists(path): #如果目录不存在创建目录
        os.makedirs(path)
    file_name=str(uuid.uuid1())+"."+file_suffix
    path_file=os.path.join(path, file_name)
    file_url = settings.MEDIA_URL + relative_path_file + file_name
    open(path_file, 'wb').write(files.file.read()) # 保存图片
    return {"error": 0, "url": file_url}

```

##### 3.配置图片上传请求

​	在项目主urls.py中加入代码:

```python
   urlpatterns = [
   ...
       # kindeditor编辑器上传图片地址
    url(r'^util/upload/(?P<dir_name>[^/]+)$', upload_image, name='upload_image'),
    ]
```

##### 4.在html页面中使用

​	首先在js脚本标签中加入配置,注意顺序不能调换

```html
    {% load static %}
    <script type="text/javascript" src="{% static 'kindeditor/kindeditor-all.js' %}"></script>
    <script type="text/javascript" src="{% static 'kindeditor/lang/zh-CN.js' %}"></script>
    <script type="text/javascript">
        KindEditor.ready(function(K) {
                window.editor = K.create('#editor_id',{  <!-- id与下面使用对齐 -->
                    uploadJson:'/util/upload/kindeditor'
                });
        });
    </script>
    ....
    <!-- 然后就添加textarea,并设置id为editor_id -->
     <textarea id="editor_id" name="content" style="width:700px;height:300px;">
                                {% if goods.particulars %}
                                    {{ goods.particulars|safe }}
                                {% endif %}
                            </textarea>
```

5.保存到数据库

```python
# 模型定义:particulars = models.TextField(null=True)  # 商品详细介绍
===========================
# 和保存文本内容一样
 content = request.POST.get('content')
 goods_id = request.GET.get('goodsId')
 # 获取需要编辑的商品，并且使用save()，保存商品的描述内容
 goods = Goods.objects.filter(id=goods_id).first()
 goods.particulars = content
 goods.save()
```

#### 十五.使用session保存临时数据

​	**需求:**

​		实现未登录,将商品加入购物车,登录后,将购物车数据更新到用户自己的购物车中

​	**思路:**

​		可以将临时数据保存到session当中,这样当登录之后就可以将临时数据更新成用户数据

​	**具体实现:**

​	session保存到数据的django_session表中

```
# 保存数据到session中:
goods_list = []
goods_data = [12345, 254]
goods_list.append(goods_data)
request.session['goods'] = goods_list
# 获取数据
request.session.get('goods')

```

