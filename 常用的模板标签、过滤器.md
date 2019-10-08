# 常用的模板标签：

1. `if`标签：`if`标签相当于`Python`中的`if`语句，有`elif`和`else`相对应，但是所有的标签都需要用标签符号（`{%%}`）进行包裹。`if`标签中可以使用`==、!=、<、<=、>、>=、in、not in、is、is not`等判断运算符。示例代码如下：

   ```python
    {% if "张三" in persons %}
        <p>张三</p>
    {% else %}
        <p>李四</p>
    {% endif %}
   ```

2. `for...in...`标签：`for...in...`类似于`Python`中的`for...in...`。可以遍历列表、元组、字符串、字典等一切可以遍历的对象。示例代码如下：

   ```python
    {% for person in persons %}
        <p>{{ person.name }}</p>
    {% endfor %}
   ```

   如果想要反向遍历，那么在遍历的时候就加上一个`reversed`。示例代码如下：

   ```python
    {% for person in persons reversed %}
        <p>{{ person.name }}</p>
    {% endfor %}
   ```

   遍历字典的时候，需要使用`items`、`keys`和`values`等方法。在`DTL`中，执行一个方法不能使用圆括号的形式。遍历字典示例代码如下：

   ```python
    {% for key,value in person.items %}
        <p>key：{{ key }}</p>
        <p>value：{{ value }}</p>
    {% endfor %}
   ```

   在`for`循环中，`DTL`提供了一些变量可供使用。这些变量如下：

   - `forloop.counter`：当前循环的下标。以1作为起始值。
   - `forloop.counter0`：当前循环的下标。以0作为起始值。
   - `forloop.revcounter`：当前循环的反向下标值。比如列表有5个元素，那么第一次遍历这个属性是等于5，第二次是4，以此类推。并且是以1作为最后一个元素的下标。
   - `forloop.revcounter0`：类似于forloop.revcounter。不同的是最后一个元素的下标是从0开始。
   - `forloop.first`：是否是第一次遍历。
   - `forloop.last`：是否是最后一次遍历。
   - `forloop.parentloop`：如果有多个循环嵌套，那么这个属性代表的是上一级的for循环。

3. `for...in...empty`标签：这个标签使用跟`for...in...`是一样的，只不过是在遍历的对象如果没有元素的情况下，会执行`empty`中的内容。示例代码如下：

   ```python
    {% for person in persons %}
        <li>{{ person }}</li>
    {% empty %}
        暂时还没有任何人
    {% endfor %}
   ```

4. `with`标签：在模版中定义变量。有时候一个变量访问的时候比较复杂，那么可以先把这个复杂的变量缓存到一个变量上，以后就可以直接使用这个变量就可以了。示例代码如下：

   ```python
    context = {
        "persons": ["张三","李四"]
    }
   
    {% with lisi=persons.1 %}
        <p>{{ lisi }}</p>
    {% endwith %}
   ```

   有几点需要强烈的注意：

   - 在`with`语句中定义的变量，只能在`{%with%}{%endwith%}`中使用，不能在这个标签外面使用。

   - 定义变量的时候，不能在等号左右两边留有空格。比如`{% with lisi = persons.1%}`是错误的。

   - 还有另外一种写法同样也是支持的：

     ```python
       {% with persons.1 as lisi %}
           <p>{{ lisi }}</p>
       {% endwith %}
     ```

5. `url`标签：在模版中，我们经常要写一些`url`，比如某个`a`标签中需要定义`href`属性。当然如果通过硬编码的方式直接将这个`url`写死在里面也是可以的。但是这样对于以后项目维护可能不是一件好事。因此建议使用这种反转的方式来实现，类似于`django`中的`reverse`一样。示例代码如下：

   ```python
    <a href="{% url 'book:list' %}">图书列表页面</a>
   ```

   如果`url`反转的时候需要传递参数，那么可以在后面传递。但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用。示例代码如下：

   ```python
        # path部分
        path('detail/<book_id>/',views.book_detail,name='detail')
   
        # url反转，使用位置参数
        <a href="{% url 'book:detail' 1 %}">图书详情页面</a>
   
        # url反转，使用关键字参数
        <a href="{% url 'book:detail' book_id=1 %}">图书详情页面</a>
   ```

   如果想要在使用`url`标签反转的时候要传递查询字符串的参数，那么必须要手动在在后面添加。示例代码如下：

   ```python
        <a href="{% url 'book:detail' book_id=1 %}?page=1">图书详情页面</a>
   ```

   如果需要传递多个参数，那么通过空格的方式进行分隔。示例代码如下：

   ```python
        <a href="{% url 'book:detail' book_id=1 page=2 %}">图书详情页面</a>
   ```

6. `spaceless`标签：移除html标签中的空白字符。包括空格、tab键、换行等。示例代码如下：

   ```python
    {% spaceless %}
        <p>
            <a href="foo/">Foo</a>
        </p>
    {% endspaceless %}
   ```

   那么在渲染完成后，会变成以下的代码：

   ```html
    <p><a href="foo/">Foo</a></p>
   ```

   `spaceless`只会移除html标签之间的空白字符。而不会移除标签与文本之间的空白字符。看以下代码：

   ```python
    {% spaceless %}
        <strong>
            Hello
        </strong>
    {% endspaceless %}
   ```

   这个将不会移除`strong`中的空白字符。

7. `autoescape`标签：开启和关闭这个标签内元素的自动转义功能。自动转义是可以将一些特殊的字符。比如`<`转义成`html`语法能识别的字符，比如`<`会被转义成`&lt;`，而`>`会被自动转义成`&gt;`。模板中默认是已经开启了自动转义的。`autoescape`的示例代码如下：

   ```python
    # 传递的上下文信息
    context = {
        "info":"<a href='www.baidu.com'>百度</a>"
    }
   
    # 模板中关闭自动转义
    {% autoescape on %}
        {{ info }}
    {% endautoescape %}
   ```

   那么就会显示百度的一个超链接。如果把`on`成`off`，那么就会显示成一个普通的字符串。示例代码如下：

   ```python
    {% autoescape on %}
        {{ info }}
    {% endautoescape %}
   ```

8. `verbatim`标签：默认在`DTL`模板中是会去解析那些特殊字符的。比如`{%`和`%}`以及`{{`等。如果你在某个代码片段中不想使用`DTL`的解析引擎。那么你可以把这个代码片段放在`verbatim`标签中。示例代码下：

   ```python
    {% verbatim %}
        {{if dying}}Still alive.{{/if}}
    {% endverbatim %}
   ```

9. 更多标签请参考官方文档：`https://docs.djangoproject.com/en/2.0/ref/templates/builtins/`



# 模版常用过滤器

在模版中，有时候需要对一些数据进行处理以后才能使用。一般在`Python`中我们是通过函数的形式来完成的。而在模版中，则是通过过滤器来实现的。过滤器使用的是`|`来使用。比如使用`add`过滤器，那么示例代码如下：

```python
    {{ value|add:"2" }}
```

那么以下就讲下在开发中常用的过滤器。

### add

将传进来的参数添加到原来的值上面。这个过滤器会尝试将`值`和`参数`转换成整形然后进行相加。如果转换成整形过程中失败了，那么会将`值`和`参数`进行拼接。如果是字符串，那么会拼接成字符串，如果是列表，那么会拼接成一个列表。示例代码如下：

```python
{{ value|add:"2" }}
```

如果`value`是等于4，那么结果将是6。如果`value`是等于一个普通的字符串，比如`abc`，那么结果将是`abc2`。`add`过滤器的源代码如下：

```python
def add(value, arg):
    """Add the arg to the value."""
    try:
        return int(value) + int(arg)
    except (ValueError, TypeError):
        try:
            return value + arg
        except Exception:
            return ''
```

### cut

移除值中所有指定的字符串。类似于`python`中的`replace(args,"")`。示例代码如下：

```python
{{ value|cut:" " }}
```

以上示例将会移除`value`中所有的空格字符。`cut`过滤器的源代码如下：

```python
def cut(value, arg):
    """Remove all values of arg from the given string."""
    safe = isinstance(value, SafeData)
    value = value.replace(arg, '')
    if safe and arg != ';':
        return mark_safe(value)
    return value
```

### date

将一个日期按照指定的格式，格式化成字符串。示例代码如下：

```python
# 数据
context = {
    "birthday": datetime.now()
}

# 模版
{{ birthday|date:"Y/m/d" }}
```

那么将会输出`2018/02/01`。其中`Y`代表的是四位数字的年份，`m`代表的是两位数字的月份，`d`代表的是两位数字的日。
还有更多时间格式化的方式。见下表。

| 格式字符 | 描述                                 | 示例  |
| -------- | ------------------------------------ | ----- |
| Y        | 四位数字的年份                       | 2018  |
| m        | 两位数字的月份                       | 01-12 |
| n        | 月份，1-9前面没有0前缀               | 1-12  |
| d        | 两位数字的天                         | 01-31 |
| j        | 天，但是1-9前面没有0前缀             | 1-31  |
| g        | 小时，12小时格式的，1-9前面没有0前缀 | 1-12  |
| h        | 小时，12小时格式的，1-9前面有0前缀   | 01-12 |
| G        | 小时，24小时格式的，1-9前面没有0前缀 | 1-23  |
| H        | 小时，24小时格式的，1-9前面有0前缀   | 01-23 |
| i        | 分钟，1-9前面有0前缀                 | 00-59 |
| s        | 秒，1-9前面有0前缀                   | 00-59 |

### default

如果值被评估为`False`。比如`[]`，`""`，`None`，`{}`等这些在`if`判断中为`False`的值，都会使用`default`过滤器提供的默认值。示例代码如下：

```python
{{ value|default:"nothing" }}
```

如果`value`是等于一个空的字符串。比如`""`，那么以上代码将会输出`nothing`。

### default_if_none

如果值是`None`，那么将会使用`default_if_none`提供的默认值。这个和`default`有区别，`default`是所有被评估为`False`的都会使用默认值。而`default_if_none`则只有这个值是等于`None`的时候才会使用默认值。示例代码如下：

```python
{{ value|default_if_none:"nothing" }}
```

如果`value`是等于`""`也即空字符串，那么以上会输出空字符串。如果`value`是一个`None`值，以上代码才会输出`nothing`。

### first

返回列表/元组/字符串中的第一个元素。示例代码如下：

```python
{{ value|first }}
```

如果`value`是等于`['a','b','c']`，那么输出将会是`a`。

### last

返回列表/元组/字符串中的最后一个元素。示例代码如下：

```python
{{ value|last }}
```

如果`value`是等于`['a','b','c']`，那么输出将会是`c`。

### floatformat

使用四舍五入的方式格式化一个浮点类型。如果这个过滤器没有传递任何参数。那么只会在小数点后保留一个小数，如果小数后面全是0，那么只会保留整数。当然也可以传递一个参数，标识具体要保留几个小数。

1. 如果没有传递参数：

   | value | 模版代码 | 输出 | | --- | --- | --- | | 34.23234 | `{{ value\|floatformat }}` | 34.2 | | 34.000 | `{{ value\|floatformat }}` | 34 | | 34.260 | `{{ value\|floatformat }}` | 34.3 |

2. 如果传递参数：

   | value | 模版代码 | 输出 | | --- | --- | --- | | 34.23234 | `{{value\|floatformat:3}}` | 34.232 | | 34.0000 | `{{value\|floatformat:3}}` | 34.000 | | 34.26000 | `{{value\|floatformat:3}}` | 34.260 |

### join

类似与`Python`中的`join`，将列表/元组/字符串用指定的字符进行拼接。示例代码如下：

```python
{{ value|join:"/" }}
```

如果`value`是等于`['a','b','c']`，那么以上代码将输出`a/b/c`。

### length

获取一个列表/元组/字符串/字典的长度。示例代码如下：

```python
{{ value|length }}
```

如果`value`是等于`['a','b','c']`，那么以上代码将输出`3`。如果`value`为`None`，那么以上将返回`0`。

### lower

将值中所有的字符全部转换成小写。示例代码如下：

```python
{{ value|lower }}
```

如果`value`是等于`Hello World`。那么以上代码将输出`hello world`。

### upper

类似于`lower`，只不过是将指定的字符串全部转换成大写。

### random

在被给的列表/字符串/元组中随机的选择一个值。示例代码如下：

```python
{{ value|random }}
```

如果`value`是等于`['a','b','c']`，那么以上代码会在列表中随机选择一个。

### safe

标记一个字符串是安全的。也即会关掉这个字符串的自动转义。示例代码如下：

```python
{{value|safe}}
```

如果`value`是一个不包含任何特殊字符的字符串，比如`<a>`这种，那么以上代码就会把字符串正常的输入。如果`value`是一串`html`代码，那么以上代码将会把这个`html`代码渲染到浏览器中。

### slice

类似于`Python`中的切片操作。示例代码如下：

```python
{{ some_list|slice:"2:" }}
```

以上代码将会给`some_list`从`2`开始做切片操作。

### stringtags

删除字符串中所有的`html`标签。示例代码如下：

```python
{{ value|striptags }}
```

如果`value`是`<strong>hello world</strong>`，那么以上代码将会输出`hello world`。

### truncatechars

如果给定的字符串长度超过了过滤器指定的长度。那么就会进行切割，并且会拼接三个点来作为省略号。示例代码如下：

```python
{{ value|truncatechars:5 }}
```

如果`value`是等于`北京欢迎您~`，那么输出的结果是`北京...`。可能你会想，为什么不会`北京欢迎您...`呢。因为三个点也占了三个字符，所以`北京`+三个点的字符长度就是5。

### truncatechars_html

类似于`truncatechars`，只不过是不会切割`html`标签。示例代码如下：

```python
{{ value|truncatechars:5 }}
```

如果`value`是等于`<p>北京欢迎您~</p>`，那么输出将是`<p>北京...</p>`。