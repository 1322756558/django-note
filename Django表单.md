## HTML中的表单：

单纯从前端的`html`来说，表单是用来提交数据给服务器的,不管后台的服务器用的是`Django`还是`PHP`语言还是其他语言。只要把`input`标签放在`form`标签中，然后再添加一个提交按钮，那么以后点击提交按钮，就可以将`input`标签中对应的值提交给服务器了。

## Django中的表单：

`Django`中的表单丰富了传统的`HTML`语言中的表单。在`Django`中的表单，主要做以下两件事：

1. 渲染表单模板。
2. 表单验证数据是否合法。

## Django中表单使用流程：

在讲解`Django`表单的具体每部分的细节之前。我们首先先来看下整体的使用流程。这里以一个做一个留言板为例。首先我们在后台服务器定义一个表单类，继承自`django.forms.Form`。示例代码如下：

```python
# forms.py
class MessageBoardForm(forms.Form):
    title = forms.CharField(max_length=3,label='标题',min_length=2,error_messages={"min_length":'标题字符段不符合要求！'})
    content = forms.CharField(widget=forms.Textarea,label='内容')
    email = forms.EmailField(label='邮箱')
    reply = forms.BooleanField(required=False,label='回复')
```

然后在视图中，根据是`GET`还是`POST`请求来做相应的操作。如果是`GET`请求，那么返回一个空的表单，如果是`POST`请求，那么将提交上来的数据进行校验。示例代码如下：

```python
# views.py
class IndexView(View):
    def get(self,request):
        form = MessageBoardForm()
        return render(request,'index.html',{'form':form})

    def post(self,request):
        form = MessageBoardForm(request.POST)
        #输入完成
        if form.is_valid():
            title = form.cleaned_data.get('title')
            content = form.cleaned_data.get('content')
            email = form.cleaned_data.get('email')
            reply = form.cleaned_data.get('reply')
            return HttpResponse('success')
        else:
            print(form.errors)
            return HttpResponse('fail')
```

在使用`GET`请求的时候，我们传了一个`form`给模板，那么以后模板就可以使用`form`来生成一个表单的`html`代码。在使用`POST`请求的时候，我们根据前端上传上来的数据，构建一个新的表单，这个表单是用来验证数据是否合法的，如果数据都验证通过了，那么我们可以通过`cleaned_data`来获取相应的数据。在模板中渲染表单的`HTML`代码如下：

```html
<form action="" method="post">
    <table>

        <tr>
            <td></td>
            <td><input type="submit" value="提交"></td>
        </tr>
    </table>
</form>
```

我们在最外面给了一个`form`标签，然后在里面使用了`table`标签来进行美化，在使用`form`对象渲染的时候，使用的是`table`的方式，当然还可以使用`ul`的方式（`as_ul`），也可以使用`p`标签的方式（`as_p`），并且在后面我们还加上了一个提交按钮。这样就可以生成一个表单了



# 用表单验证数据

## 常用的Field：

使用`Field`可以是对数据验证的第一步。你期望这个提交上来的数据是什么类型，那么就使用什么类型的`Field`。

### CharField：

用来接收文本。
参数：

- max_length：这个字段值的最大长度。
- min_length：这个字段值的最小长度。
- required：这个字段是否是必须的。默认是必须的。
- error_messages：在某个条件验证失败的时候，给出错误信息。

### EmailField：

用来接收邮件，会自动验证邮件是否合法。
错误信息的key：`required`、`invalid`。

### FloatField：

用来接收浮点类型，并且如果验证通过后，会将这个字段的值转换为浮点类型。
参数：

- max_value：最大的值。
- min_value：最小的值。

错误信息的key：`required`、`invalid`、`max_value`、`min_value`。

### IntegerField：

用来接收整形，并且验证通过后，会将这个字段的值转换为整形。
参数：

- max_value：最大的值。
- min_value：最小的值。

错误信息的key：`required`、`invalid`、`max_value`、`min_value`。

### URLField：

用来接收`url`格式的字符串。
错误信息的key：`required`、`invalid`。

------

## 常用验证器：

在验证某个字段的时候，可以传递一个`validators`参数用来指定验证器，进一步对数据进行过滤。验证器有很多，但是很多验证器我们其实已经通过这个`Field`或者一些参数就可以指定了。比如`EmailValidator`，我们可以通过`EmailField`来指定，比如`MaxValueValidator`，我们可以通过`max_value`参数来指定。以下是一些常用的验证器：

1. `MaxValueValidator`：验证最大值。

2. `MinValueValidator`：验证最小值。

3. `MinLengthValidator`：验证最小长度。

4. `MaxLengthValidator`：验证最大长度。

5. `EmailValidator`：验证是否是邮箱格式。

6. `URLValidator`：验证是否是`URL`格式。

7. ```
   RegexValidator
   ```

   ：如果还需要更加复杂的验证，那么我们可以通过正则表达式的验证器：

   ```
   RegexValidator
   ```

   。比如现在要验证手机号码是否合格，那么我们可以通过以下代码实现：

   ```python
    class MyForm(forms.Form):
        telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
   ```

## 自定义验证：

有时候对一个字段验证，不是一个长度，一个正则表达式能够写清楚的，还需要一些其他复杂的逻辑，那么我们可以对某个字段，进行自定义的验证。比如在注册的表单验证中，我们想要验证手机号码是否已经被注册过了，那么这时候就需要在数据库中进行判断才知道。对某个字段进行自定义的验证方式是，定义一个方法，这个方法的名字定义规则是：`clean_fieldname`。如果验证失败，那么就抛出一个验证错误。比如要验证用户表中手机号码之前是否在数据库中存在，那么可以通过以下代码实现：

```python
class MyForm(forms.Form):
    telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])

    def clean_telephone(self):
        telephone = self.cleaned_data.get('telephone')
        exists = User.objects.filter(telephone=telephone).exists()
        if exists:
            raise forms.ValidationError("手机号码已经存在！")
        return telephone
```

以上是对某个字段进行验证，如果验证数据的时候，需要针对多个字段进行验证，那么可以重写`clean`方法。比如要在注册的时候，要判断提交的两个密码是否相等。那么可以使用以下代码来完成：

```python
class MyForm(forms.Form):
    telephone = forms.CharField(validators=[validators.RegexValidator("1[345678]\d{9}",message='请输入正确格式的手机号码！')])
    pwd1 = forms.CharField(max_length=12)
    pwd2 = forms.CharField(max_length=12)

    def clean(self):
        cleaned_data = super().clean()
        pwd1 = cleaned_data.get('pwd1')
        pwd2 = cleaned_data.get('pwd2')
        if pwd1 != pwd2:
            raise forms.ValidationError('两个密码不一致！')
```

## 提取错误信息：

如果验证失败了，那么有一些错误信息是我们需要传给前端的。这时候我们可以通过以下属性来获取： 

1. `form.errors`：这个属性获取的错误信息是一个包含了`html`标签的错误信息。 
2. `form.errors.get_json_data()`：这个方法获取到的是一个字典类型的错误信息。将某个字段的名字作为`key`，错误信息作为值的一个字典。 
3. `form.as_json()`：这个方法是将`form.get_json_data()`返回的字典`dump`成`json`格式的字符串，方便进行传输。 
4. 上述方法获取的字段的错误值，都是一个比较复杂的数据。比如以下：

```python
{'username': [{'message': 'Enter a valid URL.', 'code': 'invalid'}, {'message': 'Ensure this value has at most 4 characters (it has 22).', 'code': 'max_length'}]}
```

那么如果我只想把错误信息放在一个列表中，而不要再放在一个字典中。这时候我们可以定义一个方法，把这个数据重新整理一份。实例代码如下：

```python
class MyForm(forms.Form):
    username = forms.URLField(max_length=4)

    def get_errors(self):
        errors = self.errors.get_json_data()
        new_errors = {}
        for key,message_dicts in errors.items():
            messages = []
            #messges_dicts
            #[{'message':'1'}]
            #[{'message':'2'}]
            for message in message_dicts:
                messages.append(message['message'])
            new_errors[key] = messages
        return new_errors
    #{'telphone':['请输入正确的手机号']}
```

这样就可以把某个字段所有的错误信息直接放在这个列表中。