### 数据库的设置
在project的settings.py文件中找到`DATABASES`项

```python
DATABASES = {
    'default': {
        # 'ENGINE': 'django.db.backends.sqlite3',
        'ENGINE'  : 'django.db.backends.mysql',
        'HOST'    : 'localhost',
        'NAME'    : 'hot_mall',
        'PASSWORD': 'root',
        'PORT'    : 3306,
        'USER'    : "root"
        # 'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

将sqlite改成mysql

### 安装默认模块

文件顶端的INSTALLED_APPS设置。它保存这个Django实例中激活的所有的Django应用的名字。

. django.contrib.admin —— 管理站点。你将在本教程的第2部分使用到它。
. django.contrib.auth —— 认证系统。
. django.contrib.contenttypes —— 用于内容类型的框架。
. django.contrib.sessions —— 会话框架。
. django.contrib.messages —— 消息框架。
. django.contrib.staticfiles —— 管理静态文件的框架。

然而上面的部分应用至少需要使用一个数据库表，因此我们需要在使用它们之前先在数据库中创建相应的表。要做到这一点，请运行以下命令：

```
python manage.py migrate
```

### 启动开发服务器
当做完以上两步时候，请运行以下命令：

```python
python manage.py runserver 0.0.0.0:8000
```

然后在浏览器中访问<a href="http://localhost:8000">http://localhost:8000</a>

### 创建应用模型

~~~
python manage.py startapp polls
~~~

文件polls/models.py

~~~python
from django.db import models


class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')


class Choice(models.Model):
    question = models.ForeignKey(Question)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
~~~

### 激活模型

project/settings.py

~~~
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'polls',
)
~~~

为新修改创建迁移文件

~~~
python manage.py makemigrations polls
~~~

你应该看到类似下面的内容：

~~~
Migrations for 'polls':
  0001_initial.py:
    - Create model Question
    - Create model Choice
    - Add field question to choice
~~~

### 预览应用迁移文件生成的sql语句

sqlmigrate命令接收迁移文件的名字并返回它们的SQL语句：

~~~
python manage.py sqlmigrate polls 0001
~~~

~~~sql
BEGIN;
CREATE TABLE "polls_choice" (
    "id" serial NOT NULL PRIMARY KEY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL
);
CREATE TABLE "polls_question" (
    "id" serial NOT NULL PRIMARY KEY,
    "question_text" varchar(200) NOT NULL,
    "pub_date" timestamp with time zone NOT NULL
);
ALTER TABLE "polls_choice" ADD COLUMN "question_id" integer NOT NULL;
ALTER TABLE "polls_choice" ALTER COLUMN "question_id" DROP DEFAULT;
CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");
ALTER TABLE "polls_choice"
  ADD CONSTRAINT "polls_choice_question_id_246c99a640fbbd72_fk_polls_question_id"
    FOREIGN KEY ("question_id")
    REFERENCES "polls_question" ("id")
    DEFERRABLE INITIALLY DEFERRED;

COMMIT;
~~~

可以运行python manage.py check
它会检查你的项目中的模型是否存在问题，而不用执行迁移或者接触数据库。

运行migrate以在你的数据库中创建模型所对应的表：

~~~
python manage.py migrate
~~~

~~~
Operations to perform:
  Synchronize unmigrated apps: staticfiles, messages
  Apply all migrations: admin, contenttypes, polls, auth, sessions
Synchronizing apps without migrations:
  Creating tables...
    Running deferred SQL...
  Installing custom SQL...
Running migrations:
  Rendering model states... DONE
~~~