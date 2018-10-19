jQuery选择器

```
jQuery 元素选择器
    jQuery 使用 CSS 选择器来选取 HTML 元素。
    $("p") 选取 <p> 元素。
    $("p.intro") 选取所有 class="intro" 的 <p> 元素。
    $("p#demo") 选取所有 id="demo" 的 <p> 元素。
jQuery 属性选择器
    jQuery 使用 XPath 表达式来选择带有给定属性的元素。
    $("[href]") 选取所有带有 href 属性的元素。
    $("[href='#']") 选取所有带有 href 值等于 "#" 的元素。
    $("[href!='#']") 选取所有带有 href 值不等于 "#" 的元素。
    $("[href$='.jpg']") 选取所有 href 值以 ".jpg" 结尾的元素
    $("[href^='jpg']") 选取所有 href 值以 "jpg" 开始的元素
    $("[href*='.jpg']") 选取所有 href 值包含 ".jpg" 字段的元素
```

[jQuery选择器]: https://www.jb51.net/article/92064.htm	"jQuery选择器"

js弹出命令提示窗口,并带有取消,确定按钮

```

var r=confirm("是否跳转链接");
if (r==true)
  {
  window.location.href='https://github.com/slystrong';
  }
else
  {
  alert("已取消跳转!");

```



[jQuery事件处理]: https://www.runoob.com/manual/jquery/

JS强制刷新页面

```
location.reload()  # 重新加载
```

关于input radio单选

```
1.获取选中值，三种方法都可以：
    $('input:radio:checked').val()；
    $("input[type='radio']:checked").val();
    $("input[name='rd']:checked").val();
2.设置第一个Radio为选中值：
   $('input:radio:first').attr('checked', 'checked');
   $('input:radio:first').attr('checked', 'true');
   注：attr("checked",'checked')= attr("checked", 'true')= attr("checked", true)
3.设置最后一个Radio为选中值：
    $('input:radio:last').attr('checked', 'checked');
    $('input:radio:last').attr('checked', 'true');
4.根据索引值设置任意一个radio为选中值：
	$('input:radio').eq(索引值).attr('checked', 'true');索引值=0,1,2....
	$('input:radio').slice(1,2).attr('checked', 'true');
5.遍历Radio
    $('input:radio').each(function(index,domEle){
         //写入代码
    });
```

