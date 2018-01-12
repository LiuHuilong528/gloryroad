# Django
## virturalenv 虚拟环境

> pip install virtualenv #安装包    
virtualenv --no-site-packages venv #创建虚拟环境venv    
source venv/bin/activate #激活环境venv    
deactivate # 退出虚拟环境

## django + virtualenv
假设已经创建并激活了 Django 虚拟环境；

```
  pip install django==1.8.18
  python -m django --version # 查看版本号
  django-admin startproject mysite # 创建新的App
  python manage.py migrate # 项目初始化
  python manage.py runserver # 启动服务器
```
