
[官方文档](https://docs.djangoproject.com/en/2.2/ref/templates/builtins/)
# 变量
变量使用两个大括号表示`{{ variable }}`  
变量定义：　　
变量可以是由任意字母/数字和下划线(`_`)组成的子字符串，但是不能是下划线(`_`)开头  
* *变量定义不要包含标点符号

# `if`标签
if标签中判断运算符跟python中的判断运算符一样，`==,!=,<,<=,>,>=,in,not,is,not,or,and `
```
{% if condition %}
code block
{% elif %}
code block
{% else %}
code block
{% endif %}
```
在`if`表达式中还可以使用过滤器`filter`，如下：  
```
{% if messages|length >= 100 %}
   You have lots of messages today!
{% endif %}
```

# `ifequal and ifnotequal`
`{% ifequal a b %} ... {% endifequal %}` 相当于` {% if a == b %} ... {% endif %}`。
`{% ifnotequal a b %} ... {% endifnotequal %}` 相当于 `{% if a != b %} ... {% endif %}`。
但是，官方宣称在后续版本中会删除以上两个标签


# `for...in...`标签
```
{% for x in xx %}
code block
{% endfor%}
```
如果要反向遍历，则加`reversed`
```
{% for x in xx reversed %}
code block
{% endfor%}
```
遍历字典时，需要使用key、value、items
```
{% for key, value in dicts.items %}
<p>{{ key}}-{{ value}}</p>
{% endfor %}
```
`for`循环中，DTL提供了一些可用变量:  
> * for loop.counter:当前循环的下标值，以1作为起始值  
> * for loop.counter0:当前循环的下标值，以0作为起始值  
> * for loop.revcounter:当前循环的反向下标值，类同for loop.counter  
> * for loop.revcounter1:当前循环的反向下标值，类同for loop.counter0  
> * for loop.first:是否是第一次循环  
> * for loop.last:是否是最后一次循环   
> * for loop.parentloop: 如果有多个循环嵌套，那么这个属性代表的是上一级的for循环  

`for...in...empty`标签：这个标签使用跟`for...in...`是一样的，只不过是在遍历的对象如果没有元素的情况下，会执行`empty`中的内容。示例代码如下：  
在效率上它比使用if判断要快
```
<ul>
{% for athlete in athlete_list %}
    <li>{{ athlete.name }}</li>
{% empty %}
    <li>Sorry, no athletes in this list.</li>
{% endfor %}
</ul>
```
上述代码与以下代码效果一样，但是上述代码更加简洁、高效
```
<ul>
  {% if athlete_list %}
    {% for athlete in athlete_list %}
      <li>{{ athlete.name }}</li>
    {% endfor %}
  {% else %}
    <li>Sorry, no athletes in this list.</li>
  {% endif %}
</ul>
```

# `with...endwith`标签
在模版中定义变量。有时候一个变量访问的时候比较复杂，那么可以先把这个复杂的变量缓存到一个变量上，以后就可以直接使用这个变量就可以了。示例代码如下：  
```
{% with total=business.employees.count %}
    {{ total }} employee{{ total|pluralize }}
{% endwith %}
```
有两点需要强烈的注意：  
> * 在with语句中定义的变量，只能在`{%with%}{%endwith%}`中使用，不能在这个标签外面使用。  
> * 定义变量的时候，不能在等号左右两边留有空格。比如`{% with total = business.employees.count %}`是错误的。  


# `{% url %}`标签
在模版中，我们经常要写一些url，比如某个`a`标签中需要定义`href`属性。当然如果通过硬编码的方式直接将这个url写死在里面也是可以的。但是这样对于以后项目维护可能不是一件好事。因此建议使用这种反转的方式来实现，类似于`django`中的`reverse`一样。示例代码如下：
```
 <a href="{% url 'book:list' %}">图书列表页面</a>
```

如果url反转的时候需要传递参数，那么可以在后面传递。但是参数分位置参数和关键字参数。位置参数和关键字参数不能同时使用。示例代码如下：
```
# path部分
path('detail/<book_id>/',views.book_detail,name='detail')

# url反转，使用位置参数
<a href="{% url 'book:detail' 1 %}">图书详情页面</a>

# url反转，使用关键字参数
<a href="{% url 'book:detail' book_id=1 %}">图书详情页面</a>
```
     
如果想要在使用url标签反转的时候要传递查询字符串的参数，那么必须要手动在在后面添加。示例代码如下：
```
<a href="{% url 'book:detail' book_id=1 %}?page=1">图书详情页面</a>
```




