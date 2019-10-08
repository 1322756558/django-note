## 什么是csrf





这里通俗的讲一下，具体解释请看django官网。csrf是Django提供的防止伪装提交请求的中间件，说白了就是防止别人利用Cookie跨域访问，CSRF全称`Cross-site request forgery`（跨站请求伪造），在setting的MIDDLEWARE中`django.middleware.csrf.CsrfViewMiddleware`设置打开或关闭，在template的form表单中加入{`% csrf_token %`}，django会渲染解释成`<input type="hidden", name='csrfmiddlewaretoken' value=服务器随机生成的token>`，随表单提交上去，django在处理post请求前判断该csrfmiddlewaretoken跟cookie中的csrftoken是否一致，不一致的请求被识别成csrf攻击，返回403。

而现在采用vue+django的前后端完全分离的话，就不能使用{`% csrf_token %`}了，需要自己构建验证，大致流程分为，后端生成csrftoken，前端获取cookie中的csrftoken并随表单提交。

## django处理





django后端需要先在cookie中生成csrftoken，通过下面的方法引入csrf的get_token

```
from django.middleware.csrf import get_token
```

该方法就是生成cookie中的csrftoken，然后在前端提交表单前传递到前端（可以不传递，让前端去cookie中获取）

```
crsftoken = get_token(request)
return JsonResponse({"crsftoken":crsftoken})
```

这一步如果操作不对，会遇到Forbidden (CSRF cookie not set.)错误。

## vue处理





**由于axios默认是不发送cookie的**，所以我们需要在引入axios后进行如下设置：

```
axios.defaults.withCredentials = true
```

然后前端获取csrftoken并随表单提交(也可以去cookie中获取)

```
this.crsftoken = response.data.crsftoken
headers: {
  'Content-Type': 'application/json',
  "X-CSRFToken": this.crsftoken
}
this.axios.post(url,formdata,config)
```

### vue直接去cookie中获取crsftoken

```
let formdata = new FormData()
formdata.append('name',this.name)
let regex = /.*csrftoken=([^;.]*).*$/
let config = {
    headers: {
      'Content-Type': 'application/json',
      "X-CSRFToken": document.cookie.match(regex) === null ? null : document.cookie.match(regex)[1]
    }
}
this.axios.post(url,formdata,config)
```

这一步如果操作不对，会遇到Forbidden (CSRF token missing or incorrect.)错误。

来源----[http://blog.poison.cc/2018/12/03/vue-django-csrf/#django%E5%A4%84%E7%90%86](http://blog.poison.cc/2018/12/03/vue-django-csrf/#django处理)

