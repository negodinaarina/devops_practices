# Задание для Лабораторной 5
Сделать мониторинг сервиса, поднятого в кубере (использовать, например, prometheus и grafana). Показать хотя бы два рабочих графика, которые будут отражать состояние системы. Приложить скриншоты всего процесса настройки.

# Выполнение

## Настройка Grafana

Для начала лабораторной работы нужно установить grafana, для этого будем следовать официальной документации (https://grafana.com/docs/grafana/latest/setup-grafana/installation/helm/), по ней сначала добавить репозиторий grafana в helm на свою машину с помощью команды `helm repo add grafana`:

![alt text](image-2.png)

Потом запускаем миникуб и создаем пространство имен monitoring с помощью команды `kubectl create namespace monitoring`, в котором и будем работать:

![alt text](image-3.png)

Теперь, следуя документации, проверяем, насколько успешно установился репозиторий grafana с помощью команды `helm search repo` и, убедившись в успешности, с помощью `helm install` запускаем grafana: 
![alt text](image-4.png)

С помощью `helm list` проверяем, что в созданном пространстве имен успешность создания grafana, и делаем то же самое с `kubectl get all -n monitoring`, видим, что все хорошо:

![alt text](image-5.png)

Дальше вспоминаем, что не посмотрели пароль от дефолтного пользователся admin с помощью команды, которая привелась при запусске grafana в NOTES:

![alt text](image-6.png)

Далее после проброса портов с помощью команды `kubectl --namespace monitoring port-forward $POD_NAME 3000`, которую также подсказала grafana, можем получить доступ к интерфейсу grafana через браузер:  

![alt text](image-7.png)

## Настройка Prometheus

Теперь аналогично копируем в helm репозиторий prometheus через `helm repo add` и устанавливаем с помощью `helm install`(В последней строке скриншота можно увидеть адрес, по которому prometheus доступен внутри кластера, но к сожалению, я не люблю читать и не читаю, что выводят мне в подсказках, что добавит головной боли и путаницы в работе):

![alt text](image-10.png)

Проверяем, что все запустилось с помощью `kubectl get`:

![alt text](image-12.png)

Теперь я решила получить адрес сервиса prometheus аналогично тому, как я получала адрес сервиса postgresql в Лабораторной 3, через `minikube service <название сервиса>`:

![alt text](image-13.png)

И вбила полученный адрес при настройке Data Source для последующего создания дашбордов для мониторинга:

![alt text](image-14.png)

И получила ошибку:

![alt text](image-15.png)

По совету интернетов пробросила порты для prometheus аналогично тому, как уже делалось в данной лабораторной для grafana:

![alt text](image-16.png)

В браузере доступ к prometheus есть, но подключить его к grafana все еще не выходило:

![alt text](image-17.png)

Продолжая изучение интернетов понимаем, что и postgres не настроен для мониторинга, так как в конфигурации, написанной в Лабораторной 3*, нет следующего:
```
metrics:
  enabled: true
```

Добавив это в values.yml, обновляем с помощью `helm upgrade`:

![alt text](image-18.png)

А далее, перечитав все предыдущие шаги, я попробовала подключить grafana к prometheus через адрес my-prometheus-server.default.svc.cluster.local, о котором писалав отчете выше: 

![alt text](image-20.png)

И таким образом все получилось, при этом проброс портов для prometheus здесь не нужен:

![alt text](image-19.png)

Далее по туториалам из интернетов делаем дашборты для мониторинга БД, а именно для сбора статистики текущих сессий, транзакций, операция CRUD:

![alt text](image-21.png)