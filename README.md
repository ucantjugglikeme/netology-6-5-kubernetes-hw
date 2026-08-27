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

Файл deployment-redis.yaml:

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

Файл service-redis.yaml:

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


---

### Задание 4

1. Создадим ConfigMap, в котором укажем конфигурацию в файле default.conf.
2. Создадим Deployment, в котором укажем образ nginx:alpine, укажем пути монтирования и свяжем том с ConfigMap.
3. Создадим Service для Deployment nginx.
4. Создадим Ingress для направления запросов на /test nginx.
5. Запустим Deployment и Ingress и проверим работу nginx.

Файл deployment-nginx.yaml:

```YAML
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-conf
data:
  default.conf: |
    server {
        listen       80;
        listen  [::]:80;
        server_name  localhost;

        location /test {
            add_header Content-Type text/plain;
            return 200 'Hello from k8s';
        }
    }
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.31.4-alpine3.24
        ports:
        - containerPort: 80
        volumeMounts:
        - name: config-volume
          mountPath: /etc/nginx/conf.d/default.conf
          subPath: default.conf
      volumes:
      - name: config-volume
        configMap:
          name: nginx-conf
```

Файл ingress-nginx.yaml:

```YAML
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    kubernetes.io/ingress.class: "traefik"
spec:
  rules:
  - http:
      paths:
      - path: /test
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

![Создание ConfigMap и Deployment nginx](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img10.png)

![Создание Service и Ingress и проверка работы nginx](https://github.com/ucantjugglikeme/netology-6-5-kubernetes-hw/blob/main/img/img11.png)
