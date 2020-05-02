# FAQ
## CSS、JS 404
在`settings`配置中增加
```
STATIC_URL = '/static/'
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
```


## django如何在用户登录后返回到原来来页面，并保持登陆状态?
html
```
<form class="form-inline">
    {% if request.user.is_authenticated %}
    <button class="btn btn-outline-success" type="submit" ><a href="{% url 'common:login' %}">{{ user }}</a></button>
    <button class="btn btn-outline-success" type="submit">退出</button>
    {% else %}
    <button class="btn btn-outline-success" type="submit" ><a href="{% url 'common:login' %}">登录</a></button>
    <button class="btn btn-outline-success" type="submit">注册</button>
    {% endif %}
</form>
```
views.py
```
from django.shortcuts import render, redirect, reverse
from django.contrib.auth import authenticate, login, logout
def login_view(request):
    if request.method == 'GET':
        return render(request, 'common/usermanage/login.html', locals())
    elif request.method == 'POST':
        account = request.POST.get('account')
        password = request.POST.get('password')
        if account is None or password is None:
            print(1)
            return render(request, 'common/usermanage/login.html', {"errormsg": "用户名或密码错误"})
        else:
            user = authenticate(username=account, password=password)
            if user is not None:
                print(2)
                login(request, user=user)
                return redirect('common:index')
                # return render(request, 'common/index.html', {'account': account})
            else:
                print(3)
                return render(request, 'common/usermanage/login.html', {"errormsg": "用户名或密码错误"})
```


