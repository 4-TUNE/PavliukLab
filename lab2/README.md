# **Лабораторна робота №2**
---
## Послідовність виконання лабораторної роботи:
#### 1. Створюю папку ***Lab_2*** в якій ствоюю ***README.md***.
#### 2. За допомогою пакетного менеджера ***PIP*** інсталював ***pipenv*** та створив ізольоване середовище для ***Python***. Використовуючи команди:
```text
sudo pip install pipenv
sudo pipenv --python 3.6
sudo pipenv shell
```
Ознайомився з командаю `pipenv -h` і виконав її.
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab2$ pipenv -h
Usage: pipenv [OPTIONS] COMMAND [ARGS]...
Options:
  --where          Output project home information.
  --venv           Output virtualenv information.
  --py             Output Python interpreter information.
  --envs           Output Environment Variable options.
  --rm             Remove the virtualenv.
  --bare           Minimal output.
  --completion     Output completion (to be eval'd).
  --man            Display manpage.
  --three / --two  Use Python 3/2 when creating virtualenv.
  --python TEXT    Specify which version of Python virtualenv should use.
  --site-packages  Enable site-packages for the virtualenv.
  --version        Show the version and exit.
  -h, --help       Show this message and exit.
Usage Examples:
   Create a new project using Python 3.6, specifically:
   $ pipenv --python 3.6
   Install all dependencies for a project (including dev):
   $ pipenv install --dev
   Create a lockfile containing pre-releases:
   $ pipenv lock --pre
   Show a graph of your installed dependencies:
   $ pipenv graph
   Check your installed dependencies for security vulnerabilities:
   $ pipenv check
   Install a local setup.py into your virtual environment/Pipfile:
   $ pipenv install -e .
   Use a lower-level pip command:
   $ pipenv run pip freeze
Commands:
  check      Checks for security vulnerabilities and against PEP 508 markers
             provided in Pipfile.
  clean      Uninstalls all packages not specified in Pipfile.lock.
  graph      Displays currently–installed dependency graph information.
  install    Installs provided packages and adds them to Pipfile, or (if none
             is given), installs all packages.
  lock       Generates Pipfile.lock.
  open       View a given module in your editor.
  run        Spawns a command installed into the virtualenv.
  shell      Spawns a shell within the virtualenv.
  sync       Installs all packages specified in Pipfile.lock.
  uninstall  Un-installs a provided package and removes it from Pipfile.
  update     Runs lock, then sync. 
```
#### 3. Встановив бібліотеку ***requests*** в моєму середовищі. Ця бібліотека дозволяє створювати HTTP запити до заданих Web сторінок. А також встановив бібліотеку ***ntplib*** яка працює з часом.
Використав команди:
```text
pipenv install requests
pipenv install ntplib
```
#### 4. Створив ***app.py*** файл. Скопіював код програми із репозиторію викладача до себе. Для кращого розуміння програми ознайомився з ***Python tutorial***.
#### 5. Запускаю програму за допомогою команди `sudo python app.py`. 
Результат виконання:
```text
((lab2) ) root@roma2-VirtualBox:~/tpis/PavliukLab/lab2# python app.py
========================================
Результат без параметрів: 
No URL passed to function
========================================
Результат з правильною URL: 
Time is:  08:06:13 PM
Date is:  11-01-2021
success
((lab2) ) root@roma2-VirtualBox:~/tpis/PavliukLab/lab2# 
```
#### 6. Встановив бібліотеку `pytest` за допомогою команди `pipenv install pytest`. Для кращого розуміння ознайомився з документацією ***pytest***.
#### 7. Створив папку ***tests***, в якій створив файли ***tests.py*** і ***__init__.py***. Скопіював код програми із репозиторію викладача до себе. Запускаю програму за допомогою команди `pytest tests/tests.py`. 
Виконанння програми:
```text
((lab2) ) root@roma2-VirtualBox:~/tpis/PavliukLab/lab2# pytest tests/tests.py
============================= test session starts ==============================
platform linux -- Python 3.6.9, pytest-6.2.5, py-1.10.0, pluggy-1.0.0
rootdir: /home/roma2/tpis/PavliukLab/lab2
collected 5 items                                                              

tests/tests.py .....                                                     [100%]

============================== 5 passed in 0.56s =============================== 
```
#### 8. ❗ (Захист) У програмі дописав функцію яка буде перевіряти час доби AM/PM та відповідно друкувати: Доброго дня/ночі.
Код програми:
```python
def home_work(time = datetime.today().strftime("%H:%M %p")):
    messege = ""
    if "AM" in time:
    	messege = "Доброго дня"
    elif "PM" in time:
    	messege = "Доброї ночі"
    return messege
```
#### 9. ❗ (Захист) Написав тест що буде перевіряти правильність виконання моєї функції.
Код тесту:
```python
def test_home_work(self):
    self.assertEqual(home_work("07:03 PM"), "Доброї ночі")
    self.assertEqual(home_work("07:30 AM"), "Доброго дня")
```
Виконання тесту:
```text
((lab2) ) root@roma2-VirtualBox:~/tpis/PavliukLab/lab2# pytest tests/tests.py
============================= test session starts ==============================
platform linux -- Python 3.6.9, pytest-6.2.5, py-1.10.0, pluggy-1.0.0
rootdir: /home/roma2/tpis/PavliukLab/lab2
collected 5 items                                                              

tests/tests.py .....                                                     [100%]

============================== 5 passed in 0.54s ===============================
((lab2) ) root@roma2-VirtualBox:~/tpis/PavliukLab/lab2#  
```
#### 10. Перенаправляю результат виконання тестів у файл ***results.txt*** за допомогою команди `pytest tests/tests.py > results.txt`, а також додаю результат виконання програми у кінець цього ж файл за допомогою команди `python app.py >> results.txt`.
#### 11. Зробив коміт із змінами до свого репозиторію.

