#### 一.关于不使用form表单进行post请求报错403

报错403因为django中间件对POST请求进行了验证,防止跨站域请求

```js
需要在网页添加{% csrf_token %},获取服务器发送的随机字符串,
然后在消息头里面设置X-CSRFToken的值为我们获取到的随机字符串.
用ajax示例:
var csrf = $('input[name="csrfmiddlewaretoken"]').val()
$.ajax({
    url:'/app/student',
    type:'POST',
    dataType:'json',
    data:{},
    headers:{'X-CSRFToken':csrf},
    success:function(){},
    error:function(){},
    
});
```

ajax:

```
$.get(url, function(data){});
$.post(url, {'key': value}, function(data){});
$.ajax({
    url:'',
    data:{},
    dataType:'json',
    headers:{'X-CSRFToken':''}, # 遇到403就需要传回去
    type:'POST/PUT/PATCH/DELETE/GET',
    success:function(data){},
    error:function(data){}
});
```



#### 二.rest_framework风格

返回数据格式:

```
正常返回:
[
    data:[
        {
            'id':2,
            's_name':'小红',
        },
        {
            'id':3,
            's_name':'小明',
        }
    ],
    code: 200,
    msg: 'success'
]
错误返回:
[
    data:[],
    code: 203,
    msg: '请求错误!'
]
```

可以自定义改造返回

创建utils文件夹,以及自定义返回的returnRenderer.py文件

```
from rest_framework.renderers import JSONRenderer


class MyJsonRenderer(JSONRenderer):
    """
    {
        code:0,
        msg:'信息',
        data:data
    }
    """
    def render(self, data, accepted_media_type=None, renderer_context=None):
        # 第一次改造
        if isinstance(data, dict):
            code = data.pop('code', 200)
            msg = data.pop('msg', '请求成功')
        else:
            code = 200
            msg = '请求成功'
        res = {
            'code': code,
            'msg': msg,
            'data': data
        }
        return super().render(res)

```

在setting中配置

```
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': (
        'utils.returnRenderer.MyJsonRenderer',
    ),
}
```

在views进行重构RetrieveModelMixin.retrieve方法

```
    # 重构
    def retrieve(self, request, *args, **kwargs):
        # 第二次改造
        try:
            instance = self.get_object()
            serializer = self.get_serializer(instance)
            return Response(serializer.data)
        except:
            data = {
                'code': 203,
                'msg': '学生不存在'
            }
        return Response(data)
```

核心:资源丶状态转移丶接口统一

GET /app/students/

GET/PUT/PATCH/DELETE/ /app/students/[id]/

POST /app/students/