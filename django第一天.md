# django  第一天

> 03 demo版 05年正式版  
>
> 自定义了一套用户管理系统 开发起来非常的快  

[python+django月活7亿](http://python.jobbole.com/87814/)

[官网](<https://www.djangoproject.com/>)

[2.0中文文档](<http://djangobook.py3k.cn/2.0/chapter01/>)

[中文文档](<http://python.usyiyi.cn/translate/django2/index.html>)

| python      | django |
| ----------- | ------ |
| 最大支持3.5 | 1.8    |
| 2.7 3.4 3.5 | 1.9    |
| 3.4 3.5 3.6 | 2.0    |
| 3.5 3.6 3.7 | 2.1    |

* web服务器 

  负责处理http的请求  响应静态文件  常见的 Apache nginx  iis

* 应用服务器 

  负责处理逻辑的服务器  因为 python jaee php 这些代码是不可能通过nginx apache  只能通过应用服务器来处理 常见的应用服务器有 uwsgi tomcat 

* web应用框架 

  封装了常用的web功能   flask、tornado、django 、 java

  的 ssh(structs2+spring3+hibernate3)



## URL组成部分  

```
scheme://host:port/path/?query_string=xxx#anchor

scheme 访问的协议  http 80 https 443 ftp 21  
host:主机名域名或者 ip地址   
port:端口号  默认使用80端口 
path: 路径  也就是平时写的路由  
query_string：要查找的字符串    xxx 就是查询的名称 比如https://cn.bing.com/search?q=python 
anchor : 锚点  后台不用管 一般是前台用来定位的

```



> 注意 url中所有的编码都是 ASCII  如果出现中文  浏览器会先进行编码 然后进行传输  



## 准备工作  虚拟环境

win永久安装pip的源

首先在window的文件夹窗口输入 ：`  %APPDATA%`<img src="https://pic3.zhimg.com/50/v2-91ea9e542434423beeed18fef481ba8b_hd.jpg" data-rawwidth="576" data-rawheight="483" class="origin_image zh-lightbox-thumb" width="576" data-original="https://pic3.zhimg.com/v2-91ea9e542434423beeed18fef481ba8b_r.jpg"/>	

然后在底下新建pip文件夹，然后到pip文件夹里面去新建个pip.ini,然后再里面输入内容:

```
[global]
timeout = 6000
index-url = http://pypi.douban.com/simple
trusted-host = pypi.douban.com
```



截图：<img src="https://pic1.zhimg.com/50/v2-f5dd6f97deab41779991e2c651bc6b98_hd.jpg" data-rawwidth="868" data-rawheight="225" class="origin_image zh-lightbox-thumb" width="868" data-original="https://pic1.zhimg.com/v2-f5dd6f97deab41779991e2c651bc6b98_r.jpg"/>

### 为什么需要虚拟环境：

> 京东  python2 flask2.0   淘宝 python3 flask1.0

到目前位置，我们所有的第三方包安装都是直接通过`pip install xx`的方式进行安装的，这样安装会将那个包安装到你的系统级的`Python`环境中。但是这样有一个问题，就是如果你现在用`Django 1.10.x`写了个网站，然后你的领导跟你说，之前有一个旧项目是用`Django 0.9`开发的，让你来维护，但是`Django 1.10`不再兼容`Django 0.9`的一些语法了。这时候就会碰到一个问题，我如何在我的电脑中同时拥有`Django 1.10`和`Django 0.9`两套环境呢？这时候我们就可以通过虚拟环境来解决这个问题。

### 虚拟环境原理介绍：

虚拟环境相当于一个抽屉，在这个抽屉中安装的任何软件包都不会影响到其他抽屉。并且在项目中，我可以指定这个项目的虚拟环境来配合我的项目。比如我们现在有一个项目是基于`Django 1.10.x`版本，又有一个项目是基于`Django 0.9.x`的版本，那么这时候就可以创建两个虚拟环境，在这两个虚拟环境中分别安装`Django 1.10.x`和`Django 0.9.x`来适配我们的项目。

### 安装`virtualenv`：

`virtualenv`是用来创建虚拟环境的软件工具，我们可以通过`pip`或者`pip3`来安装：

```shell
    pip install virtualenv
    pip3 install virtualenv
```

### 创建虚拟环境：

创建虚拟环境非常简单，通过以下命令就可以创建了：

```shell
    virtualenv [虚拟环境的名字]
```

如果你当前的`Python3/Scripts`的查找路径在`Python2/Scripts`的前面，那么将会使用`python3`作为这个虚拟环境的解释器。如果`python2/Scripts`在`python3/Scripts`前面，那么将会使用`Python2`来作为这个虚拟环境的解释器。

### 进入环境：

虚拟环境创建好了以后，那么可以进入到这个虚拟环境中，然后安装一些第三方包，进入虚拟环境在不同的操作系统中有不同的方式，一般分为两种，第一种是`Windows`，第二种是`*nix`：

1. `windows`进入虚拟环境：进入到虚拟环境的`Scripts`文件夹中，然后执行`activate`。
2. `*nix`进入虚拟环境：`source /path/to/virtualenv/bin/activate`
   一旦你进入到了这个虚拟环境中，你安装包，卸载包都是在这个虚拟环境中，不会影响到外面的环境。

### 退出虚拟环境：

退出虚拟环境很简单，通过一个命令就可以完成：`deactivate`。

### 创建虚拟环境的时候指定`Python`解释器：

在电脑的环境变量中，一般是不会去更改一些环境变量的顺序的。也就是说比如你的`Python2/Scripts`在`Python3/Scripts`的前面，那么你不会经常去更改他们的位置。但是这时候我确实是想在创建虚拟环境的时候用`Python3`这个版本，这时候可以通过`-p`参数来指定具体的`Python`解释器：

```shell
    virtualenv -p C:\Python36\python.exe [virutalenv name]
```

------

> virtualenv 需要进入目录  激活或者退出 这样很麻烦   

### virtualenvwrapper：

`virtualenvwrapper`这个软件包可以让我们管理虚拟环境变得更加简单。不用再跑到某个目录下通过`virtualenv`来创建虚拟环境，并且激活的时候也要跑到具体的目录下去激活。

#### 安装`virtualenvwrapper`：

1. *nix：`pip install virtualenvwrapper`。
2. windows：`pip install virtualenvwrapper-win`。

#### `virtualenvwrapper`基本使用：

1. 创建虚拟环境：

   ```shell
    mkvirtualenv my_env
   ```

   那么会在你当前用户下创建一个`Env`的文件夹，然后将这个虚拟环境安装到这个目录下。
   如果你电脑中安装了`python2`和`python3`，并且两个版本中都安装了`virtualenvwrapper`，那么将会使用环境变量中第一个出现的`Python`版本来作为这个虚拟环境的`Python`解释器。

2. 切换到某个虚拟环境：

   ```shell
    workon my_env
   ```

3. 退出当前虚拟环境：

   ```shell
    deactivate
   ```

4. 删除某个虚拟环境：

   ```shell
    rmvirtualenv my_env
   ```

5. 列出所有虚拟环境：

   ```shell
    lsvirtualenv
   ```

6. 进入到虚拟环境所在的目录：

   ```shell
    首先切换到该虚拟环境
    cdvirtualenv
   ```

#### 修改`mkvirtualenv`的默认路径：

在`我的电脑->右键->属性->高级系统设置->环境变量->系统变量`中添加一个参数`WORKON_HOME`，将这个参数的值设置为你需要的路径。

#### 创建虚拟环境的时候指定`Python`版本：

在使用`mkvirtualenv`的时候，可以指定`--python`的参数来指定具体的`python`路径：

```
    mkvirtualenv --python==C:\Python36\python.exe qf_env
```



## 安装 django  

> pip install django==2.0
>
> pip install pymysql 

## 第一个django 项目

1. 命令行的方式 django-admin  startproject 项目名字   新建一个项目  仅仅是搭好了一个架子 真正起作用的还是app   
2. 在pycharm 进行创建  选定制定好的虚拟环境  

## 创建应用  

1.命令行   

​	

### 启动 

1. python manage.py runserver 默认端口号8000  如果想指定端口号 需要  python manage.py runserver 9000 即可  
2. 如果想让通过127.0.0.1 之外其他ip访问  需要 python manage.py runserver 0.0.0.0:9000
3. pycharm 绿色按钮启动   前提设置 configurations  勾选 single  也可以设置host  和 port 
4. 如果开启0.0.0.0 那么必须在settings.py中 修改ALLOWED_HOSTS  改为 ALLOWED_HOSTS = ['你的ip','127.0.0.1']  然后就可以在浏览器中 http://192.168.58.189:9000/ 访问  

## 项目结构  

1. manage.py   以后和项目打交道 基本上都是基于这个文件  终端下面 python manage.py help 可以查看它能做什么  一般情况下 不要编辑这个文件  
2. settings.py 本项目的设置项 以后所有和项目相关的配置都放到这里  
3. urls.py 这个用来 配置项目的url   比如访问 http://192.168.58.189:9000/news/ news 就在这里配置  写完了 视图函数 要暴漏出去  通过url
4. wsgi.py  部署的时候用到这个 一般不动  项目与wsgi协议兼容的 的web服务器入口  

### 项目和应用    project  和 app  

> 豆瓣  拥有 图书 电影  音乐 同城  这些模块  站在django 的角度来看   电影 图书 音乐 就是豆瓣的app  
>
> django 项目 会有许多app组成    一个app 可以被用到其它的项目 

## URL 分发器  

### url 映射   在浏览器输入什么url才能访问到我们的视图函数 

> django 回到 主的urls.py中 寻找对应的视图  有一个urlpatterns变量 来这个变量中读取匹配规则  需要path()函数包裹   返回一个对象 

```
from django.contrib import admin
from django.urls import path
from django.http import HttpResponse
from book.views import book
from movie.views import movie
def index(request):
    return HttpResponse("豆瓣首页")

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',index), #后面不要加括号
    path('book/',book), #后面不要加括号
    path('movie/',movie), #后面不要加括号
]

```



## 视图 

> 第一个参数永远是request对象 这个对象存储了 请求过来的所有信息 比如参数 和 请求头   返回结果必须是HttpResponse

```
from django.http import HttpResponse
# Create your views here.
def book(request):
    return HttpResponse("图书首页")
```

## url 传参  

> 在settings.py 中已经规定好了 ROOT_URLCONF 所以django 回到 urls.py中 寻找规则  匹配    
>
> 采用在url中使用变量的方式进行传参  <参数名> 在视图函数中也写明参数   视图函数中的  参数名和 url中的参数名必须保持一致   在 url中可以传递多个参数   

```
# Create your views here.
from django.http  import HttpResponse

def book(request):
    return HttpResponse("图书首页")

def book_detail(request,book_id,category_id):
    text = "您获取的图书id是:%s,图书分类是:%s" % (book_id,category_id)
    return HttpResponse(text)

def author(request):
    author_id = request.GET['id'] #GET请求 获取参数的方式  
    text = "作者的ID是:%s" % author_id
    return HttpResponse(text)

def publisher(request,publisher_id):
    text = "出版社的ID是:%s" % publisher_id
    return HttpResponse(text)
    
    
urls.py 


urlpatterns = [
    path('',index),
    path('book/',views.book),
    path('book/detail/<book_id>/<category_id>/',views.book_detail),
    path('book/author/',views.author),
    path('book/publisher/<slug:publisher_id>/',views.publisher),

]

```

### URL参数转化器  

```
path('book/publisher/<slug:publisher_id>/',views.publisher),
```



* str  除了 / 以外的任意字符

*  int 只能是整型数字 

* path 所有的字符都是满足的 

* uuid   只有满足 uuid.uuid4() 返回的字符串格式才可以 

  >>> import uuid
  >>>
  >>> print(uuid.uuid4())
  >>> 6f0b06e2-273e-4844-9e6e-2ff0bd33d2f6

* slug   英文的- 英文字符 阿拉伯数字 下划线 才满足  



### urls 中包含另外一个urls 模块  

> 项目越来越大  所有的url 全部放到 urls 中 会非常庞大 关键是 不好管理  解决办法是 在每个app 中  新建一个urls.py 文件  所有跟app相关的url 全部放到这个里边    然后 主url 包裹 这个app的url   

```
from django.http import HttpResponse

# Create your views here.
def book(request):
    return HttpResponse("图书首页")

def book_detail(request,book_id):
    text = '图书的id是:%s' % book_id
    return HttpResponse(text)

def book_list(request):
    return HttpResponse("图书列表页")
    
    
book/urls.py  

from django.urls import path
from . import views
urlpatterns = [  #也要放到urlpatterns 变量中 
    path('',views.book), #/book
    path('detail/<int:book_id>/',views.book_detail), #主urls.py会跟这个进行拼接  注意不要多加/
    path('list/',views.book_list),
]
```



### include 函数用法 

```
主urls.py 

from django.urls import path,include #先导入include 
from movie import views
urlpatterns = [
   path('book/',include('book.urls')),
   path('movie/',include([
       path('',views.movie),
       path('list/',views.movie_list),
   ]))
]


1. include(arg,namespace=None)
	arg: 子url模块字符串 比如 book.urls 
	namespace：实例命令空间  应用命名空间  先预留  
	
2. include(pattern_list) 列表  
	例子:   
    path('movie/',include([
       path('',views.movie),
       path('list/',views.movie_list),
   ]))
```



### 应用命名空间和实例命名空间  

> app_name 、 namespace

#### url命名

> url是经常变化的 如果我们把url写死   会经常修改代码  可以给url请个名字  以后使用url 可以通过这个名字进行反转得到url   这样的话就不用写死    
>
> 如何给它命名? 
>
> urlpatterns  = [
>
> ​	path('',views.视图函数名，name=“”)
>
> ]	

```
front app   

views.py  

from django.http import HttpResponse
from django.shortcuts import redirect,reverse

def index(request):
    #?username=xxx
    username = request.GET.get('username')
    if username:
        return HttpResponse("前台首页")
    else:
        login_url = reverse('front:login')
        print('=' * 50)
        print(login_url)
        print('=' * 50)
        return redirect(login_url)

def login(request):
    return HttpResponse("前台登录页面")
--------------
urls.py 

    from django.urls import path
    from . import views
    app_name = 'front' #  需要先定义 app_name 变量
    urlpatterns = [
    path('',views.index,name='index'), #给url 起名 前提先给应用命名
    path('signin/',views.login,name='login'),
    

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
cms  app  

views.py   都有 index 和 login  直接redirect(‘/login/’) 容易出现一个问题 前台后台如果没有登录都跳转到前台登录也  我们的要求是 后台没登录 跳转到后台登陆页

from django.http import HttpResponse
from django.shortcuts import redirect,reverse

def index(request):
    #?username=xxx
    username = request.GET.get('username')
    if username:
        return HttpResponse("CMS后台首页")
    else:
        login_url =  reverse('cms:login')
        print('=' * 50)
        print(login_url)
        print('=' * 50)
        return redirect(login_url)

def login(request):
    return HttpResponse("CMS登录页面")


urls.py  

from django.urls import path
from . import views
app_name = 'cms'
urlpatterns = [
    path('',views.index,name="index"),
    path('login/',views.login,name="login"),
]
```

### 实例命名空间  

```
有个需求: 
1.同一个后台 我们设置两个路由地址   比如: 127.0.0.1:9000/cms1/login 127.0.0.1:9000/cms2/login
进入的是同一套管理系统  

这就叫做 同一个app下 两个不同的实例  

问题: 
127.0.0.1:9000/cms1 如果没有登录  应该跳转到 127.0.0.1:9000/cms1/login
127.0.0.1:9000/cms2 如果没有登录  应该跳转到 127.0.0.1:9000/cms2/login

应用命名空间写法  

login_url =  reverse('cms1:login')
print('=' * 50)
print(login_url)
print('=' * 50)
return redirect(login_url)   这样都跳转到  127.0.0.1:9000/cms1/login  


解决以上的问题 : 只要创建实例命名空间   namespace  

主urls: 
urlpatterns = [
    path('',include('front.urls')),
    #同一个app下面有两个实例
    path('cms1/',include('cms.urls',namespace='cms1')),
    path('cms2/',include('cms.urls',namespace='cms2')),
]

cms views.py  

 		current_app = request.resolver_match.namespace #获取当前的实例 从cms1 过来 就跳到cms1/login 
        return redirect(reverse("%s:login"% current_app))
```



### re_path  

> re_path 和 path的作用是一样的 不同的是re_path功能更加强大可以使用正则表达式 

* 推荐是用 r'' 原生字符串 
* 定义变量 需要使用() 包裹起来  
* ?P<参数名 >紧跟正则表达式规则 

```
app的 views.py  

from django.http import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse("文章首页")

def article_list_year(request,year):
    text = '您输入的年份是:%s' % year
    return  HttpResponse(text)

def article_list_month(request,month):
    text = '您输入的月份是:%s' % month
    return  HttpResponse(text)
    
app的   urls.py 

from django.urls import re_path
from . import views

urlpatterns = [
    #r代表的是原生字符串
    re_path(r'^$',views.index),
    re_path(r'^list/(?P<year>\d{4})/$',views.article_list_year),
    re_path(r'^list/(?P<month>\d{2})/$',views.article_list_month),
]
```

ps: 如果不是特别的要求 建议path就够了  特殊需求可以使用  避免把正则表达式规则忘记  



### reverse 

> 反转url的时候 需要添加参数  可以在reverse方法中 通过 kwargs={“”：“”}
>
> 想要传递 查询字符串 或者  确定登录之后的跳转目标  next=  

#### 传参

```
views.py 

from django.http import HttpResponse
from django.shortcuts import reverse,redirect

def index(request):
    username = request.GET.get('username')
    if username:
        return HttpResponse("首页")
    else:
        # login_url = reverse('login')
        # return redirect(login_url)
        detail_url = reverse('detail',kwargs={"article_id":666,"page":888})
        return redirect(detail_url)


def login(request):
    return HttpResponse("登录首页")

def article_detail(request,article_id,page):
    text = "您的文章id是:%s" % article_id
    return HttpResponse(text)
    
    
urls.py 这里是主urls  如果用include包含 需要 app_name 变量  

urlpatterns = [
    path('',views.index,name='index'),
    path('login/',views.login,name='login'),
    path('detail/<article_id>/<page>',views.article_detail,name='detail'),
]
```





