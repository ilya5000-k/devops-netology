## Задача 1

Сценарий выполения задачи:

- создайте свой репозиторий на https://hub.docker.com;
- выберете любой образ, который содержит веб-сервер Nginx;
- создайте свой fork образа;
- реализуйте функциональность:
запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на 

https://hub.docker.com/username_repo.

```
https://hub.docker.com/u/ilya5000k/nginx_my
```
```

root@server1:~/docker/src/build/nginx# docker images
REPOSITORY           TAG       IMAGE ID       CREATED             SIZE
ilya5000k/nginx_my   1.21.my   b59616dafd1f   About an hour ago   163MB
ilya5000k/nginx      1.21.my   c2c9bff94235   2 hours ago         163MB
nginx                1.21      c316d5a335a5   5 days ago          142MB
nginx                latest    c316d5a335a5   5 days ago          142MB

docker run -d -p 5900:80 ilya5000k/nginx_my:1.21.my
```
```
root@server1:~/data# curl 127.0.0.1:5900
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>root@server1:~/data#

```

## Задача 2

Посмотрите на сценарий ниже и ответьте на вопрос:
"Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная 

машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

- Высоконагруженное монолитное java веб-приложение;
 ```
 физическая машина , тк. требуется единовременно много ресурсов
 ```
- Nodejs веб-приложение; 
```
 контейнер, тк. не требуется много ресурсов, можно разделить по разным точкам мира.
```
- Мобильное приложение c версиями для Android и iOS;
 ```
 виртуальная машина, тк. у контейнера нет графического интерфейса.
```
- Шина данных на базе Apache Kafka;
 ```
 контейнер, тк. требуется быстрое наращивание мощности.
```
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды 
elasticsearch, два logstash и две ноды kibana;
```
виртуальная машина или контейнеры, тк. нет нагрузки. 
```

- Мониторинг-стек на базе Prometheus и Grafana;
 ```
 контейнер, тк. не требуется много ресурсов много ресурсов и есть возможность 
масштабирования.
```

- MongoDB, как основное хранилище данных для java-приложения;
 ```
 виртуальная или физическая машина , тк. требуется единовременно много ресурсов
```
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.
```
виртуальная или физическая машина , тк. требуется высокая доступность.
````
```
Для каждой задачи можно использовать разные варианты. Дело вкуса и денег...
```

## Задача 3

- Запустите первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив 

папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
```
docker run -d -it -v /root/data:/var/data centos bash
```

- Запустите второй контейнер из образа ***debian*** в фоновом режиме, подключив папку 

```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера;
```
docker run -d -it -v /root/data:/var/data debian bash
```

- Подключитесь к первому контейнеру с помощью ```docker exec``` и создайте текстовый файл 

любого содержания в ```/data```;
```
root@server1:~/docker/src/build/z3# docker exec -it 9e7d3dc9c87b bash
[root@9e7d3dc9c87b /]# echo www>/var/data/temp.txt
[root@9e7d3dc9c87b /]# cd  /var/data
[root@9e7d3dc9c87b data]# ls
temp.txt
```

- Добавьте еще один файл в папку ```/data``` на хостовой машине;
```
root@server1:~/data# echo qqq>temp1.txt
```


- Подключитесь во второй контейнер и отобразите листинг и содержание файлов в ```/data``` 
контейнера.
```
root@server1:~/data# docker exec -it 9d436e2ca185 bash
root@9d436e2ca185:/# ls /var/data
temp.txt  temp1.txt
root@9d436e2ca185:/# cat /var/data/temp.txt && cat /var/data/temp1.txt
www
qqq
```


## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

Соберите Docker образ с Ansible, загрузите на Docker Hub и пришлите ссылку вместе с 

остальными ответами к задачам.




```
docker build -t ilya5000k/ansible:2.9.24 .
Sending build context to Docker daemon   2.56kB
Step 1/5 : FROM alpine:3.14

Executing busybox-1.33.1-r6.trigger
OK: 98 MiB in 69 packages
Removing intermediate container cb19aae63016
 ---> 45648a42a0a9
Step 3/5 : RUN mkdir /ansible &&     mkdir -p /etc/ansible &&     echo 'localhost' > 

/etc/ansible/hosts
 ---> Running in 3343a682c78b
Removing intermediate container 3343a682c78b
 ---> 5f626dd1a617
Step 4/5 : WORKDIR /ansible
 ---> Running in 115aac5aaf60
Removing intermediate container 115aac5aaf60
 ---> 09ba09509201
Step 5/5 : CMD [ "ansible-playbook", "--version" ]
 ---> Running in adc3d24421d1
Removing intermediate container adc3d24421d1
 ---> df3463385dfe
Successfully built df3463385dfe
Successfully tagged ilya5000k/ansible:2.9.24

root@server1:~/docker/src/build/ansible# docker images
REPOSITORY          TAG       IMAGE ID       CREATED         SIZE
ilya5000k/ansible   2.9.24    df3463385dfe   3 minutes ago   230MB
alpine              3.14      0a97eee8041e   2 months ago    5.61MB

root@server1:~/docker/src/build/ansible# docker push ilya5000k/ansible:2.9.24
The push refers to repository [docker.io/ilya5000k/ansible]
0bfb8cba4d91: Pushed
836fbb9a1848: Pushed
1a058d5342cc: Mounted from library/alpine
2.9.24: digest: sha256:fa46ab65f1af22a053b503c769a07dedf93d39cce028ed256273ac832d31bea4 size: 

947
```

```
https://hub.docker.com/r/ilya5000k/ansible/tags
```
