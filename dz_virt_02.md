## Задача 1

- Опишите своими словами основные преимущества применения на практике IaaC паттернов.
- Какой из принципов IaaC является основополагающим?

```
Модель «Инфраструктура как код (IaC)», которую иногда называют "программируемой 
инфраструктурой", — это модель, по которой процесс настройки инфраструктуры 
аналогичен процессу программирования ПО. По сути, она положила начало устранению 
границ между написанием приложений и созданием сред для этих приложений. 
Приложения могут содержать скрипты, которые создают свои собственные виртуальные 
машины и управляют ими. ЭтоИнфраструктура как код  позволяет управлять виртуальными 
машинами на программном уровне, что исключает необходимость ручной настройки и 
обновлений для отдельных компонентов оборудования. Инфраструктура становится 
чрезвычайно "эластичной», то есть воспроизводимой и масштабируемой. Одни оператор 
может выполнять развертывание и управление как одной, так и 1000 машинами, используя
 один и тот же набор кода. Среди гарантированных преимуществ инфраструктуры как 
кода — скорость, экономичность и уменьшение риска. основа облачных вычислений 
и неотъемлемая часть DevOps.


Основополагающим принципом применения IaaC является Идемпоте?нтность (лат.idem — тот же самый + potens
 — способный)—это свойство объекта или операции, при повторном выполнениикоторой мы
 получаем результат идентичный предыдущему и всем последующим выполнениям.






```


## Задача 2

- Чем Ansible выгодно отличается от других систем управление конфигурациями?
- Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?


```
1. Преимущества Ansible:
2. Легкость в изучении
3. Написан на Python
4. Не нужно ставить клиента (агента) на машинутолько SSH подключение. 
Как следствие, упрощение обслуживания и траблшутинга.
5. YAML плейбуки
6. Портал Ansible Galaxy.Это объединение Ansible сообщества, где люди делятся
 наработками и решениями той или иной задачи. Множество плейбуков, фреймворков, 
дистрибутивов и сопутствующего ПО. 

Push метод работы систем конфигурации более надёжный? т. к. более контролируемый.






```


## Задача 3

Установить на личный компьютер:

- VirtualBox
- Vagrant
- Ansible

*Приложить вывод команд установленных версий каждой из программ, оформленный в markdown.*


```
apt list virtualbox -a
Вывод списка… Готово
virtualbox/bionic-updates,now 5.2.42-dfsg-0~ubuntu1.18.04.1 amd64 [установлен]
virtualbox/bionic-security 5.2.18-dfsg-2~ubuntu18.04.5 amd64
virtualbox/bionic 5.2.10-dfsg-6 amd64



apt list vagrant
Вывод списка… Готово
vagrant/bionic,bionic,now 2.0.2+dfsg-2ubuntu8 all [установлен]

apt list ansible -a
Вывод списка… Готово
ansible/bionic-updates,bionic-updates,bionic-security,bionic-security,now 2.5.1+dfsg-1ubuntu0.1 all [установлен]
ansible/bionic,bionic 2.5.1+dfsg-1 all





```



## Задача 4 (*)

Воспроизвести практическую часть лекции самостоятельно.

- Создать виртуальную машину.
- Зайти внутрь ВМ, убедиться, что Docker установлен с помощью команды

```

vagrant provision
==> server1.netology: Running provisioner: ansible...
    server1.netology: Running ansible-playbook...
 
PLAY [nodes] *******************************************************************
 
TASK [Gathering Facts] *********************************************************
ok: [server1.netology]
 
TASK [Create directory for ssh-keys] *******************************************
changed: [server1.netology]
 
TASK [Adding Adding rsa-key in /root/.ssh/authorized_keys] *********************
changed: [server1.netology]
 
TASK [Checking DNS] ************************************************************
changed: [server1.netology]
 
TASK [Installing tools] ********************************************************
ok: [server1.netology] => (item=git)
ok: [server1.netology] => (item=curl)
 
TASK [Installing docker] *******************************************************
changed: [server1.netology]
 
TASK [Add the current user to docker group] ************************************
changed: [server1.netology]
 
PLAY RECAP *********************************************************************
server1.netology           : ok=7    changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

```

 
vagrant ssh
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)
 
 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
 
  System information as of Mon 24 Jan 2022 05:11:30 AM UTC
 
  System load:  0.18              Users logged in:          0
  Usage of /:   3.3% of 61.31GB   IPv4 address for docker0: 172.17.0.1
  Memory usage: 20%               IPv4 address for eth0:    10.0.2.15
  Swap usage:   0%                IPv4 address for eth1:    192.168.192.200
  Processes:    105
 
This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Mon Jan 24 05:09:50 2022 from 10.0.2.2
vagrant@server1:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
 
 

```
