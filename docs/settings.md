# settings
## 设置语言／时区
```
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_TZ = False
```

## 设置后台名称
在admin.py模块中增加如下配置
```
# 登录界面看到的文字
admin.site.site_header = '测试工作台'
# 后台标题
admin.site.site_title = '测试工作台后台管理'
```
