# Домашнее задание к занятию "Docker. Часть 2" - Васин Станислав


### Задание 1

Процесс установки Docker Compose изображен на скриншотах. Docker Compose нужен для запуска множества приложений, сервисов в виде связки контейнеров. Использование Docker Compose по сравнению с Docker может существенно облегчить развёртывание и управление контейнерами, поскольку описать в файле взаимодействие контейнеров удобнее, чем вручную прописывать для каждого контейнера команды. Поскольку конфигурация хранится в файле, упрощается перенос проекта между разными машинами. Кроме того, можно хранить разные версии конфигураций в проекте, что обеспечивает воспроизводимость, прозрачность, сохранность настроек и возможность отката к предыдущим версиям. Например, можно развернуть БД PostgreSQL и приложение Python + FastAPI или Nextcloud + PostgreSQL + Redis и гибко управлять конфигурациями.

![Установка пакетов ca-certificates и curl](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img1.png)

![Добавление ключа GPG Docker](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img2.png)

![Добавление репозитория в apt sources](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img3.png)

![Установка Docker вместе с Docker Compose](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img4.png)

![Установка Docker вместе с Docker Compose](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img5.png)


---

### Задание 2

1. Создадим файл docker-compose.yml.
2. Внесём первичные настройки.

```YAML
version: "3.8"

services: {}

volumes: {}

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```


---

### Задание 3

1. Создадим директорию prometheus и добавим файл prometheus.yml.
2. Добавим в файл docker-compose.yml конфигурацию для Prometheus.
3. В volumes добавим именованный том prometheus_data.
4. Запустим Prometheus и проверим его работу.

```YAML
version: "3.8"

services:
  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network

volumes:
  prometheus_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Запуск Prometheus](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img6.png)

![Проверка работы Prometheus](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img7.png)


---

### Задание 4

1. Добавим в файл docker-compose.yml конфигурацию для Pushgateway.
2. Запустим Prometheus и Pushgateway.
3. Откроем веб-морду Pushgateway.

```YAML
version: "3.8"

services:
  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network
  pushgateway:
    image: prom/pushgateway:v1.11.3
    container_name: vasin-sa-netology-pushgateway
    ports:
      - 9091:9091
    restart: unless-stopped
    networks:
      - docker2-hw-network

volumes:
  prometheus_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Запуск Prometheus и Pushgateway](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img8.png)

![Веб-морда Pushgateway](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img9.png)


---

### Задание 5

1. Создадим директорию grafana и добавим файл grafana.ini с логином и паролем.
2. Добавим в файл docker-compose.yml конфигурацию для Grafana.
3. В volumes добавим именованный том grafana_data.
4. В environment укажем путь до файла grafana.ini внутри контейнера.
5. Запустим контейнеры и откроем Grafana в браузере.
6. Войдем с логином и паролем.

```YAML
version: "3.8"

services:
  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network
  pushgateway:
    image: prom/pushgateway:v1.11.3
    container_name: vasin-sa-netology-pushgateway
    ports:
      - 9091:9091
    restart: unless-stopped
    networks:
      - docker2-hw-network
  grafana:
    image: grafana/grafana:13.2.0
    container_name: vasin-sa-netology-grafana
    ports:
      - 80:3000
    volumes:
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    environment:
      - GR_PATH_CONFIG=/etc/grafana/grafana.ini
    restart: unless-stopped
    networks:
      - docker2-hw-network

volumes:
  prometheus_data:
  grafana_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Запуск контейнеров](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img10.png)

![Grafana в браузере](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img11.png)

![Grafana в браузере](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img12.png)


---

### Задание 6

1. Добавим в конфигурации Prometheus и Pushgateway healthcheck.
2. Зададим проверку работы контейнеров с помощью утилиты wget в режиме сканера и укажем интервалы.
3. Зададим порядок запуска Pushgateway -> Prometheus -> Grafana с проверкой работы сервисов через depends_on.
4. Для всех конейнеров задан режим перезапуска во всех случаях, кроме ручной остановки.
5. Для всех контейнеров указано использование подсети vasin-sa-my-netology-hw.
6. Запустим контейнеры и проверим их статусы.

```YAML
version: "3.8"

services:
  pushgateway:
    image: prom/pushgateway:v1.11.3
    container_name: vasin-sa-netology-pushgateway
    ports:
      - 9091:9091
    restart: unless-stopped
    networks:
      - docker2-hw-network
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9091/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      pushgateway:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9090/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  grafana:
    image: grafana/grafana:13.2.0
    container_name: vasin-sa-netology-grafana
    ports:
      - 80:3000
    volumes:
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    environment:
      - GR_PATH_CONFIG=/etc/grafana/grafana.ini
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      prometheus:
        condition: service_healthy

volumes:
  prometheus_data:
  grafana_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Запуск контейнеров](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img13.png)


---

### Задание 7

1. Выполняем запрос `echo "vasin_sa 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology`.
2. Входим в Grafana с помощью логина и пароля.
3. Создаем Data Source Prometheus.
4. Создаем график на основе добавленной метрики.

```YAML
version: "3.8"

services:
  pushgateway:
    image: prom/pushgateway:v1.11.3
    container_name: vasin-sa-netology-pushgateway
    ports:
      - 9091:9091
    restart: unless-stopped
    networks:
      - docker2-hw-network
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9091/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      pushgateway:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9090/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  grafana:
    image: grafana/grafana:13.2.0
    container_name: vasin-sa-netology-grafana
    ports:
      - 80:3000
    volumes:
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    environment:
      - GR_PATH_CONFIG=/etc/grafana/grafana.ini
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      prometheus:
        condition: service_healthy

volumes:
  prometheus_data:
  grafana_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Вывод контейнеров после запуска](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img14.png)

![Скриншот графика на основе метрики](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img15.png)


---

### Задание 8

Воспользуемся командой `docker compose down` для остановки и удаления всех контейнеров.

![Остановка и удаление контейнеров](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img16.png)


---

### Задание 9

1. Создадим директорию alertmanager и добавим файл alertmanager.yml.
2. Обновим файл prometheus.yml и добавим файл first_rules.yml для работы с Alertmanager.
3. Добавим в файл docker-compose.yml конфигурацию для Alertmanager.
4. В volumes добавим именованный том alertmanager_data.
5. Добавим зависимость Prometheus от Alertmanager через проверку работы Alertmanager.
6. Запустим контейнеры и проверим появление события в Alertmanager.

Файл first_rules.yml:

```YAML
groups:
  - name: host_alerts
    rules:
      - alert: HighMetricValue
        expr: vasin_sa > 10
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "Высокое значение метрики на хосте."
          description: "Значение метрики больше 10. Текущее значение: {{ $value }}."
```

Файл prometheus.yml:

```YAML
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  # - job_name: "docker-server"
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
  #   static_configs:
  #     - targets: ["172.17.0.1:9100"]

  - job_name: 'pushgateway'
    honor_labels: true
    static_configs:
      - targets: ["pushgateway:9091"]
```

Файл docker-compose.yml:

```YAML
version: "3.8"

services:
  pushgateway:
    image: prom/pushgateway:v1.11.3
    container_name: vasin-sa-netology-pushgateway
    ports:
      - 9091:9091
    restart: unless-stopped
    networks:
      - docker2-hw-network
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9091/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  alertmanager:
    image: prom/alertmanager:v0.34.0
    container_name: vasin-sa-netology-alertmanager
    ports:
      - 9093:9093
    volumes:
      - ./alertmanager/alertmanager.yml:/etc/alertmanager/alertmanager.yml
      - alertmanager_data:/alertmanager
    restart: unless-stopped
    networks:
      - docker2-hw-network
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9093/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  prometheus:
    image: prom/prometheus:v3.14.0
    container_name: vasin-sa-netology-prometheus
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/first_rules.yml:/etc/prometheus/first_rules.yml
      - ./prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      alertmanager:
        condition: service_healthy
      pushgateway:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost:9090/-/healthy"]
      interval: 10s
      timeout: 5s
      retries: 3

  grafana:
    image: grafana/grafana:13.2.0
    container_name: vasin-sa-netology-grafana
    ports:
      - 80:3000
    volumes:
      - ./grafana/grafana.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    environment:
      - GR_PATH_CONFIG=/etc/grafana/grafana.ini
    restart: unless-stopped
    networks:
      - docker2-hw-network
    depends_on:
      prometheus:
        condition: service_healthy

volumes:
  alertmanager_data:
  prometheus_data:
  grafana_data:

networks:
  docker2-hw-network:
    driver: bridge
    name: vasin-sa-my-netology-hw
    ipam:
      driver: default
      config:
        - subnet: 10.5.0.0/16
```

![Запуск контейнеров](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img17.png)

![Отправка метрики](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img18.png)

![Возникновение события об алерте](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img19.png)


---

### Задание 10

Порядок установки сервисов напрямую в ОС:

1. Скачаем и установим Pushgateway, создадим unit-файл.
2. Скачаем и установим Alertmanager с конфигурацией из файла alertmanager.yml, создадим unit-файл.
3. Поменяем в prometheus.yml имена таргетов с alertmanager и pushgateway на localhost, поменяем путь до файла с правилами на /etc/prometheus/first_rules.yml.
4. Скачаем и установим Prometheus с конфигурацией из файла prometheus.yml и с правилами алертинга first_rules.yml, создадим unit-файл.
5. Добавим для grafana.ini номер порта 80.
6. Установим Grafana с конфигурацией из файла grafana.ini.
7. Проверим график в Grafana и возникновение события в Alertmanager.

Отказ от Docker и Docker Compose и переход на bare metal может быть обоснован несколькими причинами.

1. Docker замедляет работу и утилизирует больше ресурсов. Развертывание сервисов напрямую в ОС обеспечивает максимальную производительность.
2. В Docker образах потенциально мможет быть больше уязвимостей. Установка напрямую в ОС предоставляет больше контроля и прозрачности.
3. Приложения в Docker сложнее отлаживать. Приходится заходить в контейнер и пользоваться минимальным набором инструментов. При установке в ОС доступно большое количество инструментов и отладку выполнять проще.
4. Сервисы могут располагаться на разных машинах, могут быть географически отделены. Может требоваться, чтобы определенное приложение было как можно более независимым от инфраструктуры других приложений. Например, Alertmanager.
5. Не на все случаи жизни существуют образы. Часть приложений приходится собирать вручную, иногда приходится собирать что-то самописное. Иногда целесообразнее вообще не использовать Docker.

Файл pushgateway.service:

```
[Unit]
Description=Pushgateway Service
After=network.target

[Service]
User=pushgateway
Group=pushgateway
Type=simple
ExecStart=/usr/local/bin/pushgateway \
    --web.listen-address=":9091"
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Файл alertmanager.service:

```
[Unit]
Description=Alertmanager Service
After=network.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/local/bin/alertmanager \
    --web.listen-address=":9093" \
    --config.file=/etc/alertmanager/alertmanager.yml \
    --storage.path=/var/lib/alertmanager
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Файл prometheus.service:

```
[Unit]
Description=Prometheus Service
After=network.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --web.listen-address=":9090" \
    --config.file=/etc/prometheus/prometheus.yml \
    --storage.tsdb.path=/var/lib/prometheus
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Файл grafana.ini:

```
[security]

admin_user = vasin-sa
admin_password = netology

[server]
http_port = 80
```

![Скачивание Pushgateway](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img20.png)

![Установка Pushgateway](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img21.png)

![Создание unit-файла для Pushgateway и запуск](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img22.png)

![Скачивание Alertmanager](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img23.png)

![Установка Alertmanager](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img24.png)

![Создание unit-файла для Alertmanager и запуск](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img25.png)

![Скачивание Prometheus](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img26.png)

![Установка Prometheus](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img27.png)

![Создание unit-файла для Prometheus и запуск](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img28.png)

![Добавление репозитория Grafana](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img29.png)

![Установка Grafana](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img30.png)

![Запуск Grafana](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img31.png)

![Отправка метрик](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img32.png)

![График в Grafana](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img33.png)

![Событие в Alertmanager](https://github.com/ucantjugglikeme/netology-6-4-docker2-hw/blob/main/img/img34.png)
