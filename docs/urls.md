# urls模块化
如果项目变得越来越大。那么url会变得越来越多。如果都放在主`urls.py`文件中，那么将不太好管理。因此我们可以将每个app自己的urls放到自己的app中进行管理。一般我们会在app中新建一个urls.py文件用来存储所有和这个app相关的子url。  
需要注意的地方：  
1. 应该使用`include`函数包含子`urls.py`，并且这个`urls.py`的路径是相对于项目的路径。示例代码如下：  
    ```python
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('book',include('book.urls'))
    ]
    ```  
2. 在`app`的`urls.py`中，所有的url匹配也要放在一个叫做`urlpatterns`的变量中，否则找不到。
3. `url`是会根据主`urls.py`和app中的`urls.py`进行拼接的，因此注意不要多加斜杠。



# url传递参数
## url映射
1. 为什么会去urls.py文件中寻找映射呢？
是因为在`settings.py`文件中配置了`ROOT_URLCONF`为`urls.py`。所有django会去`urls.py`中寻找。
2. 在`urls.py`中我们所有的映射，都应该放在`urlpatterns`这个变量中。
3. 所有的映射不是随便写的，而是使用`path`函数或者是`re_path`函数进行包装的。

## url传参数
1. 采用在url中使用变量的方式：在path的第一个参数中，使用`<参数名>`的方式可以传递参数。然后在视图函数中也要写一个参数，视图函数中的参数必须和url中的参数名称保持一致，不然就找不到这个参数。另外，url中可以传递多个参数。
2. 采用查询字符串的方式：在url中，不需要单独的匹配查询字符串的部分。只需要在视图函数中使用`request.GET.get('参数名称')`的方式来获取。示例代码如下：  
    ```python
    def author_detail(request):
        author_id = request.GET['id']
        text = '作者的id是：%s' % author_id
        return HttpResponse(text)
    ```  
    因为查询字符串使用的是`GET`请求，所以我们通过`request.GET`来获取参数。并且因为`GET`是一个类似于字典的数据类型，所有获取值跟字典的方式都是一样的。

## url参数的转换器
1. str：除了斜杠`/`以外所有的字符都是可以的。
2. int：只有是一个或者多个的阿拉伯数字。
3. path：所有的字符都是满足的。
4. uuid：只有满足`uuid.uuid4()`这个函数返回的字符串的格式。
5. slug：英文中的横杆或者英文字符或者阿拉伯数字或者下划线才满足。

# URL映射的时候指定默认参数
使用path或者是re_path的后，在route中都可以包含参数，而有时候想指定默认的参数，这时候可以通过以下方式来完成。示例代码如下：
```
from django.urls import path

from . import views

urlpatterns = [
    path('blog/', views.page),
    path('blog/page<int:num>/', views.page),
]

# View (in blog/views.py)
def page(request, num=1):
    # Output the appropriate page of blog entries, according to num.
    ...
```
当在访问blog/的时候，因为没有传递num参数，所以会匹配到第一个url，这时候就执行view.page这个视图函数，而在page函数中，又有num=1这个默认参数。因此这时候就可以不用传递参数。而如果访问blog/1的时候，因为在传递参数的时候传递了num，因此会匹配到第二个url，这时候也会执行views.page，然后把传递进来的参数传给page函数中的num。



# include函数的用法
1. include(module,namespace=None)：
    * module：子url的模块字符串。
    * namespace：实例命名空间。这个地方需要注意一点。如果指定实例命名空间，那么前提必须要先指定应用命名空间。也就是在子`urls.py`中添加`app_name`变量。
2. include((pattern_list, app_namespace), namespace=None)：`include`函数的第一个参数既可以为一个字符串，也可以为一个元组，如果是元组，那么元组的第一个参数是子`urls.py`模块的字符串，元组的第二个参数是应用命名空间。也就是说，应用命名空间既可以在子`urls.py`中通过`app_name`指定，也可以在`include`函数中指定。
3. include(pattern_list)：`pattern_list`是一个列表。这个列表中装的是`path`或者`re_path`函数。实例代码如下：
    ```pyhon
    path('movie/',include([
        path('',views.movie),
        path('list/',views.movie_list),
    ]))
    ```


# url命名

## 为什么需要url命名？
因为url是经常变化的。如果在代码中写死可能会经常改代码。给url取个名字，以后使用url的时候就使用他的名字进行反转就可以了，就不需要写死url了。

## 如何给一个url指定名称？
在`path`函数中，传递一个`name`参数就可以指定。示例代码如下：
```python
urlpatterns = [
    path('',views.index,name='index'),
    path('login/',views.login,name='login')
]
```

## 应用命名空间
在多个app之间，有可能产生同名的url。这时候为了避免反转url的时候产生混淆，可以使用应用命名空间，来做区分。定义应用命名空间非常简单，只要在`app`的`urls.py`中定义一个叫做`app_name`的变量，来指定这个应用的命名空间即可。示例代码如下：
```python
# 应用命名空间
app_name = 'front'

urlpatterns = [
    path('',views.index,name='index'),
    path('login/',views.login,name='login')
]
```
以后在做反转的时候就可以使用`应用命名空间:url名称`的方式进行反转。示例代码如下：
```python
login_url = reverse('front:login')
```

## 应用(app)命名空间和实例命名空间：
一个app，可以创建多个实例。可以使用多个url映射同一个app。所以这就会产生一个问题。以后在做反转的时候，如果使用应用命名空间，那么就会发生混淆。为了避免这个问题。我们可以使用实例命名空间。实例命名空间也是非常简单，只要在`include`函数中传递一个`namespace`变量即可。示例代码如下：
```python
urlpatterns = [
    path('',include('front.urls')),
    # 同一个app下有两个实例
    path('cms1/',include('cms.urls',namespace='cms1')),
    path('cms2/',include('cms.urls',namespace='cms2')),
]
```
以后在做反转的时候，就可以根据实例命名空间来指定具体的url。示例代码如下：
```python
def index(request):
    username = request.GET.get("username")
    if username:
        return HttpResponse('CMS首页')
    else:
        # 获取当前的命名空间
        current_namespace = request.resolver_match.namespace
        return redirect(reverse("%s:login"%current_namespace))
```



# url reverse
1. 如果在反转url的时候，需要添加参数，那么可以传递`kwargs`参数到`revers`函数中。示例代码如下：
    ```python
    detail_url = reverse('detail',kwargs={"article_id":1,'page':2})
    ```
2. 如果想要添加查询字符串的参数，则必须手动的进行拼接。示例代码如下：
    ```python
    login_url = reverse('login') + "?next=/"
    ```
   
# 自定义URL（PATH）转换器

需求:  
实现一个获取文章列表的demo，用户可以根据`/articles/文章分类/`的方式来获取文章。其中文章分类采用的是`分类1+分类2+分类3...`的方式拼接的，并且如果只有一个分类，那就不需要加号。示例如下：
```
# 1. 第一种：获取python分类下的文章
/articles/python/
# 2. 第二种：获取python和django分类下的文章
/articles/python+django/
# 3. 第三种：获取python和django和flask分类下的文章
/articles/python+django+flask/
以此类推...
```

在“文章分类”参数传到视图函数之前要把这些分类分开来存储到列表中。
比如参数是`python+django`，那么传到视图函数的时候就要变成`['python','django']`。

以后在使用reverse反转的时候，限制传递“文章分类”的参数应该是一个列表，并且要将这个列表变成`python+django`的形式。


## 自定义URL转换器
之前已经学到过一些django内置的url转换器，包括有int、uuid等。有时候这些内置的url转换器并不能满足我们的需求，因此django给我们提供了一个接口可以让我们自己定义自己的url转换器。

自定义url转换器按照以下五个步骤来走就可以了： 
1. 定义一个类，直接继承自object就可以了。 
2. 在类中定义一个属性regex，这个属性是用来限制url转换器规则的正则表达式。 
3. 实现to_python(self,value)方法，这个方法是将url中的值转换一下，然后传给视图函数的。 
4. 实现to_url(self,value)方法，这个方法是在做url反转的时候，将传进来的参数转换后拼接成一个正确的url。 5. 将定义好的转换器，使用`django.urls.converters.register_converter`方法注册到django中。

示例代码如下：
```python
from django.urls import register_converter

class CategoryConverter(object):
    regex = r'\w+|(\w+\+\w+)+'

    def to_python(self,value):
        # python+django+flask
        # ['python','django','flask']
        result = value.split("+")
        return result

    def to_url(self,value):
        # value：['python','django','flask']
        # python+django+flask
        if isinstance(value,list):
            result = "+".join(value)
            return result
        else:
            raise RuntimeError("转换url的时候，分类参数必须为列表！")

register_converter(CategoryConverter,'cate')
```

## 图片等静态资源无法访问
```
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```


