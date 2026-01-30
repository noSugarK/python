# 准备

Python > 3.10

django > 5.0

# 入门

## URL组成

URL是uniformResourceLocator的简写，统一资源定位符。

一个URL由以下几部分组成：

```
scheme://host:port/path/?query-string=xxx#anchor
```

- scheme：代表的是访问的协议，一般为http或者https以及ftp等。
- host：主机名，域名，比如`www.baidu.com`。
- port：端口号。HTTP协议是80端口，HTTPS协议是443端口。
- path：查找路径。比如:`www.jianshu.com/trending/now`，后面的trending/now就是path。
- query-string：查询字符串，比如:`www.baidu.com/s?wd=python`，后面的`wd=python`就是查询字符串。
- anchor：锚点，后台一般不用管，前端用来做页面定位的。
  注意：URL中的所有字符都是ASCII字符集，如果出现非ASCII字符，比如中文，浏览器会进行编码再进行传输。

## 创建一个Django项目

### 命令行方式

1. 创建项目：在终端中使用命令`django-admin startproject [项目名称]`，例如：

   ```bash
   django-admin startproject fristproject
   ```

   ![命令行创建项目](image/命令行创建项目.png)

2. 创建应用（app）：在终端中进入项目所在路径，执行如下命令均可创建一个app

   ```
   python manage.py startapp [app名称]
   ```

   ```
   django-admin startapp app_name
   ```

### pycharm方式

用pycharm新建一个Django项目

![创建Django项目](image/创建Django项目.png)

## 运行Django项目

### 命令行方式

```bash
python manage.py runserver
```

运行之后在`http://127.0.0.1:8000/`可以访问网站，若需要修改端口号可以在运行的时候指定端口号：`python manage.py runserver 9000`。

另外这样运行的项目只能在本机上访问，如果想要在其他电脑上访问本网站，那么需要指定ip地址为0.0.0.0，示例：

```bash
python manage.py runserver 0.0.0.0:8000
```

### pycharm方式

运行Django服务器即可

- manage.py：以后和项目交互基本上都是基于这个文件。一般都是在终端输入`python manage.py [子命令]`，可以输入`python manage.py help`看下能做什么事情。除非你知道你自己在做什么，一般情况下不应该编辑这个文件。
- settings.py：本项目的设置项，以后所有和项目相关的配置都是放在这个里面。
- urls.py：这个文件是用来配置URL路由的。比如访问http://127.0.0.1/news/是访问新闻列表页，这些东西就需要在这个文件中完成。
- wsgi.py：项目与wsGI协议兼容的web服务器入口，部署的时候需要用到的，一般情况下也是不需要修改的。

project和app的关系

一个app代表一个模块，所以URL请求的响应都是由app来处理。Django项目由许多app组成，一个app可以被用到其他项目，Django也能拥有不同的app。

## URL与视图的映射

```Python
#views.py
from django.shortcuts import HttpResponse

def index(request):
    return HttpResponse("hello!")

#urls.py
import views

urlpatterns = [
    path('',views.index)
]
```

### 在URL中携带参数

1. 通过查询字符串：http://127.0.0.1:8000/s?wd=python&a=1&b=2

   ```python
   # book/views.py
   from django.shortcuts import HttpResponse
   
   #http://127.0.0.1:8000/book?id=2
   def book_detail_query_string(request):
       # request.GET={"id":2}
       book_id = request.GET.get('id')
       return HttpResponse(f"图书id为：{book_id}")
   
   # urls.py
   from book import views
   
   urlpatterns = [
       path("admin/", admin.site.urls),
       path("book", views.book_detail_query_string)
   ]
   ```

   

2. 在path中携带：http://127.0.0.1:8000/book/2

   ```python
   # book/views.py
   from django.shortcuts import HttpResponse
   
   def book_detail_path(request,book_id):
       return HttpResponse(f"图书id为：{book_id}")
   
   # urls.py
   from book import views
   
   urlpatterns = [
       path("admin/", admin.site.urls),
       # http://127.0.0.1:8000/book/2
       path("book/<int:book_id>", views.book_detail_path)
   ]
   ```

### path函数

path函数的定义为:path(route,view,name=None,kwargs=None)。以下对这几个参数进行讲解。

1. route参数：url的匹配规则。这个参数中可以指定ur1中需要传递的参数，比如在访问文章详情页的时候，可以传递一个id。传递参数是通过尖括号来进行指定的。并且在传递参数的时候，可以指定这个参数的数据类型，比如文章的id都是int类型，那么可以这样写\<int:id\>，以后匹配的时候，就只会匹配到id为int类型的url，而不会匹配其他的url，并且在视图函数中获取这个参数的时候，就已经被转换成一个int类型了。其中还有几种常用的类型：
   - str：非空的字符串类型。默认的转换器。但是不能包含斜杠。
   - int：匹配任意的零或者正数的整形。到视图函数中就是一个int类型。
   - slug：由英文中的横杠-，或者下划线_连接英文字符或者数字而成的字符串。
   - uuid：匹配uuid字符串。
   - path：匹配非空的英文字符串，可以包含斜杠/。
2. view参数：可以为一个视图函数或者是类视图.as_view()或者是django.urls.include函数的返回值。
3. name参数：这个参数是给这个url取个名字的，这在项目比较大，url比较多的时候用处很大。

### URL中包含另外一个urls模块

```python
# urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("book", include("book.urls")),
]
```

```python
# book/urls.py
from django.urls import path

from . import views

# 指定应用名称(应用命名空间)
app_name = "book"

urlpatterns = [
    # http://127.0.0.1:8000/book?id=2&name=三国演义
    path("", views.book_detail_query_string, name="book_string"),
    # http://127.0.0.1:8000/book/2
    path("/<int:book_id>", views.book_detail_path, name="book_path"),
]
```

### URL反转

之前我们都是通过url来访问视图函数。有时候我们知道这个视图函数，但是想反转回他的url。这时候就可以通过reverse来实现。示例代码如下：

```python
from django.urls import reverse

reverse("list")
> /book/list/
```

如果有应用命名空间或者有实例命名空间，那么应该在反转的时候加上命名空间。示例代码如下：

```python
reverse('book:list')
> /book/list/
```

如果这个ur中需要传递参数，那么可以通过kwargs来传递参数。示例代码如下：

```python
reverse("book:detail",kwargs={"book_id":1})
> /book/detail/1
```

因为django中的reverse反转ur1的时候不区分GET请求和PosT请求，因此不能在反转的时候添加查询字符串的参数。如果想要添加查询字符串的参数，只能手动的添加。示例代码如下：

```python
login_url = reverse('login') + "?next=/"
```

# 模版

## 模版介绍

### 模板渲染

```python
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```

### 模版查找路径配置

在项目的`settings.py`文件中。有一个`TEMPLATES`配置，这个配置包含了模板引擎的配置，模板查找路径的配置，模板上下文的配置等。模板路径可以在两个地方配置。

1. DIRS：这是一个列表，在这个列表中可以存放所有的模板路径，以后在视图中使用`render`或者`render_to_string`渲染模板的时候，会在这个列表的路径中查找模板。
2. APP_DIRS：默认为True，这个设置为True后，会在INSTALLED_APPS的安装了的APP下的`templates`文件夹中查找模板。
3. 查找顺序：比如代码`render('1ist.html')`。先会在DIRS这个列表中依次查找路径下有没有这个模板，如果有，就返回。如果DIRS列表中所有的路径都没有找到，那么会先检查当前这个视图所处的app是否已经安装，如果已经安装了，那么就先在当前这个app下的`templates`文件夹中查找模板，如果没有找到，那么会在其他已经安装了的app中查找。如果所有路径下都没有找到，那么会抛出一个`TemplateDoesNotExist`的异常。

## DTL模版语法

### 变量

模板中可以包含变量，Django在渲染模板的时候，可以传递变量对应的值过去进行替换。变量的命名规范和Python非常类似，只能是阿拉伯数字和英文字符以及下划线的组合，不能出现标点符号等特殊字符。变量需要通过视图函数渲染，视图函数在使用`render`或者`render_to_string`的时候可以传递一个`context`的参数，这个参数是一个字典类型。以后在模板中的变量就从这个字典中读取值的。示例代码如下：

```python
# profile.html模板代码
<p>{{ username }}</p>

# views.py代码
def profile(request):
	username = '用户名'
	return render(request,'profile.html',context={'username':username})
```

模板中的变量同样也支持点（.的形式。在出现了点的情况，比`person.username`，模板是按照
以下方式进行解析的：

1. 如果person是一个字典，那么就会查找这个字典的username这个key对应的值。
2. 如果person是一个对象，那么就会查找这个对象的username属性，或者是username这个方法。
3. 如果出现的是person.1，会判断persons是否是一个列表或者元组或者任意的可以通过下标访问的对象，如果是的话就取这个列表的第1个值。如果不是就获取到的是一个空的字符串。

### 常用标签

#### if标签

```html
{% if user.age >= 18 %}
	已成年
{% else %}
	未成年
{% endif %}
```
{% raw %}
#### for标签

遍历列表

```html
{% for person in persons %}
	<p>{{ person.name }}</p>
{% endfor %}
```

如果需要反向遍历，则需加上reversed

```
{% for person in persons reversed %}
	<p>{{ person.name }}</p>
{% endfor %}
```

遍历字典

```html
{% for key,value in person.items %}
    <p>{{ key }}:{{ value }}</p>
{% endfor %}
```

在for循环中，DTL提供了一些变量可供使用。这些变量如下：

- forloop.counter：当前循环的下标。以1作为起始值。
- forloop.countero：当前循环的下标。以o作为起始值。
- forloop.revcounter：当前循环的反向下标值。比如列表有5个元素，那么第一次遍历这个属性是等于5，第二次是4，以此类推。并且是以1作为最后一个元素的下标。
- forloop.revcountero：类似于forloop.revcounter。不同的是最后一个元素的下标是从0开始。
- forloop.first：是否是第一次遍历。
- forloop.last：是否是最后一次遍历。
- forloop.parentloop：如果有多个循环嵌套，那么这个属性代表的是上一级的for循环。

#### for...in...empty标签

和for标签一样，只是当遍历的对象没有元素时输出empty中的内容

```html
{% for person in persons %}
	<p>{{ person.name }}</p>
{% empty %}
	没有任何人
{% endfor %}
```

#### with标签

在模板中定义变量。可以将复杂的变量缓存到一个变量上，后面可以直接使用这个变量。

```python
context = [
	"person" : ["张三", "李四"]
]

{% with lisi=person.1 %}
	<p>{{ lisi }}</p>
{% endwith %}
```

**tips：**

- with语句中定义的变量只能在{%with%}...{%wndwith%}中使用，无法在标签外使用。

- 定义变量时不能在等号两边留有空格。

- 也可以使用`with ... as ...`进行赋值

  ```python
  {% with person.1 as lisi %}
  	<p>{{ lisi }}</p>
  {% endwith %}
  ```

#### url标签

在模版中，我们经常要写一些url，比如某个a标签中需要定义`href`属性。当然如果通过硬编码的方式直接将这个url写死在里面也是可以的。但是这样对于以后项目维护可能不是一件好事。因此建议使用这种反转的方式来实现，类似于`django`中的`reverse`一样。示例代码如下:

```html
<a href="{% url 'book:list' %}">图书列表页面</a>
```

如果url反转的时候需要传递参数，那么可以在后面传递。但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用。示例代码如下：

```python
# path部分
path('detail/<book_id>/',views.book_detail,name='detail')

#url反转，使用位置参数
<a href="{%url 'book:detail' 1 %}">图书详情页面</a>

#url反转，使用关键字参数
<a href="{% url 'book:detail' book_id=1 %}">图书详情页面</a>
```

如果想要在使用ur标签反转的时候要传递查询字符串的参数，那么必须要手动在后面添加。示例代码如下：

```html
<a href="{% url 'book:detail' book_id=1 %}?page=1">图书详情页面</a>
```

如果需要传递多个参数，那么通过空格的方式进行分隔。示例代码如下：

```html
<a href="{% url 'book:detail' book_id=1 page=2 %}">图书详情页面</a>
```

#### block标签

用于填写模板内容，详见[模板继承](#模板继承)。

#### extends标签

继承标签，写在模板第一行。用于[模板继承](#模板继承)。

### 常用过滤器

在模板中，有时候需要对一些数据进行处理以后才能使用。一般在Python中是通过函数的形式来完成的。而在模板中，则是通过过滤器来实现的。过滤器的使用则是通过`|`来使用。

#### add

将传进来的参数添加到原来的值上面。这个过滤器会尝试将值和参数转换成整形然后进行相加。如果转换成整形过程中失败了，那么会将值和参数进行拼接。如果是字符串，那么会拼接成字符串，如果是列表，那么会拼接成一个列表。示例代码如下：

```python
{{ value|add:'2' }}
```

如果value是等于4，那么结果将是6。如果value是等于一个普通的字符串，比如abc，那么结果将是abc2。add过滤器的源代码如下：

```python
def add(value, arg):
    try:
        return int(value) + int(arg)
    except (valueError, TypeError):
        try:
            return value + arg
        except Exception:
        	return ''
```

#### cut

移除值中所有指定的字符串。如下示例是移除value中所有的空格：

```python
{{ value | cut:' ' }}
```

#### date

将一个日期按照指定的格式，格式化字符串。

```python
context = [
    'nowTime' : datetime.now()
]

当前时间{{ nowTime | date:"Y年m月d日" }}
```

| 格式字符串 | 描述                    | 显示范围  |
| ---------- | ----------------------- | --------- |
| Y          | 年，四位数              | 1949~9999 |
| m          | 月，两位数              | 01~12     |
| n          | 月，无前缀0             | 1~12      |
| d          | 天，两位数              | 01~31     |
| j          | 天，无前缀0             | 1~31      |
| g          | 小时，12小时制，无前缀0 | 0~11      |
| h          | 小时，12小时制，两位数  | 00~11     |
| G          | 小时，24小时制，无前缀0 | 0~23      |
| H          | 小时，24小时制，两位数  | 00~23     |
| i          | 分钟，两位数            | 00~59     |
| s          | 秒，两位数              | 00~59     |

#### default

如果值被评估为False。比如`[]`，`""`，`None`，`{}`等这些在if判断中为`False`的值，都会使用`default`过滤器提供的默认值。示例代码如下：

```python
{{ value | default:"nothing" }}
```

如果value是等于一个空的字符串。比如`""`，那么以上代码将会输出`nothing`。


#### deault_if_none

如果值是`None`，那么将会使用`default_if_none`提供的默认值。这个和`default`有区别，`default`是所有被评估为`False`的都会使用默认值。而`default_if_none`则只有这个值是等于`None`的时候才会使用默认值。示例代码如下：

```python
{{ value | default_if_none:"nothing" }}
```

如果value是等于`""`也即空字符串，那么以上会输出空字符串。如果value是一个`None`值，以上代码才会输出`nothing`。

#### first

返回列表/元组/字符串中的第一个元素。

```python
{{ value | first }}
```

#### last

返回列表/元组/字符串中的最后一个元素。

```python
{{ value | last }}
```

| value         | first输出 | last输出 |
| ------------- | --------- | -------- |
| ['a','b','c'] | a         | c        |
| abc           | a         | c        |
| ('a','b','c') | a         | c        |

#### length

获取列表/元组/字典/字符串的长度。

```python
{{ value | length }}
```

#### lower

将值中的所有字符转换成小写。

```python
{{ value | lower }}
```

#### upper

将值中的所有字符转换成大写。

```python
{{ value | upper }}
```

#### join

将列表/元组/字符串用指定字符进行拼接。

```python
{{ value | join:'/' }}
```

#### floatformat

使用四舍五入的方式格式化一个浮点类型。如果这个过滤器没有传递任何参数。那么只会在小数点后保留一个小数，如果小数后面全是0，那么只会保留整数。当然也可以传递一个参数，标识具体要保留几个小数。

##### 如果没有传递参数：

|value      | 模版代码                       |输出|
|-----------|----------------------------|----|
|34.23234   | {{ value \| floatformat }} |34.2|
|34.000     | {{ value \| floatformat }} |34|
|34.260     | {{ value \| floatformat }} |34.3|

##### 如果传递参数：

|value | 模版代码                         |输出|
|------|------------------------------|----|
|34.23234| {{ value \| floatformat:3 }} |34.232|
|34.0000 | {{ value \| floatformat:3 }} |34.000|
|34.26000| {{ value \| floatformat:3 }} |34.260|

#### random

在被给的列表/元组/字符串中随机的选择一个值。

```python
{{ value | random }}
```

#### slice

类似于Python中的切片操作。

```python
{{ some_list | slice:"2" }}
```

以上代码将会给some_list从2开始做切片操作。

#### safe

标记一个字符串是安全的。也级会关掉这个字符串的自动转义。

```python
{{ value | safe }}
```

如果value是一个不包含任何特殊字符的字符串，比如`<a>`这种，那么以上代码就会把字符串正常的输入。如果value是一串`html`代码，那么以上代码将会把这个html代码渲染到浏览器中。

#### striptags

删除字符串中所有的html标签。

```python
{{ value | striptags }}
```

如果是`<strong>hello world</strong>`，则会输出`hello world`。

#### truncatechars

如果给定的字符串长度超过了过滤器指定长度。那么就会进行切割，并且会拼接三个点来作为省略号。

```python
{{ value | truncatechars:"5" }}
```

如果value是"北京欢迎您~"，那么输出结果是"北京欢迎..."。省略号占一个字符。

#### truncatechars_html

类似于truncatechars，只不过不会切割html标签。

```python
{{ value | truncatechars_html:5 }}
```

如果value是`<p>北京欢迎您~</p>`，那么输出是`<p>北京欢迎···</p>`。

### 模板结构

#### include模板

有时候一些代码是在许多模版中都用到的。如果我们每次都重复的去拷贝代码那肯定不符合项目的规范。一般我们可以把这些重复性的代码抽取出来，就类似于Python中的函数一样，以后想要使用这些代码的时候，就通过include包含进来。这个标签就是include。示例代码如下：

```html
# header.html
<p>我是header</p>

# footer.html
<p>我是footer</p>

# main.html
{% include 'header.html' %}
<p>我是main内容</p>
{% include 'footer.html' %}
```

include标签寻找路径的方式。也是跟render渲染模板的函数是一样的。

默认include标签包含模版，会自动的使用主模版中的上下文，也即可以自动的使用主模版中的变量。如果想传入一些其他的参数，那么可以使用with语句。示例代码如下：

```
# header.html
<p>用户名：{{username }}</p>

# main.html
{% include "header.html" with username='huangyong' %}
```

#### 模板继承

在前端页面开发中。有些代码是需要重复使用的。这种情况可以使用inc1ude标签来实现。也可以使用另外一个比较强大的方式来实现，那就是模版继承。模版继承类似于Python中的类，在父类中可以先定义好一些变量和方法，然后在子类中实现。模版继承也可以在父模版中先定义好一些子模版需要用到的代码，然后子模版直接继承就可以了。并且因为子模版肯定有自己的不同代码，因此可以在父模版中
定义一个block接口，然后子模版再去实现。以下是父模版的代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="{% static 'style.css' %}"/>
    <title>{% block title %}我的站点{% endblock %}</title>
</head>
<body>
    <div id="sidebar">
        {% block sidebar %}
            <ul>
                <li><a href="/">首页</a></li>
                <li><a href="/blog/">博客</a></li>
            </ul>
        {% endblock %}
    </div>
    <div id="content">
    	{% block content %}{% endblock %}
    </div>
</body>
</html>
```

这个模版，我们取名叫做`base.html`，定义好一个简单的`html`骨架，然后定义好两个`b1ock`接口，让子模版来根据具体需求来实现。子模板然后通过`extends`标签来实现，示例代码如下：

```html
{% extends "base.html" %}

{% block title %}博客列表{% endblock %}

{% block content %}
    {% for entry in blog_entries %}

    <h2>{{ entry.title }}</h2>
    <p>{{ entry.body }}</p>

    {% endfor %}
{% endblock %}
```

**需要注意的是：extends标签必须放在模版的第一行。子模板中的代码必须放在block中，否则将不会被渲染。**

如果在某个block中需要使用父模版的内容，那么可以使用`{{ b1ock.super }}`来继承。比如上例，`{% b1ock title %}`，如果想要使用父模版的`title`，那么可以在子模版的`title block`中使用`{{ block.super }}`来实现。

在定义block的时候，除了在block开始的地方定义这个b1ock的名字，还可以在block结束的时候定义名字。比如`{% block title %}{% endblock title %}`。这在大型模版中显得尤其有用，能让你快速的看到block包含在哪里。

## 加载静态文件

在一个网页中，不仅仅只有一个html骨架，还需要css样式文件，执行文件以及一些图片等。因此在DTL中加载静态文件是一个必须要解决的问题。在DTL中，使用`static`标签来加载静态文件。要使用static标签，首先需要`{% load static %}`。加载静态文件的步骤如下：

1. 首先确保`django.contrib.staticfiles`已经添加到`settings.INSTALLED_APPS`中。

2. 确保在`settings.py`中设置了`STATIC_URL`。比如：

   ```
   STATIC_URL = 'static/"
   ```

3. 在已经安装了的app下创建一个文件夹叫做static，然后再在这个static文件夹下创建一个当前app的名字的文件夹，再把静态文件放到这个文件夹下。例如你的app叫做book，有一个静态文件叫做`zhi1liao.Jpg`，那么路径为`book/static/book/zhiliao.Jpg`。（为什么在app下创建一个static文件夹，还需要在这个static下创建一个同app名字的文件夹呢？原因是如果直接把静态文件放在static文件夹下，那么在模版加载静态文件的时候就是使用`zhi1iao.jpg`，如果在多个app之间有同名的静态文件，这时候可能就会产生混淆。而在static文件夹下加了一个同名app文件夹，在模版中加载的时候就是使用`app/zhiliao.jpg`，这样就可以避免产生混淆。）

4. 如果有一些静态文件是不和任何app挂钩的。那么可以在settings.py中添加STATICFILES_DIRS，以后DTL就会在这个列表的路径中查找静态文件。比如可以设置为：

   ```python
   STATICFILES_DIRS = [
       BASE_DIR / "static"
   ]
   ```

5. 在模板中使用load标签加载static标签。比如要加载在项目的static文件夹下的css文件夹中的index.css文件。示例代码如下：

   ```html
   {% load static %}
   <link rel="stylesheet" href="{% static 'css/index.css' %}">
   ```

6. 如果不想每次都在模板中加载静态文件都使用load加载static标签，可以在`settings.py`中的`TEMPLATES/OPTIONS`添加`'builtins' : ['django.templatetags.static']`，这样以后在模板中就可以直接使用`static`标签，不用手动load了。

   ```python
   TEMPLATES = [
       {
           "BACKEND": "django.template.backends.django.DjangoTemplates",
           "DIRS": [BASE_DIR / "templates"],
           "APP_DIRS": True,
           "OPTIONS": {
               "context_processors": [
                   "django.template.context_processors.request",
                   "django.contrib.auth.context_processors.auth",
                   "django.contrib.messages.context_processors.messages",
               ],
               # 这里加载
               'builtins' : ['django.templatetags.static']
           },
       },
   ]
   ```

7. 如果没有在settings.INSTALLED_APPS中添加django.contrib.staticfiles。那么我们就需要手动的将请求静态文件的url与静态文件的路径进行映射了，这个操作通常用来加载媒体文件（上传的文件）。示例代码如下：

   ```python
   from django.conf import settings
   from django.conf.urls.static import static
   
   urlpatterns = [
   	path('admin/', admin.site.urls),
   	...
   ]+ static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
   ```

   在settings.py中的对MEDIA_URL和MEDIA_ROOT的配置如下：

   ```python
   MEDIA_ROOT = BASE_DIR / 'media'
   MEDIA_URL= '/media/'
   ```

   **注意：静态文件和媒体文件，最好都是通过Nginx等专业的web服务器来部署，以上方式仅在开发阶段使用。**

# 数据库

## 相关软件

mysql下载[MySQL :: Download MySQL Community Server (Archived Versions)](https://downloads.mysql.com/archives/community/)

[到底是谁还卸载不干净MySql呀！_mysql和mysql80两个服务-CSDN博客](https://blog.csdn.net/DRUN_K/article/details/135125606)

### navicat

### MySQL相关驱动

MySQL-python

mysqlclient

pymysql

MySQL Connector/Python

## 数据库操作

### 在settings.py中配置

```python
DATABASES = {
    "default": {
        # 数据库引擎
        "ENGINE": "django.db.backends.mysql",
        # 数据库名称
        "NAME": "database_demo",
        # 连接MySQL数据库的用户名
        'USER': 'root',
        # 连接MySQL数据库的密码
        'PASSWORD': '123456',
        # MySQL数据库的主机地址
        'HOST': '127.0.0.1',
        # MySQL数据库的端口号
        'PORT': '3306',
    }
}
```

### 操作数据库

#### 新建一个app为book

报错

```bash
(D:\conda_envs\django) PS D:\PycharmProjects\template_demo> python manage.py startapp book
Traceback (most recent call last):
  File "D:\conda_envs\django\Lib\site-packages\django\db\backends\mysql\base.py", line 16, in <module>
    import MySQLdb as Database
ModuleNotFoundError: No module named 'MySQLdb'

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "D:\PycharmProjects\template_demo\manage.py", line 22, in <module>
    main()
  File "D:\PycharmProjects\template_demo\manage.py", line 18, in main
    execute_from_command_line(sys.argv)
  File "D:\conda_envs\django\Lib\site-packages\django\core\management\__init__.py", line 442, in execute_from_command_line
    utility.execute()
  File "D:\conda_envs\django\Lib\site-packages\django\core\management\__init__.py", line 416, in execute
    django.setup()
  File "D:\conda_envs\django\Lib\site-packages\django\__init__.py", line 24, in setup
    apps.populate(settings.INSTALLED_APPS)
  File "D:\conda_envs\django\Lib\site-packages\django\apps\registry.py", line 116, in populate
    app_config.import_models()
  File "D:\conda_envs\django\Lib\site-packages\django\apps\config.py", line 269, in import_models
    self.models_module = import_module(models_module_name)
                         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\importlib\__init__.py", line 90, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "<frozen importlib._bootstrap>", line 1387, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1360, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1331, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 935, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 999, in exec_module
  File "<frozen importlib._bootstrap>", line 488, in _call_with_frames_removed
  File "D:\conda_envs\django\Lib\site-packages\django\contrib\auth\models.py", line 5, in <module>
    from django.contrib.auth.base_user import AbstractBaseUser, BaseUserManager
  File "D:\conda_envs\django\Lib\site-packages\django\contrib\auth\base_user.py", line 43, in <module>
    class AbstractBaseUser(models.Model):
  File "D:\conda_envs\django\Lib\site-packages\django\db\models\base.py", line 145, in __new__
    new_class.add_to_class("_meta", Options(meta, app_label))
  File "D:\conda_envs\django\Lib\site-packages\django\db\models\base.py", line 373, in add_to_class
    value.contribute_to_class(cls, name)
  File "D:\conda_envs\django\Lib\site-packages\django\db\models\options.py", line 238, in contribute_to_class
    self.db_table, connection.ops.max_name_length()
                   ^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\site-packages\django\utils\connection.py", line 15, in __getattr__
    return getattr(self._connections[self._alias], item)
                   ~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\site-packages\django\utils\connection.py", line 62, in __getitem__
    conn = self.create_connection(alias)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\site-packages\django\db\utils.py", line 193, in create_connection
    backend = load_backend(db["ENGINE"])
              ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\site-packages\django\db\utils.py", line 113, in load_backend
    return import_module("%s.base" % backend_name)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\importlib\__init__.py", line 90, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "D:\conda_envs\django\Lib\site-packages\django\db\backends\mysql\base.py", line 18, in <module>
    raise ImproperlyConfigured(
django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module.
Did you install mysqlclient?
```

在settings.py的同目录下的`__init__.py`中写入

```python
import pymysql
pymysql.install_as_MySQLdb()
```

重新新建app即可

#### 在app中获取数据库信息

> book/views.py

```python
from django.shortcuts import render
# 使用django封装好的connection对象，会自动读取settings.py中数据库的配置信息
from django.db import connection
# Create your views here.
def index(request):
    return render(request, 'index.html')

def book_list(request):
    # 获取游标对象
    cursor = connection.cursor()
    # 拿到游标对象后执行sql语句
    cursor.execute("select * from book")
    # 获取所有的数据
    book_list = cursor.fetchall()
    for book in book_list:
        print(book)
    context = {
        'book_list': book_list
    }
    return render(request, 'book_list.html', context=context)

def book_info(request, book_id):
    context = {
        'book_id': book_id
    }
    return render(request, 'book_info.html', context=context)
```

### cursor对象常用接口

PythonDBAPI下规范下cursor对象常用接口：

[PEP 249 – Python Database API Specification v2.0 | peps.python.org](https://peps.python.org/pep-0249/#cursor-methods)

1. description：如果cursor执行了查询的sql代码。那么读取`cursor.description`属性的时候，将返回一个列表，这个列表中装
   的是元组，元组中装的分别是`(name,type_code,display_size,internal_size,precision,scale,null_ok)`，其中name代表的是查找出来的数据的字段名称，其他参数暂时用处不大。

2. rowcount：代表的是在执行了sql语句后受影响的行数。

3. close：关闭游标。关闭游标以后就再也不能使用了，否则会抛出异常。

4. execute(sql[，parameters])：执行某个sql语句。如果在执行sql语句的时候还需要传递参数，那么可以传给parameters参数。示例代码如下：

   ```python
   cursor.execute("select * from article where id=%s",(1,))
   ```

5. fetchone：在执行了查询操作以后，获取第一条数据。

6. fetchmany(size)：在执行查询操作以后，获取多条数据。具体是多少条要看传的size参数。如果不传size参数，那么默认是获取第一条数据。

7. fetchall：获取所有满足sql语句的数据。

## ORM模型

### 创建ORM模型

在app的model.py文件中创建模型

> book/model.py

```python
from django.db import models

class Book(models.Model):
    name = models.CharField(max_length=100)
    author = models.CharField(max_length=20)
    price = models.FloatField(default=0.0)
    publish_date = models.DateTimeField(auto_now=True)
```

### 映射模型到数据库中

1. 在settings.py中，配置好DATABASES，做好数据库相关的配置。

2. 在app中的models.py中定义好模型，这个模型必须继承自django.db.models。

3. 将这个app添加到settings.py的INSTALLED_APP中。

4. 生成迁移脚本

   ```
   python manage.py makemigrations
   ```

5. 将迁移脚本文件映射到数据库中

   ```
   python manage.py migrate
   ```

## 基本CRUD操作

### 添加一个模型到数
首先需要创建一个模型。创建方法和创建普通python对象是一样的。在创建完模型后需要调用模型的save方法，这样Django会自动将这个模型转换成sql语句，然后存储到数据库中。

```python
class Book(models.Model):
    name = models.CharField(max_length=100)
    author = models.CharField(max_length=20)
    price = models.FloatField(default=0.0)
    publish_date = models.DateTimeField(auto_now_add=True)
```

```python
from django.http import HttpResponse
from .models import Book

def add_book(request):
    book = Book(author='罗贯中', name='三国演义', price=99.9)
    book.save()
    return HttpResponse('添加成功')
```

### 查找数据

查找数据都是通过模型下的objects对象来实现的。

#### 查找所有数据

查找Book这个模型对应的表下的所有数据。

```python
books = Book.objects.all()
```

#### 数据过滤

在查找数据的时候，可以通过调用objects的filter方法进行数据过滤。

```python
books = Book.objects.filter(name="三国演义")

#多条件
books = Book.objects.filter(name="三国演义",desc='test')
```

调用filter，会将所有满足条件的模型对象都返回。

#### 获取单个对象

如果只需要返回第一个满足条件的对象，可以使用get方法。

```python
def book_info(request, book_id):
    try:
        book = Book.objects.get(id=book_id)
    except Book.DoesNotExist:
        return HttpResponse('图书不存在')

    context = {
        'book': book
    }
    return render(request, 'book_info.html', context=context)
```

如果没有找到满足条件的对象会抛出一个异常，而filter则是返回一个空列表。

### 数据排序

如果想在查找数据的时候使用某个字段来进行排序，可以使用order_by方法来实现。

```python
books = Book.objects.order_by('publish_date')
```

如果需要从大到小排序

```python
books = Book.objects.order_by('-publish_date')
```

### 修改数据

```python
def update_book(request):
    book = Book.objects.get(id=1)
    book.name = '西游记'
    book.price = 99.9
    book.save()
    return HttpResponse('更新成功')
```

### 删除数据

查找到数据即可调用delete方法进行删除

```python
def delete_book(request):
    book = Book.objects.get(id=1)
    book.delete()
    return HttpResponse('删除成功')
```

## 常用Field和参数

### 数据库表字段类型

| 字段类型                  | 字段解释                                                     |
| ------------------------- | ------------------------------------------------------------ |
| AutoField                 | 自增整数                                                     |
| BigAutoField              | 长自增型整数                                                 |
| BooleanField              | 布尔类型                                                     |
| CharField                 | 必须要传递max_length这个关键字参数进去                       |
| DateField                 | 日期类型。使用这个Field可以传递以下几个参数：<br>auto_now<br> auto_now_add<br> |
| DateTimeField             | 日期时间类型                                                 |
| TimeField                 | 时间类型                                                     |
| EmailField                | 邮箱类型                                                     |
| FileField                 | 文件类型                                                     |
| ImageField                | 图片类型                                                     |
| FloatField                | 浮点数类型                                                   |
| IntegerField              | 整型                                                         |
| BigIntegerField           | 大整型                                                       |
| PositiveIntegerField      | 正整型                                                       |
| SmallIntegerField         | 小整型                                                       |
| PositiveSmallIntegerField | 小正整型                                                     |
| TextField                 | 大量的文本类型                                               |
| UUIDField                 | 只能存储uuid格式的字符串。uuid是一个32位的全球唯一的字符串，一般用来做主键。 |
| URLField                  | 字符串类型，在表单层面判断是否为URL格式                      |

### 常用参数

| 常用参数 |参数解释|
|------|-|
|null|默认是为False。如果设置为True，Django将会在映射表的时候指为可以为空。在使用字符串相关的`Field (CharField/TextField)`的时候，官方推荐尽量不要使用这个参数，也就是保持默认值False。因为Django在处 理字符串相关的Field的时候，即使这个Field的null=False，如果你没有给这个Field传递任何值，那么Django也会使用一个空的字符串`""`来作为默认值存储进去。因此如果再使用null=True，Django会产生两种空值的情形（NULL或者空字符串）。如果想要在表单验证的时候允许这个字符串为空，那么建议使用 blank=True。如果你的Field是BooleanField，那么对应的可空的字段则为NullBooleanField。|
|blank|标识这个字段在表单验证的时候是否可以为空。默认是False。这个和null是有区别的，null是一个纯数据库级别的。而blank是表单验证级别的。|
|db_column|这个字段在数据库中的名字。如果没有设置这个参数，那么将会使用模型中属性的名字。|
|default|默认值。可以为一个值，或者是一个函数，但是不支持lambda表达式。并且不支持列表/字典/集合等可变的数据结构。|
|primary_key|是否为主键。默认是false|
|unique|在表中这个字段的值是否唯一。一般是手机号/邮箱等|

更多请参考官方文档：https://docs.djangoproject.com/zh-hans/5.0/ref/models/fields/

### 模型中`Meta`配置

对于一些模型级别的配置。我们可以在模型中定义一个类，叫做Meta。然后在这个类中添加一些类属性来控制模型的作用。比如我们想要在数据库映射的时候使用自己指定的表名，而不是使用模型的名称。那么我们可以在Meta类中添加一个db_table的属性。示例代码如下：

```python
class Book(models.Model):
    name = models.CharField(max_length=20,null=False)
    desc =models.CharField(max_length=10o,name='description',db_column="description1")
    class Meta:
        db_table = 'book_model'
```

以下将对Meta类中的一些常用配置进行解释。

#### db_table:

这个模型映射到数据库中的表名。如果没有指定这个参数，那么在映射的时候将会使用模型名来作为默认的表名。

#### ordering:

设置在提取数据的排序方式。后面章节会讲到如何查找数据。比如我想在查找数据的时候根据添加的时间排序，那么示例代码如下：

```python
class Book(models.Model):
    name = models.CharField(max_length=20,null=False)
    desc =models.CharField(max_length=10o,name='description',db_column="description1")
    pub_date = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        db_table = 'book_model'
        ordering = ['-pub_date', 'name']
```

详见官方文档：https://docs.djangoproject.com/zh-hans/5.0/ref/models/options/

## 外键和表关系

### 外键

在MySQL中，表有两种引l擎，一种是`InnoDB`，另外一种是`myisam`。如果使用的是`InnoDB`引擎，是支持外键约束的。外键的存在使得`ORM`框架在处理表关系的时候异常的强大。因此这里我们首先来介绍下外键在`Django`中的使用。

类定义为`class Foreignkey(to，on_delete，**options)`。第一个参数是引l用的是哪个模型，第二个参数是在使用外键引用的模型数据被删除了，这个字段该如何处理，比如有`CASCADE、SET_NULL`等。这里以一个实际案例来说明。比如有一个User和一个Article两个模型。一个User可以发表多篇文章，一个Article只能有一个Author，并且通过外键进行引用。那么相关的示例代码如下：

```python
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)
    
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()

    # author_id foreign key(user.id)
    author = models.ForeignKey("User",on_delete=models.CASCADE)
```

如果说模型不在同一app中，使用`app名称.模型名称`进行引用

```python
# User模型在user app中
class User(models.Model):
    username = models.CharField(max_length=20)
    password = models.CharField(max_length=100)
    
# Article模型在article app中
class Article(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()

    # author_id foreign key(user.id)
    author = models.ForeignKey("user.User",on_delete=models.CASCADE)
```

如果模型的外键引l用的是本身自己这个模型，那么to参数可以为'se1f'，或者是这个模型的名字。在论坛开发
中，一般评论都可以进行二级评论，即可以针对另外一个评论进行评论，那么在定义模型的时候就需要使用外键来
引用自身。示例代码如下：

```python
class Comment(models.Model):
	content = models.TextField()
	origin_comment = models.ForeignKey('self',on_delete=models.CAScADE,null=True)

# 或者
#origin_comment=models.ForeignKey('Comment',on_delete=models.CASCADE,null=True)
```

### 外键删除操作

如果一个模型使用了外键。那么在对方那个模型被删掉后，该进行什么样的操作。可以通过on_delete来指定。可以指定的类型如下：

| 类型          | 解释                                                                                                   |
|-------------|------------------------------------------------------------------------------------------------------|
| CASCADE     | 级联操作。如果外键对应的那条数据被删除了，那么这条数据也会被删除。                                                                    |
| PROTECT     | 受保护。即只要这条数据引I用了外键的那条数据，那么就不能删除外键的那条数据。                                                               |
| SET_NULL    | 设置为空。如果外键的那条数据被删除了，那么在本条数据上就将这个字段设置为空。如果设置这个选项，前提是要指定这个字段可以为空。                                       |
| SET_DEFAULT | 设置默认值。如果外键的那条数据被删除了，那么本条数据上就将这个字段设置为默认值。如果设置这个选项，前提是要指定这个字段一个默认值。                                    |
| SETO        | 如果外键的那条数据被删除了。那么将会获取SET函数中的值来作为这个外键的值。SET函数可以接收一个可以调用的对象（比如函数或者方法），如果是可以调用的对象，那么会将这个对象调用后的结果作为值返回回去。 |
| DO_NOTHING  | 不采取任何行为。一切全看数据库级别的约束。                                                                                |

**以上这些选项只是Django级别的，数据级别依旧是RESTRICT！**

### 表关系

表之间的关系都是通过外键来进行关联的。而表之间的关系，无非就是三种关系：一对一、一对多（多对一）、多对多等。以下将讨论一下三种关系的应用场景及其实现方式。

#### 一对多

##### 应用场景

比如文章和作者之间的关系。一个文章只能由一个作者编写，但是一个作者可以写多篇文章。文章和作者之间的关系就是典型的多对一的关系。

##### 实现方式

一对多或者多对一，都是通过`ForeignKey`来实现的。还是以文章和作者的案例进行讲解。

```python
class User(models.Model):
	username = models.CharField(max_length=20)
	password = models.CharField(max_length=100)
    
class Article(models.Model):
	title = models.CharField(max_length=100)
	content = models.TextField()
	author = models.ForeignKey("User",on_delete=models.CASCADE)
```

那么以后在给Article对象指定author，就可以使用以下代码来完成：

```python
article = Article(title='abc',content='123')
author = User(username='zhiliao',password='111l11')
#要先保存到数据库中
author.save()
article.author = author
article.save()
```

并且以后如果想要获取某个用户下的所有文章，可以通过`article_set`来实现。

```python
user = User.objects.get(id='zhangsan')
articles = user.article_set.all()
```

#### 一对一

##### 应用场景

比如一个用户表和一个用户信息表。在实际网站中，可能需要保存用户的许多信息，但是有些信息是不经常用的。如果把所有信息都存放到一张表中可能会影响查询效率，因此可以把用户的一些不常用的信息存放到另外一张表中我们叫做userExtension。但是用户表user和用户信息表userExtension就是典型的—对一了。

##### 实现方式

Django为一对一提供了一个专门的Field叫做`OneToOneField`来实现一对一操作。示例代码如下:

```python
class User(models.Model):
	username = models.CharField(max_length=20)
	password = models.CharField(max_length=100)
class UserExtension(models.Model):
	birthday = models.DateTimeField(null=True)
	school = models.CharField(blank=True,max_length=50)
	user = models.OneToOneField("User", on_delete=models.CASCADE)
```

在`UserExtension`模型上增加了一个一对一的关系映射。其实底层是在`UserExtension`这个表上增加了一个user_id，来和user进行关联，并且这个外键数据在表中必须是唯一的，来保证一对一。

#### 多对多

##### 应用场景

比如文章和标签的关系。一篇文章可以有多个标签，一个标签可以被多个文章所引用。因此标签和文章的关系是典型的多对多的关系。

##### 实现方式

Django为这种多对多的实现提供了专门的Fie1d。叫做`ManyToManyField`。还是拿文章和标签为例进行讲解。示例代码如下：

```python
class Article(models.Model):
	title = models.CharField(max_length=100)
	content = models.TextField()
	tags = models.ManyToManyField("Tag",related_name="articles")
    
class Tag(models.Model):
	name = models.CharField(max_length=50)
```

在数据库层面，实际上Django是为这种多对多的关系建立了一个中间表。这个中间表分别定义了两个外键，引用到article和tag两张表的主键。

{% endraw %}

## 查询操作