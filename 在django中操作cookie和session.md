## 在django中操作cookie和session：

### 操作cookie：

#### 设置cookie：

设置`cookie`是设置值给浏览器的。因此我们需要通过`response`的对象来设置，设置`cookie`可以通过`response.set_cookie`来设置，这个方法的相关参数如下： 

1. `key`：这个`cookie`的`key`。 
2. `value`：这个`cookie`的`value`。 
3. `max_age`：最长的生命周期。单位是秒。 
4. `expires`：过期时间。跟`max_age`是类似的，只不过这个参数需要传递一个具体的日期，比如`datetime`或者是符合日期格式的字符串。如果同时设置了`expires`和`max_age`，那么将会使用`expires`的值作为过期时间。 
5. `path`：对域名下哪个路径有效。默认是对域名下所有路径都有效。 
6. `domain`：针对哪个域名有效。默认是针对主域名下都有效，如果只要针对某个子域名才有效，那么可以设置这个属性. 
7. `secure`：是否是安全的，如果设置为`True`，那么只能在`https`协议下才可用。 
8. `httponly`：默认是`False`。如果为`True`，那么在客户端不能通过`JavaScript`进行操作。

#### 删除cookie：

通过`delete_cookie`即可删除`cookie`。实际上删除`cookie`就是将指定的`cookie`的值设置为空的字符串，然后使用将他的过期时间设置为`0`，也就是浏览器关闭后就过期。

#### 获取cookie：

获取浏览器发送过来的`cookie`信息。可以通过`request.COOKIES`来或者。这个对象是一个字典类型。比如获取所有的`cookie`，那么示例代码如下：

```python
cookies = request.COOKIES
for cookie_key,cookie_value in cookies.items():
   print(cookie_key,cookie_value)
```

------

### 操作session：

`django`中的`session`默认情况下是存储在服务器的数据库中的，在表中会根据`sessionid`来提取指定的`session`数据，然后再把这个`sessionid`放到`cookie`中发送给浏览器存储，浏览器下次在向服务器发送请求的时候会自动的把所有`cookie`信息都发送给服务器，服务器再从`cookie`中获取`sessionid`，然后再从数据库中获取`session`数据。但是我们在操作`session`的时候，这些细节压根就不用管。我们只需要通过`request.session`即可操作。示例代码如下：

```python
def index(request):
   request.session.get('username')
   return HttpResponse('index')
```

`session`常用的方法如下：

1. `get`：用来从`session`中获取指定值。
2. `pop`：从`session`中删除一个值。
3. `keys`：从`session`中获取所有的键。
4. `items`：从`session`中获取所有的值。
5. `clear`：清除当前这个用户的`session`数据。
6. `flush`：删除`session`并且删除在浏览器中存储的`session_id`，一般在注销的时候用得比较多。
7. `set_expiry(value)`：设置过期时间。
   - 整形：代表秒数，表示多少秒后过期。
   - `0`：代表只要浏览器关闭，`session`就会过期。
   - `None`：会使用全局的`session`配置。在`settings.py`中可以设置`SESSION_COOKIE_AGE`来配置全局的过期时间。默认是`1209600`秒，也就是2周的时间。
8. `clear_expired`：清除过期的`session`。`Django`并不会清除过期的`session`，需要定期手动的清理，或者是在终端，使用命令行`python manage.py clearsessions`来清除过期的`session`。

### 修改session的存储机制：

默认情况下，`session`数据是存储到数据库中的。当然也可以将`session`数据存储到其他地方。可以通过设置`SESSION_ENGINE`来更改`session`的存储位置，这个可以配置为以下几种方案： 

1. `django.contrib.sessions.backends.db`：使用数据库。默认就是这种方案。 
2. `django.contrib.sessions.backends.file`：使用文件来存储session。 
3. `django.contrib.sessions.backends.cache`：使用缓存来存储session。想要将数据存储到缓存中，前提是你必须要在`settings.py`中配置好`CACHES`，并且是需要使用`Memcached`，而不能使用纯内存作为缓存。 
4. `django.contrib.sessions.backends.cached_db`：在存储数据的时候，会将数据先存到缓存中，再存到数据库中。这样就可以保证万一缓存系统出现问题，session数据也不会丢失。在获取数据的时候，会先从缓存中获取，如果缓存中没有，那么就会从数据库中获取。 
5. `django.contrib.sessions.backends.signed_cookies`：将`session`信息加密后存储到浏览器的`cookie`中。这种方式要注意安全，建议设置`SESSION_COOKIE_HTTPONLY=True`，那么在浏览器中不能通过`js`来操作`session`数据，并且还需要对`settings.py`中的`SECRET_KEY`进行保密，因为一旦别人知道这个`SECRET_KEY`，那么就可以进行解密。另外还有就是在`cookie`中，存储的数据不能超过`4k`。