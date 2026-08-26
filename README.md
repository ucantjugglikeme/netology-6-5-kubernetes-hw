# Домашнее задание к занятию "Kubernetes" - Васин Станислав


### Задание 1

1. Скачаем и установим k3s.
2. Проверим работу кластера с помощью `kubectl get nodes`.
3. Немного настроим переменные окружения и добавим автодополнение, чтобы k3s и kubectl корректно работали.

![Установка k3s и проверка работы кластера](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img1.png)

![Настройка переменной окружения "KUBECONFIG" и добавление автодополнения](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img2.png)


---

### Задание 2

1. Создадим файл deployment-redis.yaml и поменяем образ на bitnamilegacy/redis:6.0.13, т.к. к сожалению эта версия уже не поддерживается.
2. В env name меняем на ALLOW_EMPTY_PASSWORD, а value на yes.
3. Создадим файл service-redis.yaml для Delpoyment.
4. Применим манифесты и проверим работу Deployment и Service.

Файл deployment-redis.yaml

```YAML
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  selector:
    matchLabels:
      app: redis
  replicas: 1
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: master
        image: bitnamilegacy/redis:6.0.13
        env:
          - name: ALLOW_EMPTY_PASSWORD
            value: "yes"
        ports:
        - containerPort: 6379
```

Файл service-redis.yaml

```YAML
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  type: ClusterIP
  selector:
    app: redis
  ports:
    - protocol: TCP
      port: 6379
      targetPort: 6379
```

![Применение манифестов и проверка работы](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img3.png)


---

### Задание 3

1. Проверим статус пода, войдем в под и выполним команду `ps aux`.
2. Пользуемся командой `kubectl logs redis-6dd58cd6f4-p6p65 --since=15m` для вывода логов (за 15 мин, т.к. за 5 мин не было логов).
3. Удалим под с помощью команды `kubectl delete pod redis-6dd58cd6f4-p6p65`.
4. Поскольку у нас работает ReplicaSet внутри Deployment, должен создаться ещё один под.
5. Узнаем название нового пода и пробросим порт с помощью `kubectl port-forward pod/redis-6dd58cd6f4-6rlvc 6379:6379`.
6. Можно использовать команду `kubectl port-forward svc/redis-service 6379:6379` для проброса через Service.
7. Проверим подключение к Redis.

![Проверка статуса пода redis](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img4.png)

![Проверка статуса пода redis](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img5.png)

![Результат выполнения "ps aux" в поде redis](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img6.png)

![Вывод логов](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img7.png)

![Удаление пода и проброс порта для нового пода](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img8.png)

![Подключение к Redis](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img9.png)
