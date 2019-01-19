---
title: django多库
date: 2019-01-19 14:54:39
tags:
categories:
    - python
---
#### 1. 创建项目
```
django-admin.py startproject mysite
```
#### 2. 创建两个APP
```
python manage.py startapp app01
python manage.py startapp app02
```
#### 3. 在mysite/settings.py中注册APP
```
INSTALLED_APPS = (
    'app01',
    'app02',
)
```
#### 4. 修改mysite/settings.py中数据库的配置
```python
DATABASES = {
    'default': {                             # Django默认数据库必须有，没有会报错可以为空
      'ENGINE': 'django.db.backends.mysql',  # 选用的数据库引擎
      'NAME': DB_NAME,                       # 库名
      'USER': DB_USER,                       # 用户名
      'PASSWORD': DB_PASSWORD,               # 密码
      'HOST': DB_HOST,                       # 地址
      'PORT': DB_PORT,                       # 端口
      'CONN_MAX_AGE': None,                  # 控制每个连接的最大存活时间 None为用不关闭
    },
    'db01' : {
      'ENGINE': 'django.db.backends.mysql',
      'NAME': 'db01',
      'USER': DB_USER,
      'PASSWORD': DB_PASSWORD,
      'HOST': DB_HOST,
      'PORT': DB_PORT,
      'CONN_MAX_AGE': None,
    },
    'db02' : {
      'ENGINE': 'django.db.backends.mysql',
      'NAME': 'db02',
      'USER': DB_USER,
      'PASSWORD': DB_PASSWORD,
      'HOST': DB_HOST,
      'PORT': DB_PORT,
      'CONN_MAX_AGE': None,
    }
}
```

#### 5. 设置路由和关联model
  ##### 1. 基类
  ```py
  class RouterBase(object):
      DB_NAME = None
      APP_NAME = None

      def db_for_read(self, model, **hints):
          # 对于该model用哪个数据库来读
          if model._meta.app_label == self.APP_NAME:
              return self.DB_NAME
          return None

      def db_for_write(self, model, **hints):
          # 对于该model用哪个数据库来写。
          if model._meta.app_label == self.APP_NAME:
              return self.DB_NAME
          return None

      def allow_relation(self, obj1, obj2, **hints):
          # 是否允许两个对象关联到数据库。
          if obj1._meta.app_label == self.APP_NAME or obj2._meta.app_label == self.APP_NAME:
              return True
          return None

      def allow_migrate(self, db, model):
          # 对于指定的app，是否允许对db这个数据库进行migrate
          if db == self.DB_NAME:
              return model._meta.app_label == self.APP_NAME
          elif model._meta.app_label == self.APP_NAME:
              return False
          return None

      def allow_syncdb(self, db, model):
          # 对于指定的app，是否允许对db这个数据库进行syncdb
          if db == self.DB_NAME:
              return model._meta.app_label == self.APP_NAME
          elif model._meta.app_label == self.APP_NAME:
              return False
          return None
  ```

  ##### 2. 子类
  ```py
  class RouterApp01(RouterBase):
      # app01的路由器
      DB_NAME = 'db01'
      APP_NAME = 'app01'
  class RouterApp02(RouterBase):
      # app02的路由器
      DB_NAME = 'db02'
      APP_NAME = 'app02'
  ```

  ##### 3. 修改mysite/settings.py中路由类
  ```py
  DATABASE_ROUTERS = [
      'xx.xx.RouterApp01',  # xx是对应的路由类的路径
      'xx.xx.RouterApp02',
  ]
  ```
  ##### 4. 在models.py中表类添加标签
  ```py
  class Model01(models.Model):
      class Meta:
          # 元属性不会在数据库中添加对应字段
          app_label = 'app01' # 给数据库添加app01的标签，app01的标签是和数据库db01对应
  ```

#### 6. 指定数据库迁移
```
python manage.py syncdb --database=db01
python manage.py syncdb --database=db02
```
