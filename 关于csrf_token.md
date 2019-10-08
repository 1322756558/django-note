## 一、基本使用

默认情况下，使用`django-admin startproject xxx`命令创建工程时，CSRF防御机制就已经开启了。如果没有开启，请在MIDDLEWARE设置中添加'django.middleware.csrf.CsrfViewMiddleware'。

**对于GET请求，一般来说没有这个问题，CSRF通常是针对POST方法的！**

在含有POST表单的模板中，需要在其`<form>`表单元素内部添加`csrf_token`标签，如下所示：

```
<form action="" method="post">
    {% csrf_token %}
    ....
</form>
```

这样，当表单数据通过POST方法，发送到后台服务器的时候，除了正常的表单数据外，还会携带一个CSRF令牌随机字符串，用于进行csrf验证。其实没有多么麻烦和复杂，对么？如果表单中没有携带这个csrf令牌，你将会获得一枚403奖章。

额外提示：对于初学者，要明白一件事情，就是我们上面讲的都是Django项目自己内部的事务，不涉及与外界的关系。例如，你不能把上面那个表单发往百度，百度会懵逼的，你这发的啥？其次，那样也不安全，可能引起CSRF信息泄露而导致自己的站点出现漏洞。

## 二、 AJAX

我们知道，在前端的世界，有一种叫做AJAX的东西，也就是“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），经常被用来在不刷新页面的情况下，提交和请求数据。如果我们的Django服务器接收的是一个通过AJAX发送过来的POST请求的话，那么将很麻烦。

为什么？因为AJAX中，没有办法像form表单中那样携带`{% csrf_token %}`令牌。

那怎么办呢？

好办！在你的前端模版的JavaScript代码处，添加下面的代码：

```
// using jQuery
function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
var csrftoken = getCookie('csrftoken');

function csrfSafeMethod(method) {
    // 这些HTTP方法不要求CSRF包含
    return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
}
$.ajaxSetup({
    beforeSend: function(xhr, settings) {
        if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
            xhr.setRequestHeader("X-CSRFToken", csrftoken);
        }
    }
});
```

上面代码的作用就是让你的ajax的POST方法带上CSRF需要的令牌，它依赖Jquery库，必须提前加载Jquery。这是Django官方提供的解决方案哦，^-^。

## 三、装饰器

### 1. 单独指定csrf验证需要

有时候，我们在全站上关闭了CSRF功能，但是希望某些视图还有CSRF防御，那怎么办呢？

Django为我们提供了一个`csrf_protect(view)`装饰器，使用起来非常方便，如下所示：

```
from django.views.decorators.csrf import csrf_protect
from django.shortcuts import render

@csrf_protect
def my_view(request):
    c = {}
    # ...
    return render(request, "a_template.html", c)
```

现在，虽然全站关掉了csrf，但是my_view视图依然需要进行csrf验证。

### 2. 单独指定忽略csrf验证

有正就有反。在全站开启CSRF机制的时候，有些视图我们并不想开启这个功能。比如，有另外一台机器通过requests库，模拟HTTP通信，以POST请求向我们的Django主机服务器发送过来了一段保密数据。它无法携带CSRF令牌，必然会被403。

这怎么办呢？

在接收这个POST请求的视图上为CSRF开道口子，不进行验证。这就需要使用Django为我们提供的`csrf_exempt(view)`装饰器了，下面是使用范例：

```
from django.views.decorators.csrf import csrf_exempt
from django.http import HttpResponse

@csrf_exempt
def my_view(request):
    return HttpResponse('Hello world')
```

这下POST数据是没问题了，但是又带来了新的安全问题，需要你自己处理。

### 3. 确保csrf令牌被设置

Django还提供了一个装饰器，确保被装饰的视图在返回页面时同时将csrf令牌一起返回。

这个装饰器是：ensure_csrf_cookie(view)，其使用方法和上面的一样：

```
from django.views.decorators.csrf import ensure_csrf_cookie
from django.http import HttpResponse

@ensure_csrf_cookie
def my_view(request):
    return HttpResponse('Hello world')
```

### 4. requires_csrf_token(view)

这个装饰器类似csrf_protect，一样要进行csrf验证，但是它不会拒绝发送过来的请求。

```
from django.views.decorators.csrf import requires_csrf_token
from django.shortcuts import render

@requires_csrf_token
def my_view(request):
    c = {}
    # ...
    return render(request, "a_template.html", c)
```

转自-----<http://www.liujiangblog.com/course/django/179>