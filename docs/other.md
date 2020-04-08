# 其他技巧

## 将django项目中的某个模块单独运行

１.将以下内容插入到对应模块引用django模块代代码之前
```
import os
import django
os.environ.setdefault('DJANGO_SETTING_MODULE', 'MyDjango.settings')
django.setup()
```

2.在运行配置中，修改pycharm的配置

```
PYTHONUNBUFFERED=1;DJANGO_SETTINGS_MODULE=gzcrmworkshop.settings
```