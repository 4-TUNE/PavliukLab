# **Лабораторна робота №3**
---
## Послідовність виконання лабораторної роботи:
#### 1. Створив папку ***Lab3*** у власному репозиторію. Перейшовши у папку проініціалізував середовище pipenv та встановив необхідні пакети за допомогою команд:
```text
sudo pipenv --python 3.8
sudo pipenv install django
```
#### 2. За допомогою `Django Framework` створив заготовку (template) власного проекту `my_site`. Використовуючи команду `sudo pipenv run django-admin startproject my_site`. 
Для зручності виніс всі створені файли на один рівень вище щоб структура проекту була такою як показано нижче:
```text
Lab3/
├── my_site/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py
```
За допомогою команд:
```sh
sudo mv my_site/my_site/* my_site/
sudo mv my_site/manage.py ./
sudo rmdir my_site/my_site
```
#### 3. Запускаю `Django` сервер. За допомогою команди `sudo pipenv run python manage.py runserver` та перейшов за посиланням яке вивелось у консолі.
Виконання команди в консолі:
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab3$ sudo pipenv run python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
November 26, 2021 - 16:05:48
Django version 3.2.9, using settings 'my_site.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
#### 4. Після того як все запустилось успішно і стартова сторінка `Django` відображається коректно, зупинив сервер виконавши переривання `Ctrl+C`. Створив коміт із базовим темплейтом сайту. А також ознайомився із функціоналом `Django Framework`.

#### 5. Далі створив темплейт свого додатку у якому буде описано всі web сторінки мого сайту `main`. За допомогою команди `sudo pipenv run python manage.py startapp main`. Також створив коміт із новоствореними файлами темплейту додатка.

#### 6. Cтворив папку `main/templates/`, а також у даній папці файл `main.html`. Також у папці додатку створив ще один файл `main/urls.py`. Зробив коміт із даними файлами.

#### 7. Після створення додатку вказав `Django frameworks` його назву та де шукати веб сторінки. Це здійснюється у файлі `my_site/settings.py` у змінній `INSTALLED_APPS`, а також вніс зміни у файл `my_site/urls.py`.
Доданий вміст до файлу `my_site/settings.py`:
```python
# Application definition
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'main'
]
```
Доданий вміст до файлу `my_site/urls.py`:
```python
"""my_site URL Configuration
The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path, include
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('main.urls')),
]
```
#### 8. Далі переходжу до мого додатку та буду виконувати дії над WEB сторінками. Для цього: створив сторінки двох типів - перша буде зчитуватись з .html темплейта. друга сторінка буде просто повертати відповідь у форматі JSON.
Вміст файла `main/views.py`:
```py
from django.shortcuts import render
from django.http import JsonResponse
import os
from datetime import datetime
def main(request):
    return render(request, 'main.html', {'parameter': "test"})
def health(request):
    response = {'date': 'test1', 'current_page': "test2", 'server_info': "test3", 'client_info': "test4"}
    return JsonResponse(response)
```
#### 9. Щоб поєднати функції із реальними URL шляхами за якими будуть доступні наші веб сторінки заповнив файл `main/urls.py` згідно зразка. Як можна зрозуміти з коду є два URL посидання:
* головна сторінка яка буде опрацьовуватись функцією main;
* сторінка health/ яка буде опрацьована функцією health;

Доданий вміст до файлу `main/urls.py`:
```python
from django.urls import path
from . import views
urlpatterns = [
    path('', views.main, name='main'),
    path('health/', views.health, name='health')
]
```

#### 10. Запустив сервер та переконався що сторінки доступні. Виконайте коміт робочого `Django` сайту.
Виконання команди:
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab3$ sudo pipenv run python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
November 26, 2021 - 16:30:36
Django version 3.2.9, using settings 'my_site.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
[26/Nov/2021 16:31:15] "GET / HTTP/1.1" 200 158
Not Found: /favicon.ico
[26/Nov/2021 16:31:16] "GET /favicon.ico HTTP/1.1" 404 2440
```


