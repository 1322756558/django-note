### 在django防御csrf攻击

#### 原理

在客户端页面上添加csrftoken, 服务器端进行验证，服务器端验证的工作通过'django.middleware.csrf.CsrfViewMiddleware'这个中间层来完成。在django当中防御csrf攻击的方式有两种, 1.在表单当中附加csrftoken 2.通过request请求中添加X-CSRFToken请求头。注意:Django默认对所有的POST请求都进行csrftoken验证，若验证失败则403错误侍候。

#### 在表单中附加csrftoken

**后端**

```
from django.shortcuts import render
from django.template.context_processors import csrf

def ajax_demo(request):
     # csrf(request)构造出{‘csrf_token’: token}
    return render(request, 'post_demo.html', csrf(request))
```

**前端**

```
  $('#send').click(function(){
                
    $.ajax({
        type: 'POST',
        url:'{% url 'ajax:post_data' %}',
        data: {
                username: $('#username').val(),
                content: $('#content').val(),
               'csrfmiddlewaretoken': '{{ csrf_token }}'  关键点
            },
        dataType: 'json',
        success: function(data){

        },
        error: function(){
    
        }
    
    });
  });
```

#### 通过request请求中添加X-CSRFToken请求头

**后端**

该方式需要借助于Cookie传递csrftoken, 设置Cookie的方式有两种。ps:经测试即便什么都不做，也会设置Cookie，不过官方文档说，不保证每次都有效

1.表单中添加{%csrf_token%}这个模板标签

```
<form id="comment_form" action="#"></form>
{% csrf_token %}   就是这个
<p>姓名: <input type="text" name="useranme" id="username"></p>
<p>内容: <textarea name="content" id="content" rows="5" cols="30"></textarea></p>
<p><input type="button", id="send" value="提交"></p> 
```

2.ensure_csrf_cookie装饰器。

```
from django.shortcuts import render
from django.views.decorators.csrf import ensure_csrf_cookie

@ensure_csrf_cookie
def ajax_demo(request):
    return render(request, 'ajax_demo.html')
```

**前端**
 前端要做的事情，在进行post提交时，获取Cookie当中的csrftoken并在请求中添加X-CSRFToken请求头, 该请求头的数据就是csrftoken。通过$.ajaxSetup方法设置AJAX请求的默认参数选项， 在每次ajax的POST请求时，添加X-CSRFToken请求头

```
<script type="text/javascript">
    $(function(){

        function getCookie(name) {
            var cookieValue = null;
            if (document.cookie && document.cookie != '') {
                var cookies = document.cookie.split(';');
                for (var i = 0; i < cookies.length; i++) {
                    var cookie = jQuery.trim(cookies[i]);
                    // Does this cookie string begin with the name we want?
                    if (cookie.substring(0, name.length + 1) == (name + '=')) {
                        cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                        break;
                    }
                }
            }
            return cookieValue;
        }

        <!--获取csrftoken-->
        var csrftoken = getCookie('csrftoken');
        console.log(csrftoken);

        //Ajax call
        function csrfSafeMethod(method) {
            // these HTTP methods do not require CSRF protection
            return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
        }

        $.ajaxSetup({
            crossDomain: false, // obviates need for sameOrigin test
            //请求前触发
            beforeSend: function(xhr, settings) {
                if (!csrfSafeMethod(settings.type)) {
                    xhr.setRequestHeader("X-CSRFToken", csrftoken);
                }
            }
        });

        $('#send').click(function(){
            console.log($("#comment_form").serialize());

            $.ajax({
                type: 'POST',
                url:'{% url 'ajax:post_data' %}',
                data: {
                        username: $('#username').val(),
                        content: $('#content').val(),
                       //'csrfmiddlewaretoken': '{{ csrf_token }}'
                    },
                dataType: 'json',
                success: function(data){
                         
                    
                },
                error: function(){

                }

            });
        });


    });
</script>
```

### 取消csrftoken验证

通过csrf_exempt, 来取消csrftoken验证，方式有两种。
 1 .在视图函数当中添加csrf_exempt装饰器

```
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def post_data(request):
    pass
```

2 .在urlconf当中

```
from django.views.decorators.csrf import csrf_exempt
urlpatterns = [
    url(r'^post/get_data/$', csrf_exempt(post_data), name='post_data'),

]
```

作者：Ljian1992

链接：https://www.jianshu.com/p/a178f08d9389

来源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。