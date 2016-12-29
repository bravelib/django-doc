### 创建一个管理员用户

~~~
python manage.py createsuperuser
~~~

### 启动开发服务器

### 用管理员登录Django系统

### 让polls应用在管理站点中可编辑

polls/admin.py

~~~
from django.contrib import admin

from .models import Question

class QuestionField(admin.ModelAdmin):
	#fields 表示字段排序
    fields = ['pub_date', 'question_text']
    #fieldsets其中第一个字段表示标题
    #classes表示字段的显示样式
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'],'classes': ['collapse']}),
    ]

admin.site.register(Question, QuestionField)
~~~