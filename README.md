### Домашнее задание к занятию "5.3. Контейнеризация на примере Docker"

#### Задача 1

Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование докера? Или лучше подойдет виртуальная машина, физическая машина? Или возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
- Go-микросервис для генерации отчетов;
- Nodejs веб-приложение;
- Мобильное приложение c версиями для Android и iOS;
- База данных postgresql используемая, как кэш;
- Шина данных на базе Apache Kafka;
- Очередь для Logstash на базе Redis;
- Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
- Мониторинг-стек на базе prometheus и grafana;
- Mongodb, как основное хранилище данных для java-приложения;
- Jenkins-сервер.

***Ответ:***

`Высоконагруженное монолитное java веб-приложение;`
 - физический сервер, т.к. у нас монолитное приложение, следовательно без микросервисов - docker не подходит. А так как оно ещё и высоконагруженное, то необходим быстрый доступ к вычислительным ресурсам, без использования слоев виртуализации. 

`Go-микросервис для генерации отчетов;`
 - Doker контейнер, наверное, это классический вариант использования.
 т.к. подходит для приложений, которые не сохраняют результаты своей работы. Наше Go-программа: принимает на вход набор данных, его преобразует, возвращает результат. Ничего никуда не сохраняет.
 
`Nodejs веб-приложение;`
 - Для разработки/тестирования можно использовать Docker контейнер
 - Для продакшена лучше использовать виртуальный сервер.
 
`Мобильное приложение c версиями для Android и iOS;`
 - Виртуальная машина. Docker не подходит для приложений, предполагающих наличие полнофункционального UI. Он главным образом нацелен на изолированные контейнеры с консольными приложениями, поэтому в нашем случае не подойдёт.
 

`База данных postgresql используемая, как кэш;`
- Виртуальная машина. 
Использование Docker не рекомендуется для хранения базы данных, т.к. при прекращении работы контейнера будут навсегда потеряны все данные, хранящиеся внутри него.
Если данные не критичны и нужны в рамках сессии, то можно запустить postgresql в docker.

 
`Шина данных на базе Apache Kafka;`
 - Виртуальная машина, но если потеря данных не является критичной (тест), то можно использовать Docker контейнер. 

`Очередь для Logstash на базе Redis;`
 - Физическая машина, так как требуется высокая производительность, без затрат на виртуализацию.
  
  
`Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;`
 - elasticsearvh - виртуальная машина, т.к. это база данных, то обеспечить её отказоустойчивость.
 - logstash и kibana - можно docker, т.к. данные они не хранят. 
 
Elasticsearch - NoSQL-хранилище документов, с возможностью полнотекстового поиска
Logstash настроен на приём логов по TCP/UDP-протоколам, читает сообщения из Redis и сохраняет в Elasticsearch. Kibana предоставляет визуальный интерфейс для поиска и отображения собранных данных.

    
`Мониторинг-стек на базе prometheus и grafana;`
- Docker, т.к. сами программы не хранят данные, а только преобразуют их, то docker - это хороший вариант: можно быстро установить и пользоваться.  

`Mongodb, как основное хранилище данных для java-приложения;`
- Виртаульная машина - оптимальный вариант, есть много инструментов для администрирования: резервирование, снапшоты и т.д.

Docker не подходит для хранения ценных данных. 
Физический сервер - допустимый, но слишком затратный вариант.


`Jenkins-сервер.`
 - Docker. Можно развернуть полнофункциональный сервер Jenkins из готового образа.
 

---

#### Задача 2
 
Сценарий выполения задачи:

- создайте свой репозиторий на докерхаб;
- выберете любой образ, который содержит апач и веб-сервер;
- создайте свой форк образа;
- реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:

```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m kinda DevOps now</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на докерхаб-репо.

***Ответ:***

Создал необходимые файлы и директории
```
mkdir /home/user/netology/5.3/mydockerbuild
cd /home/user/netology/5.3/mydockerbuild
vi Dockerfile
vi index.html

$ cat Dockerfile
FROM httpd:2.4
COPY ./index.html /usr/local/apache2/htdocs/
```
```
$ cat index.html
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m kinda DevOps now</h1>
</body>
</html>
```
Взял docker image Apache и запустил его с нашим файлом `index.html`
```
docker build -t my-apache2 .
docker run -dit --name romrsch -p 8080:80 my-apache2
docker ps

CONTAINER ID   IMAGE        COMMAND              CREATED         STATUS         PORTS                                   NAMES
50bbbb8036c1   my-apache2   "httpd-foreground"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp   romrsch
user@ubuntu:~/netology/5.3/mydockerbuild$

```
создал свой образ
```
docker commit romrsch romrsch/netology5.3
sha256:a6e37597c959147affd35b6b9ba39a8119b4ed19f6b700e559f3f77af213a42a


user@ubuntu:~/netology/5.3/mydockerbuild$ docker images
REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
romrsch/netology5.3   latest    a6e37597c959   8 seconds ago    138MB
my-apache2            latest    86f9d032621d   17 minutes ago   138MB
httpd                 2.4       30287d899656   2 weeks ago      138MB
httpd                 latest    30287d899656   2 weeks ago      138MB
debian                latest    7a4951775d15   2 weeks ago      114MB
```
Залил образ на docker hub
```

docker login
Username: romrsch
Password:

Login Succeeded

user@ubuntu:~/netology/5.3/mydockerbuild$ docker push romrsch/netology5.3
Using default tag: latest
The push refers to repository [docker.io/romrsch/netology5.3]
b6305ef7b536: Pushed
82ad1577e768: Pushed
dfd488a286c9: Mounted from library/httpd
15176fdb9a61: Mounted from library/httpd
61172cb5065c: Mounted from library/httpd
9fbbeddcc4e4: Mounted from library/httpd
764055ebc9a7: Mounted from library/httpd
latest: digest: sha256:85a921e98acd276f9aa328584516eca9a2b216ed5496e0384a27b4a0bcb508f0 size: 1780
user@ubuntu:~/netology/5.3/mydockerbuild$

```
ссылка на мой образ
https://hub.docker.com/repository/docker/romrsch/netology5.3



---
#### Задача 3

- Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку info из текущей рабочей директории на хостовой машине в /share/info контейнера;
- Запустите второй контейнер из образа debian:latest в фоновом режиме, подключив папку info из текущей рабочей директории на хостовой машине в /info контейнера;
- Подключитесь к первому контейнеру с помощью exec и создайте текстовый файл любого содержания в /share/info ;
- Добавьте еще один файл в папку info на хостовой машине;
- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /info контейнера.

***Ответ:***


Первый контейнер: Centos
```
"На хосте":
user@ubuntu:~/netology/5.3$ docker pull centos

docker run -v /home/user/netology/5.3/centos/info:/share/  --name my_container local/c7-systemd

user@ubuntu:~$ docker ps
CONTAINER ID   IMAGE              COMMAND            CREATED          STATUS          PORTS     NAMES
582862f1bc5a   local/c7-systemd   "/usr/sbin/init"   28 seconds ago   Up 26 seconds             my_container

"В контейнере Centos"

user@ubuntu:~$ docker exec -it my_container /bin/bash
[root@582862f1bc5a /]#
[root@582862f1bc5a /]#

[root@582862f1bc5a /]# touch /share/myfile
[root@582862f1bc5a /]# echo CENTOS >/share/myfile
[root@582862f1bc5a /]# cat /share/myfile
CENTOS
[root@582862f1bc5a /]#

"На хосте" появился файл:
user@ubuntu:~/netology/5.3/centos$ cat info/myfile
CENTOS

user@ubuntu:~/netology/5.3/centos$ echo "HOST" > info/my_host

```
Второй контейнер Debian
```
docker pull debian
docker run -v /home/user/netology/5.3/centos/info:/share --name my_container2 debian

docker exec -it my_container2 /bin/bash

[root@69c46eacb43c:/]# ls /share
my_host  myfile
[root@69c46eacb43c:/]# cat /share/myfile
CENTOS
[root@69c46eacb43c:/]# cat /share/my_host
HOST

```
---

