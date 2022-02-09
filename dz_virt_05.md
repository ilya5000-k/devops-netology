
## Задача 1

Дайте письменые ответы на следующие вопросы:

- В чём отличие режимов работы сервисов в Docker Swarm кластере: replication и global?
```
Для режима replication указывается, сколько идентичных задач нужно запустить. 
Например, вы решили развернуть сервис HTTP с тремя репликами, каждая из которых
обслуживает один и тот же контент.

global — это режим, который запускает одну задачу на каждой ноде. 
Предварительно заданного количества задач нет. Каждый раз, когда 
вы добавляете ноду в swarm, оркестратор создает задачу, а планировщик
назначает задачу новой ноде. По официальной документации, хорошими 
кандидатами на роль глобальных сервисов являются агенты мониторинга, 
антивирусные сканеры или другие типы контейнеров, которые вы хотите 
запустить на каждой ноде в swarm.

```
- Какой алгоритм выбора лидера используется в Docker Swarm кластере?
```
Raft — алгоритм для решения задач консенсуса в сети ненадёжных вычислений. Raft разрабатывался с учётом 
недостатков более старого алгоритма Паксос. При выборе ключевых идей, предпочтение отдавалось более простым
и практичным решениям. Тем не менее, несмотря на относительную простоту, Raft обеспечивает безопасную
и эффективную реализацию машины состояний поверх кластерной вычислительной системы. Существует множество 
реализаций Raft с открытым исходным кодом на разных языках программирования.

```

- Что такое Overlay Network?
```
Оверлейная сеть (от англ. Overlay Network) — общий случай логической сети, создаваемой поверх другой сети. 
Узлы оверлейной сети могут быть связаны либо физическим соединением, либо логическим, для которого в основной
сети существуют один или несколько соответствующих маршрутов из физических соединений. Примерами оверлеев 
являются сети VPN и одноранговые сети, которые работают на основе интернета и представляют собой «надстройки» 
над классическими сетевыми протоколами, предоставляя широкие возможности, изначально не предусмотренные
разработчиками основных протоколов. Коммутируемый доступ в интернет фактически осуществляется через оверлей 
(например, по протоколу PPP), который работает «поверх» обычной телефонной сети.

```

## Задача 2

Создать ваш первый Docker Swarm кластер в Яндекс.Облаке

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:

```
docker node ls
```
```
[root@node01 ~]# docker node ls
ID                            HOSTNAME             STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
qhr0bjvauqotfr02ghwmtksbf *   node01.netology.yc   Ready     Active         Leader           20.10.12
69zy9l1qv985i2gebfna6r9as     node02.netology.yc   Ready     Active         Reachable        20.10.12
fb2tuwrn8w63vp6145b3ao1ge     node03.netology.yc   Ready     Active         Reachable        20.10.12
lj70mzaybx5koq8ww85lwxgeu     node04.netology.yc   Ready     Active                          20.10.12
d4wwpwo8t35k8n3ufnuf3txkr     node05.netology.yc   Ready     Active                          20.10.12
n8fl0fmb7wqo2ela06yi5pc90     node06.netology.yc   Ready     Active                          20.10.12
[root@node01 ~]#


```



## Задача 3

Создать ваш первый, готовый к боевой эксплуатации кластер мониторинга, состоящий из стека микросервисов.

Для получения зачета, вам необходимо предоставить скриншот из терминала (консоли), с выводом команды:

```
docker service ls
```

```
[root@node01 ~]# docker service ls
ID             NAME                                MODE         REPLICAS   IMAGE                                          PORTS
te8m6hfehrew   swarm_monitoring_alertmanager       replicated   1/1        stefanprodan/swarmprom-alertmanager:v0.14.0
4elfdr87jh6i   swarm_monitoring_caddy              replicated   1/1        stefanprodan/caddy:latest                      *:3000->3000/tcp, *:9090->9090/tcp, *:9093-9094->9093-9094/tcp
hdygrh3m38zz   swarm_monitoring_cadvisor           global       6/6        google/cadvisor:latest
uxxqhi5iys1x   swarm_monitoring_dockerd-exporter   global       6/6        stefanprodan/caddy:latest
xblg0ez08ae5   swarm_monitoring_grafana            replicated   1/1        stefanprodan/swarmprom-grafana:5.3.4
yfx2g70c3s77   swarm_monitoring_node-exporter      global       6/6        stefanprodan/swarmprom-node-exporter:v0.16.0
8gyywzj6tq6y   swarm_monitoring_prometheus         replicated   1/1        stefanprodan/swarmprom-prometheus:v2.5.0
1n3bk0vlo18x   swarm_monitoring_unsee              replicated   1/1        cloudflare/unsee:v0.8.0
[root@node01 ~]#


```


