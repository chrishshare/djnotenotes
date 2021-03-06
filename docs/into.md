# 第一个项目笔记

## 创建项目
1. 通过命令行的方式：首先要进入到安装了django的虚拟环境中。然后执行命令：
    ```
    django-admin startproject [项目的名称]
    ```
    这样就可以在当前目录下创建一个项目了。
2. 通过pycharm的方式：文件->新建项目->选择django。然后指定项目所在的路径，以及Python解释器，再点击Create就可以创建项目了。

## 运行项目
1. 终端：进入到项目文件夹中，然后执行以下命令即可运行：
    ```
    python manage.py runserver
    ```
2. pycharm：直接点击右上角的绿色三角箭头按钮就可以了。**注意：用pycharm运行项目，要避免一个项目运行多次。**。在项目配置中，把“只用单一实例”那个选项勾选上，避免以上的问题。

## 改变端口号
1. 在终端：运行的时候加上一个端口号就可以了。命令为：`python manage.py runserver 9000`。
2. 在pycharm中：右上角->项目配置->port。改成你想要的端口号，重新运行。

## 让同局域网中的其他电脑访问本机的项目
1. 让项目运行到额时候，host为0.0.0.0。
    * 在终端，使用命令：`python manage.py runserver 0.0.0.0:8000`。
    * 在pycharm，右上角->项目配置->host。改成`0.0.0.0`。
2. 在`settings.py`文件中，配置`ALLOWED_HOSTS`，将本机的ip地址添加进去。示例代码如下：
    ```python
    ALLOWED_HOSTS = ['192.168.0.103']
    ```
    注意：要关闭自己电脑的防火墙才行。

## 项目结构分析
1. `manange.py`：以后和项目交互基本上都是基于这个文件。一般都是在终端输入python manage.py [子命令]。可以输入python manage.py help看下能做什么事情。除非你知道你自己在做什么，一般情况下不应该编辑这个文件。
2. `settings.py`：保存项目所有的配置信息。
3. `urls.py`：用来做url与视图函数映射的。以后来了一个请求，就会从这个文件中找到匹配的视图函数。
4. `wsig.py`：专门用来做部署的。不需要修改。


## django推荐的项目规范
按照功能或者模块进行分层，分成一个个app。所有和某个模块相关的视图都写在对应的app的views.py中，并且模型和其他的也是类似。然后django已经提供了一个比较方便创建app的命令叫做`python manage.py startapp [app的名称]`。把所有的代码写在各自的app中。


## DEBUG模式
1. 如果开启了DEBUG模式，那么以后我们修改了Django项目的代码，然后按下ctrl+s，那么Django就会自动的给我们重启项目，不需要手动重启。
2. 如果开启了DEBUG模式，那么以后Django项目中的代码出现bug了，那么在浏览器中和控制台会打印出错信息。
3. 在生产环境中，禁止开启DEBUG模式，不然有很大的安全隐患。
4. 如果将DEBUG设置为False，那么必须要设置ALLOWED_HOSTS.

## ALLOWED_HOSTS
这个变量是用来设置以后别人只能通过这个变量中的ip地址或者域名来进行访问。


## admin过滤
```
@admin.register(ProductListManage)
class ProductListManageAdmin(admin.ModelAdmin):
    list_display = ('product_code', 'product_name', 'product_price', 'product_type')

    def formfield_for_dbfield(self, db_field, request, **kwargs):
        if db_field.name == 'product_type':
            kwargs['queryset'] = DictGroupManage.objects.filter(group_id='producttype')
        if db_field.name == 'product_size':
            kwargs['queryset'] = DictGroupManage.objects.filter(group_id='productsize')
        if db_field.name == 'product_color':
            kwargs['queryset'] = DictGroupManage.objects.filter(group_id='productcolor')
        return super().formfield_for_dbfield(db_field, request, **kwargs)
```

## vuestyle绑定背景色
```
:style="{'color':cl.ccolor, 'background': cl.bgcolor, backgroundImage:'url(' + mediaUrl + cl.bgimg + ')'}"
```