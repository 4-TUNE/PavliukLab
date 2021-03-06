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
#### 12. Заповнив ***Makefile*** необхідними командами (bash) для повної автоматизації процесу СІ мого проекту:
Вміст ***Makefile***:
```text
.DEFAULT_GOAL := all
all: install test run deploy
install:
	@echo " "
	@echo "--------------------------------------------"
	@echo "Installing pipenv and dependencies."
	@echo "--------------------------------------------"
	@echo " "
	sudo pip install pipenv
	sudo pipenv --python 3.6
	sudo pipenv install requests
	sudo pipenv install ntplib
	sudo pipenv install pytest
test:
	@echo " "
	@echo "--------------------------------------------"
	@echo "Start tests."
	@echo "--------------------------------------------"
	@echo " "
	sudo pipenv run pytest tests/tests.py > results.txt
run:
	@echo " "
	@echo "--------------------------------------------"
	@echo "Run Python app."
	@echo "--------------------------------------------"
	@echo " "
	sudo pipenv run python3 app.py >> results.txt
deploy:
	@echo " "
	@echo "--------------------------------------------"
	@echo "Adding and Committing results.txt to git."
	@echo "--------------------------------------------"
	@echo " "
	git add results.txt
	git commit -m "Automatic commit by MakeFile"
	git push
```
#### 13. Закомітив зміни в Makefile до власного репозиторію.
#### 14. Склонував ***git*** репозиторій на віртуальну машину Ubuntu. Перейшов у папку з  лабораторною роботою та запустив ***Makefile*** файл за допомогти команди `make add`.
Результатом виконання цієї команди є створено ізольоване середовище, виконано тести, запущено програму та закомічено файл у git репозеторій.
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab2$ make all
 
--------------------------------------------
Installing pipenv and dependencies.
--------------------------------------------
 
sudo pip install pipenv
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
WARNING: The directory '/home/roma2/.cache/pip' or its parent directory is not owned or is not writable by the current user. The cache has been disabled. Check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Requirement already satisfied: pipenv in /usr/local/lib/python2.7/dist-packages (2021.5.29)
Requirement already satisfied: pip>=18.0 in /usr/local/lib/python2.7/dist-packages (from pipenv) (20.3.4)
Requirement already satisfied: enum34; python_version < "3" in /usr/lib/python2.7/dist-packages (from pipenv) (1.1.6)
Requirement already satisfied: certifi in /usr/local/lib/python2.7/dist-packages (from pipenv) (2021.10.8)
Requirement already satisfied: setuptools>=36.2.1 in /usr/lib/python2.7/dist-packages (from pipenv) (39.0.1)
Requirement already satisfied: typing; python_version < "3.5" in /usr/local/lib/python2.7/dist-packages (from pipenv) (3.10.0.0)
Requirement already satisfied: virtualenv-clone>=0.2.5 in /usr/local/lib/python2.7/dist-packages (from pipenv) (0.5.7)
Requirement already satisfied: virtualenv in /usr/local/lib/python2.7/dist-packages (from pipenv) (20.10.0)
Requirement already satisfied: pathlib2<3,>=2.3.3; python_version < "3.4" and sys_platform != "win32" in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (2.3.6)
Requirement already satisfied: filelock<4,>=3.2 in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (3.2.1)
Requirement already satisfied: importlib-metadata>=0.12; python_version < "3.8" in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (2.1.1)
Requirement already satisfied: importlib-resources>=1.0; python_version < "3.7" in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (3.3.1)
Requirement already satisfied: backports.entry-points-selectable>=1.0.4 in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (1.1.0)
Requirement already satisfied: platformdirs<3,>=2 in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (2.0.2)
Requirement already satisfied: six<2,>=1.9.0 in /usr/lib/python2.7/dist-packages (from virtualenv->pipenv) (1.11.0)
Requirement already satisfied: distlib<1,>=0.3.1 in /usr/local/lib/python2.7/dist-packages (from virtualenv->pipenv) (0.3.3)
Requirement already satisfied: scandir; python_version < "3.5" in /usr/local/lib/python2.7/dist-packages (from pathlib2<3,>=2.3.3; python_version < "3.4" and sys_platform != "win32"->virtualenv->pipenv) (1.10.0)
Requirement already satisfied: contextlib2; python_version < "3" in /usr/local/lib/python2.7/dist-packages (from importlib-metadata>=0.12; python_version < "3.8"->virtualenv->pipenv) (0.6.0.post1)
Requirement already satisfied: zipp>=0.5 in /usr/local/lib/python2.7/dist-packages (from importlib-metadata>=0.12; python_version < "3.8"->virtualenv->pipenv) (1.2.0)
Requirement already satisfied: configparser>=3.5; python_version < "3" in /usr/local/lib/python2.7/dist-packages (from importlib-metadata>=0.12; python_version < "3.8"->virtualenv->pipenv) (4.0.2)
Requirement already satisfied: singledispatch; python_version < "3.4" in /usr/local/lib/python2.7/dist-packages (from importlib-resources>=1.0; python_version < "3.7"->virtualenv->pipenv) (3.7.0)
sudo pipenv --python 3.6
Virtualenv already exists!
Removing existing virtualenv...
Creating a virtualenv for this project...
Pipfile: /home/roma2/tpis/PavliukLab/lab2/Pipfile
Using /usr/bin/python3.6m (3.6.9) to create virtualenv...
⠹ Creating virtual environment...created virtual environment CPython3.6.9.final.0-64 in 133ms
  creator CPython3Posix(dest=/home/roma2/.local/share/virtualenvs/lab2-l4BVeHEf, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, wheel=bundle, setuptools=bundle, via=copy, app_data_dir=/home/roma2/.local/share/virtualenv)
    added seed packages: pip==21.3.1, setuptools==58.3.0, wheel==0.37.0
  activators NushellActivator,PythonActivator,FishActivator,CShellActivator,PowerShellActivator,BashActivator

✔ Successfully created virtual environment! 
Virtualenv location: /home/roma2/.local/share/virtualenvs/lab2-l4BVeHEf
sudo pipenv install requests
Installing requests...
Adding requests to Pipfile's [packages]...
✔ Installation Succeeded 
Installing dependencies from Pipfile.lock (35af5c)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 12/12 — 00:00:02
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
sudo pipenv install ntplib
Installing ntplib...
Adding ntplib to Pipfile's [packages]...
✔ Installation Succeeded 
Installing dependencies from Pipfile.lock (35af5c)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
sudo pipenv install pytest
Installing pytest...
Adding pytest to Pipfile's [packages]...
✔ Installation Succeeded 
Installing dependencies from Pipfile.lock (35af5c)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
 
--------------------------------------------
Start tests.
--------------------------------------------
 
sudo pipenv run pytest tests/tests.py > results.txt
 
--------------------------------------------
Run Python app.
--------------------------------------------
 
sudo pipenv run python3 app.py >> results.txt
 
--------------------------------------------
Adding and Committing results.txt to git.
--------------------------------------------
 
git add results.txt
git commit -m "Automatic commit by MakeFile"
[main 903fb33] Automatic commit by MakeFile
 1 file changed, 2 insertions(+), 2 deletions(-)
git push
Підрахунок об’єктів: 4, виконано.
Delta compression using up to 4 threads.
Стиснення об’єктів: 100% (4/4), виконано.
Запис об’єктів: 100% (4/4), 410 bytes | 410.00 KiB/s, виконано.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:4-TUNE/PavliukLab.git
   a7537c4..903fb33  main -> main
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab2$ 
```
