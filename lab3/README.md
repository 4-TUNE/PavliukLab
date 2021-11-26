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
