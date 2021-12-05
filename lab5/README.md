# **Лабораторна робота №5**
---
## Послідовність виконання лабораторної роботи:
#### 1. Для ознайомляння з `docker-compose` звернувся до документації.
Щоб встановити `docker-compose` використав команди:
```text
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
#### 2. Ознайомився з бібліотекою `Flask`, яку найчастіше використовують для створення простих веб-сайтів на Python.
#### 3. Моє завдання: за допомогою Docker автоматизувати розгортання веб сайту з усіма супутніми процесами. Зроблю я це двома методами: 
* за допомогою `Makefile`;
* за допомогою `docker-compose.yaml`.

#### 4. Першим розгляну метод з `Makefile`, але спочатку створю робочий проект.
#### 5. Створив папку `my_app` в якій буде знаходитись мій проект. Створив папку `tests` де будуть тести на перевірку працездатності мого проекту. Скопіював файли `my_app/templates/index.html`, `my_app/app.py `, `my_app/requirements.txt`, `tests/conftest.py`, `tests/requirements.txt`, `tests/test_app.py` з репозиторію викладача у відповідні папки мого репозеторію. Ознайомився із вмістом кожного з файлів. Звернув увагу на файл requirements.txt у папці проекту та тестах. Даний файл буде мітити залежності для мого проекту він містить назви бібліотек які імпортуються.
#### 6. Я спробував чи проект є працездатним перейшовши у папку `my_app` та після ініціалізації середовища виконав команди записані нижче:
```text
sudo pipenv --python 3.8
sudo pipenv install -r requirements.txt
sudo pipenv run python app.py
```
1. Так само я ініціалузував середовище для тестів у іншій вкладці шелу та запустив їх командою `sudo pipenv run pytest test_app.py --url http://localhost:5000` але спочатку треба перейти в папку `tests`:
    ```text
    roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5/tests$ sudo pipenv run pytest test_app.py --url http://localhost:5000
============================= test session starts ==============================
platform linux -- Python 3.8.0, pytest-6.2.5, py-1.11.0, pluggy-1.0.0
rootdir: /home/roma2/tpis/PavliukLab/lab5/tests
collected 4 items                                                              

test_app.py ..FF                                                         [100%]

=================================== FAILURES ===================================
__________________________________ test_logs ___________________________________

url = 'http://localhost:5000'

    def test_logs(url):
        response = requests.get(url + '/logs')
>       assert 'My Hostname is:' in response.text, 'Logs do not have Hostname'
E       AssertionError: Logs do not have Hostname
E       assert 'My Hostname is:' in '<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"\n  "http://www.w3.org/TR/html4/loose.dtd">\n<html>\n  ...en(\'logs/app.log\', \'r\') as log:\nFileNotFoundError: [Errno 2] No such file or directory: \'logs/app.log\'\n\n-->\n'
E        +  where '<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"\n  "http://www.w3.org/TR/html4/loose.dtd">\n<html>\n  ...en(\'logs/app.log\', \'r\') as log:\nFileNotFoundError: [Errno 2] No such file or directory: \'logs/app.log\'\n\n-->\n' = <Response [500]>.text

test_app.py:27: AssertionError
________________________________ test_main_page ________________________________

url = 'http://localhost:5000'

    def test_main_page(url):
        response = requests.get(url)
>       assert 'You are at main page.' in response.text, 'Main page without text'
E       AssertionError: Main page without text
E       assert 'You are at main page.' in '<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"\n  "http://www.w3.org/TR/html4/loose.dtd">\n<html>\n  ..._message(e))\nredis.exceptions.ConnectionError: Error -2 connecting to redis:6379. Name or service not known.\n\n-->\n'
E        +  where '<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"\n  "http://www.w3.org/TR/html4/loose.dtd">\n<html>\n  ..._message(e))\nredis.exceptions.ConnectionError: Error -2 connecting to redis:6379. Name or service not known.\n\n-->\n' = <Response [500]>.text

test_app.py:32: AssertionError
=========================== short test summary info ============================
FAILED test_app.py::test_logs - AssertionError: Logs do not have Hostname
FAILED test_app.py::test_main_page - AssertionError: Main page without text
========================= 2 failed, 2 passed in 0.16s ========================== 
    ```
2. Звернув увагу, що в мене автоматично створюються файли `Pipfile` та `Pipfile.lock`, а також на хост машині буде створена папка `.venv`. Після зупинки проекту видалив їх.
3. Перевірив роботу сайту перейшовши головну сторінку. Сайт не працює бо на відсутній `redis`.

#### 7. Видалив файли які постворювались після тестового запуску. Щоб моє середовище було чистим, все буде створюватись і виконуватись всередині Docker. Створив два файла `Dockerfile.app`, `Dockerfile.tests` та `Makefile` який допоможе автоматизувати процес розгортання.
#### 8. Скопіював вміст файлів `Dockerfile.app`, `Dockerfile.tests` та `Makefile` з репозиторію викладача та ознайомився із вмістом `Dockerfile` та `Makefile` та його директивами. 
Вміст файла `Dockerfile.app`:
```text
FROM python:3.8-slim
LABEL author="Roman Pavliuk"
# оновлюємо систему та встановлюємо потрібні пакети
RUN apt-get update \
    && apt-get upgrade -y\
    && apt-get install git -y\
    && pip install pipenv
WORKDIR /app
# Копіюємо файл із списком пакетів які нам потрібно інсталювати
COPY my_app/requirements.txt ./
RUN pipenv install -r requirements.txt
# Копіюємо наш додаток
COPY my_app/ ./
# Створюємо папку для логів
RUN mkdir logs
EXPOSE 5000
ENTRYPOINT pipenv run python app.py
```
Вміст файла `Dockerfile.tests`:
```text
FROM python:3.8-slim
LABEL author="Roman Pavliuk"
# оновлюємо систему та встановлюємо потрібні пакети
RUN apt-get update \
    && apt-get upgrade -y\
    && apt-get install git -y\
    && pip install pipenv
WORKDIR /tests
# Копіюємо файл із списком пакетів які нам потрібно інсталювати
COPY tests/requirements.txt ./
RUN pipenv install -r requirements.txt
# Копіюємо нашого проекту
COPY tests/ ./
ENTRYPOINT pipenv run pytest test_app.py --url http://app:5000
```
Вміст файла `Makefile`:
```text
STATES := app tests
REPO := 4tuned/lab4
.PHONY: $(STATES)
$(STATES):
	@docker build -f Dockerfile.$(@) -t $(REPO):$(@) .
run:
	@docker network create --driver=bridge appnet \
	&& docker run --rm --name redis --net=appnet -d redis \
	&& docker run --rm --name app --net=appnet -p 5000:5000 -d $(REPO):app
test-app:
	@docker run --rm -it --name test --net=appnet $(REPO):tests
	
docker-prune:
	@docker rm $$(docker ps -a -q) --force || true \
	&& docker container prune --force \
	&& docker volume prune --force \
	&& docker network prune --force \
	&& docker image prune --force
```
Дерективи `app` та `tests`:
Створення імеджів для сайту та тесту відповідно.
Деректива `run`:
Запускає сторінку сайту.
Деректива `test-app`:
Запуск тесту сторінки.
Деректива `docker-prune`:
Очищення іміджів, контейнера і інших файлів без тегів.
#### 9. Для початку, використовуючи команду `sudo make app` створіть Docker імеджі для додатку та для тестів `sudo make tests`. Теги для цих імеджів є з моїм Docker Hub репозиторієм. Запустив додаток командою `sudo make run` та перейшовши в іншу вкладку шелу запустіть тести командою `sudo make test-app`.
Запуск сайту
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo make run
fe8b378d3eba64d3325e854d9751538449687c0981f2da9de10831633be61977
Unable to find image 'redis:latest' locally
latest: Pulling from library/redis
e5ae68f74026: Pull complete 
37c4354629da: Pull complete 
b065b1b1fa0f: Pull complete 
6954d19bb2e5: Pull complete 
6333f8baaf7c: Pull complete 
f9772c8a44e7: Pull complete 
Digest: sha256:2f502d27c3e9b54295f1c591b3970340d02f8a5824402c8179dcd20d4076b796
Status: Downloaded newer image for redis:latest
a019c00c71b06c38b194b6348ede86028528d08d762c6b75192f890267cf9293
97b610d9ba35533addca008ec951dec8ae2ec7c681def24e7a1c12e398173f71
```
Проходження тесту:
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo make test-app
[sudo] пароль до roma2: 
============================= test session starts ==============================
platform linux -- Python 3.8.12, pytest-6.2.5, py-1.11.0, pluggy-1.0.0
rootdir: /tests
collected 4 items                                                              

test_app.py ....                                                         [100%]

============================== 4 passed in 0.10s ===============================
```

#### 10. Зупинив проект натиснувши Ctrl+C та почистив всі ресурси `Docker` за допомогою `make`.
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo make docker-prune
97b610d9ba35
a019c00c71b0
Total reclaimed space: 0B
Deleted Volumes:
b81ecc3221884d5fead4f388bf1e7fcbb9bd3201d8362b90dec23637db7ff959

Total reclaimed space: 0B
Deleted Networks:
appnet

Total reclaimed space: 0B
```

#### 11. Створив директиву `docker-push` в Makefile для завантаження створених імеджів у мій Docker Hub репозиторій.
Деректива `docker-push`:
```text
docker-push:
	@docker login \
	&& docker push $(REPO):app \
	&& docker push $(REPO):tests
```

#### 12. Видалив створені та закачані імеджі. Команда `docker images` виводить пусті рядки. Створив директиву в Makefile яка автоматизує процес видалення моїх імеджів.
Деректива `images-delete`:
```text
images-delete:
	@docker rmi $$(docker images -q)
```
Запуск:
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo docker images
REPOSITORY    TAG          IMAGE ID       CREATED         SIZE
4tuned/lab4   tests        c8cd7fede67e   8 minutes ago   300MB
4tuned/lab4   app          5897829e4afb   8 minutes ago   301MB
redis         latest       aea9b698d7d1   2 days ago      113MB
4tuned/lab4   monitoring   5e09b03bfc51   8 days ago      334MB
4tuned/lab4   django       b4a4303624e3   8 days ago      334MB
python        3.8-slim     64458f531a7e   2 weeks ago     122MB
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo make images-delete
Untagged: 4tuned/lab4:tests
Deleted: sha256:c8cd7fede67e0096dea7821b0c5e667a54ab0462421b32f9f5c9ba599e23a310
Deleted: sha256:e864212ada245363df2373916fcf93094ad42273a6c5e303058f8a924a4102e5
Deleted: sha256:53c0ee97cb84f3e27f9448f455b59c634b1f47e75d5c4fc3a7967ac6d6f4961a
Deleted: sha256:1401b8b1223ac8b5ebaa0a53cc1cf7d2989ac072fb6cd13a86a10fae7b7fb732
Deleted: sha256:c54ac2db0a0abe14e02c5005cec022f39b66f749298424837330e0937ab66bee
Deleted: sha256:07739c0fe4d5267a775c26cca82bbc408fed74482f2e5f9e6e125866978c3385
Deleted: sha256:cca3162c147e21be0d2cf9683579e3c70653e7aefcdb3e527651ca982ffd59f4
Deleted: sha256:3b1112f98395413682f35fa5575a71c69c44adba52f6172c0caf02333f2b46c5
Deleted: sha256:ff5bf7cd860b4af78e7f78bbdc63899c87d15415f5d2411eb4a3d5ebb74a48d4
Untagged: 4tuned/lab4:app
Deleted: sha256:5897829e4afb88bcac674fb5cb8879a89deed5b69bae198df26de4e05c0fcd78
Deleted: sha256:c3958380c1e3e54b3da5b6990d301f0be488fe0bba4769594d9630944ff19648
Deleted: sha256:7188442246d7dfb5006ab61483548f3daea788d34154f501644a104c2a4cf7c8
Deleted: sha256:24349ad7fd111f61d488fe903f56823766d530a5fc45c6d0e16e1a10d6e3fdcb
Deleted: sha256:485842c29e22fab79eae3aae16700484240d090d30fa953a1cbd93040ff2604f
Deleted: sha256:2ae7d22f2530f6d8ae5d1d6cac40f658a63e84460dff166575e9d90f344e4e12
Deleted: sha256:ea88a9c867611b2a433a31d483528689f02843875d3c94bdba3c4ee9024daa90
Deleted: sha256:44e5648335fdfafaeb2fd4b394a9216e75d2d2ae2bb6c6112eb2c9fe1f9c960e
Deleted: sha256:4202f8d0ba9aca0f28ac6f4dd257367fe11c456258991b33a86a17e2b762a03d
Deleted: sha256:07e722f453cea1deae8c65a0d4734195ac0f89305db139497202036175ef0123
Deleted: sha256:b2a0c02c3b67d1f806d8750a5c16df7db9b674a4c65dfe756ea1cb7a19c4fa03
Deleted: sha256:c5e8da9ed5b58b22db5c0dcfb76ef0e513fe961c885302336be6ef6c0cf21bd2
Deleted: sha256:64da58ab1158c5f9caa63eb90ab6de4eb4e64c97c4126c806d9b331b92a3412d
Deleted: sha256:203ef41d60deb50cc1c1676cd5dc35cd93d109cdb5743af7ba5d42766b9108a2
Deleted: sha256:6f16b65b05e8a3ef6d449b82316deb761042fb371b49f63faa9a908f9b9adc0c
Untagged: redis:latest
Untagged: redis@sha256:2f502d27c3e9b54295f1c591b3970340d02f8a5824402c8179dcd20d4076b796
Deleted: sha256:aea9b698d7d1d2fb22fe74868e27e767334b2cc629a8c6f9db8cc1747ba299fd
Deleted: sha256:beb6c508926e807f60b6a3816068ee3e2cece7654abaff731e4a26bcfebe04d8
Deleted: sha256:a5b5ed3d7c997ffd7c58cd52569d8095a7a3729412746569cdbda0dfdd228d1f
Deleted: sha256:ee76d3703ec1ab8abc11858117233a3ac8c7c5e37682f21a0c298ad0dc09a9fe
Deleted: sha256:60abc26bc7704070b2977b748ac0fd4ca94b818ed4ba1ef59ca8803e95920161
Deleted: sha256:6a2f1dcfa7455f60a810bb7c4786d62029348f64c4fcff81c48f8625cf0d995a
Deleted: sha256:9321ff862abbe8e1532076e5fdc932371eff562334ac86984a836d77dfb717f5
Untagged: 4tuned/lab4:monitoring
Untagged: 4tuned/lab4@sha256:2cb0391f54edcc820c24764e04e9482689eb47eb231968ff261b64a3d0661790
Deleted: sha256:5e09b03bfc51f4724a1a92f26b91743f343835eed1393e76e07cde5ddb09b3bf
Untagged: 4tuned/lab4:django
Untagged: 4tuned/lab4@sha256:1235609cb6ec14065208b844c8759e1ec1736ce298b1ce26fedcdc71e8cdcd8a
Deleted: sha256:b4a4303624e3f94fa8b7fa2b003badedae0ec5a16ca87af801df0efda3d199a8
Deleted: sha256:560bfad5f33fe32fc16703bbc1d44213e3635ac58eaacb98f48b9f60ed71ab8f
Deleted: sha256:243b69866e7c547f8dee2ed96bfa716874c0a20d3d91bc43542f38ae51b523a5
Deleted: sha256:b8a433fe5410d3a3f794883ab3785a6e72f95b11feebfd28f5c7fc09add8954f
Deleted: sha256:0be6e58197c4f3bdd94326fb073d4cf5024b9ff1cd44053ad8bc64307debe313
Deleted: sha256:28fff96ddd5b0d9241834edbb343ec20f6e93ccea19d0bd61e6d133af698eaef
Deleted: sha256:8f606046c4ecbb3add0c1a6d1445664a6638ebee4a02c8742c7e13b44389a82d
Deleted: sha256:a1a7e86beb0746421adf2d4b8df72b0ed4b82d922394bae8bcfceadecf966a72
Deleted: sha256:1f250254fee500b2c5c48ea0a685ac8e1c55d2c372991a23f3006fd90ea21614
Deleted: sha256:8be011c555308614fa668b037410da3efec3d03e237c0706dce7754ebcf53a42
Deleted: sha256:a4927d1a5fd1bb7d46ee6d7d4a180628563dbb56bb9ad4ae18bdba765634ac24
Deleted: sha256:31bbc9598c08db27212e4bfdffba7accc28e0878c47c9a32495891f78fed5e53
Deleted: sha256:3a904f6bc68cd6732264d13b625582a5629d7bfc510bb4f635caa21d583a2a4a
Deleted: sha256:6efe4cd6d3dc1208c31907147a8230045bee534e21940f02f4d1ae1eda070b27
Deleted: sha256:cd7cae4bce4313870e8ecd860832abe27517a7df23375121c6752b465a64e854
Deleted: sha256:912450f1fbce8ba69840f35377690cb6fd212ddbd127bc65894e2cfbda5552f9
Deleted: sha256:37a78e43d86af2be28961d106d3a493c06fe98c083c49d9a764c494666f4da82
Deleted: sha256:b98d6114f38dee6859ebe886bdba1a668439bb242477bceedf29f2582d4f5ada
Untagged: python:3.8-slim
Untagged: python@sha256:87cdfdbf66e79dfb1d33aca671f9bee623e3710b1f6e0b8476c98c669d69deec
Deleted: sha256:64458f531a7ebebc96b2ee5d0336b71437d8addc06b6e8b463c59af2048fa5ba
Deleted: sha256:f9f986658f04ce35ae016a13d6d9392acb4d62d294c9cb529b244092d418c077
Deleted: sha256:ac8bcc28c85def48851983a79715f36655f0b5bce310750bfff4d5f097a22beb
Deleted: sha256:9b1e7d4635b373da10d62e1bcc167080c654945f29bd1162fc8c4d04382c92ae
Deleted: sha256:ba89ed484769dc64767ad3d260de7b9a0554177d72e258985b2e8cdee2f083d9
Deleted: sha256:e1bbcf243d0e7387fbfe5116a485426f90d3ddeb0b1738dca4e3502b6743b325
```

#### 13. Перейшов до іншого варіанту з використанням `docker-compose.yaml`. Для цього створив даний файл у кореновій папці проекту та заповнив вмістом з прикладу. Проект який я буду розгортити за цим варіантом трохи відрізняється від першого тим що у нього зявляється дві мережі: приватна і публічна.
Файл `docker-compose.yaml`:
```text
version: '3.8'
services:
  hits:
    build:
      context: .
      dockerfile: Dockerfile.app
    image: 4tuned/lab4:compose-app
    container_name: app
    depends_on:
      - redis
    networks:
      - public
      - secret
    ports:
      - 80:5000
    volumes:
      - hits-logs:/hits/logs
  tests:
    build:
      context: .
      dockerfile: Dockerfile.tests
    image: 4tuned/lab4:compose-tests
    container_name: tests
    depends_on:
      - hits
    networks:
      - public
  redis:
    image: redis:alpine
    container_name: redis
    volumes:
      - redis-data:/data
    networks:
      - secret
volumes:
  redis-data:
    driver: local
  hits-logs:
    driver: local
networks:
  secret:
    driver: bridge
  public:
    driver: bridge
```

#### 14. Перевірив чи `Docker-compose` встановлений та працює у моїй системі, а далі просто запускаю `docker-compose`:
```text
docker-compose --version
sudo docker-compose -p lab5 up
```
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ docker-compose --version
docker-compose version 1.17.1, build unknown
```

#### 15. Перевірив чи працює веб-сайт. Дана сторінка відображається за адресою `http://http://10.0.2.15:5000/`:


#### 16. Перевірив чи компоуз створив докер імеджі. Всі теги коректні і назва репозиторія вказана коректно:
```text
roma2@roma2-VirtualBox:~/tpis/PavliukLab/lab5$ sudo docker images
[sudo] пароль до roma2: 
REPOSITORY    TAG             IMAGE ID       CREATED         SIZE
4tuned/lab4   compose-tests   2f9c1bfacd01   2 minutes ago   302MB
4tuned/lab4   compose-app     9285d23a1536   2 minutes ago   301MB
python        3.8-slim        1e46b5746c7c   2 days ago      122MB
redis         alpine          3900abf41552   5 days ago      32.4MB
```

#### 17. Зупинив проект натиснувши `Ctrl+C` і почистітив ресурси створені компоуз командою `docker-compose down`.

#### 18. Завантажив створені імеджі до Docker Hub репозиторію за допомого команди `sudo docker-compose push`.

#### 19. Що на Вашу думку краще використовувати `Makefile` чи `docker-compose.yaml`? - На мою думку `Makefile` при використанні більш інтуїтивно зрозумілий, адже можна в ньому побачити які команди запускаються, але і в одночас треба знати які команди використовувати. На рахунок `docker-compose.yaml` він менш зрозуміліший і там не показано команди які потрібно запустити а лише вказано що потрібно запусти, підклучити чи збілдити і користувача не хвилює як воно це робить. Як для мене мені обидва методи добрі.

#### 20. (Завдання) Оскільки Ви навчились створювати docker-compose.yaml у цій лабораторній то потрібно:
- Cтворив `docker-compose.yaml` для лабораторної №4. Компоуз повинен створити два імеджі для `Django` сайту та моніторингу, а також їх успішно запустити.
Файлик `docker-compose.yaml`:
```text
version: '3.8'
services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    image: 4tuned/lab4:compose-jango
    container_name: django
    networks:
      - public
    ports:
      - 8000:8000
  monitoring:
    build:
      context: .
      dockerfile: Dockerfile.site
    image: 4tuned/lab4:compose-monitoring
    container_name: monitoring
    network_mode: host
networks:
  public:
    driver: bridge
```
#### 21. Після успішного виконання роботи я відредагував свій `README.md` у цьому репозиторію та створив pull request.
