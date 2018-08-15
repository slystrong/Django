1.

```
在urls里面
1.11 from django.conf.urls import url
2.0.5 from django.urls import path
```

2.

```
在设置级联关系ForeignKey(),OneToOneField()
1.11 可以不用设置on_delete参数默认CASCADE
2.0.5 必须设置on_delete参数
```

3

```
在不使用关键字传值时 
直接传值:<a href="/app/addStudentCourse/{{stu.id}}/">添加课程</a>
1.11 url(r'addStudentCourse/(\d+)/', views.add_student_course)
2.0.5 path('addStudentCourse/<int:id>/', views.add_student_course)
```

