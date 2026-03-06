# Домашнее задание к занятию "`Название занятия`" - `Фамилия и имя студента`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.

Желаем успехов в выполнении домашнего задания!

### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

`Docker Compose является мощным инструментом для управления многоконтейнерными приложениями. Он позволяет быстро создавать и управлять несколькими контейнерами как одной целой системой, что значительно упрощает процесс развертывания и масштабирования приложений.`

1. `У меня уже был установлен docker, поэтому мне не надо устанавливать docker compose, так как он установился вместе с docker.`
2. `Можно посмотреть какие команды умеет делать docker compose.`

```
Поле для вставки кода...
docker compose

```

### Задание 2

`Создаем файл docker-compose.yml и вносим туда первичные настройки: version, services, volumes, networks.
При выполнении задания используется подсеть 10.5.0.0/16. Наша подсеть будет называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.`

```
Поле для вставки кода...
version: '3'
volumes:
networks:
  gribya-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1
services:

```

### Задание 3

`Создаем конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus. Добавляем необходимые тома с данными и конфигурацией. Обеспечиваем внешний доступ к порту 9090 c докер-сервера.`

```
Поле для вставки кода...
volumes:
    prometheus_data: {}
prometheus:
    image: prom/prometheus:v2.47.2
    container_name: gribya-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    networks:
      - gribya-my-netology-hw
    restart: always....

```

![Скриншот-1](https://github.com/Yulia-GG/pattern-docker/blob/img01.png)

### Задание 4

`Создаем конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway. Обеспечиваем внешний доступ к порту 9091 c докер-сервера.`

```
Поле для вставки кода...
pushgateway:
     image: prom/pushgateway:v1.6.2
     container_name: gribya-netology-pushgateway
     ports:
      - 9091:9091
     networks:
      - gribya-my-netology-hw
     depends_on:
      - prometheus
     restart: unless-stopped

```

### Задание 5

`Создаем конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
Добавляем необходимые тома с данными и конфигурацией. Добавляем переменную окружения с путем до файла с кастомными настройками. Обеспечиваем внешний доступ к порту 3000 c порта 80 докер-сервера.`

```
Поле для вставки кода...
grafana:
    image: grafana/grafana
    container_name: gribya-netology-grafana
    environment:
      GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
      - 80:3000
    volumes:
      - ./grafana:/etc/grafana
      - grafana_data:/var/lib/grafana
    networks:
      - gribya-my-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped

```

### Задание 6

`Поочерёдность запуска контейнеров: установить какой контейнер от какого зависит - depends_on.
Настроить режим перезапуска контейнеров: написать условие, при котором контейнер должен перезапускаться - restart.
Настроить использование контейнерами одной сети: имеем внутреннюю сеть networks gribya-my-netology-hw.
Запустите сценарий в detached режиме: добавить нужный флаг при запуске команды docker compose up - -d`

![Скриншот-2](https://github.com/Yulia-GG/pattern-docker/blob/img3.png)
![Скриншот-3](https://github.com/Yulia-GG/pattern-docker/blob/img5.png)
![Скриншот-4](https://github.com/Yulia-GG/pattern-docker/blob/img7.png)


### Задание 7

```
Поле для вставки кода...
version: '3'

volumes:
    prometheus_data: {}
    grafana_data: {}

networks:
  gribya-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
          gateway: 10.5.0.1

services:

  prometheus:
    image: prom/prometheus:v2.47.2
    container_name: gribya-netology-prometheus
    command: --web.enable-lifecycle --config.file=/etc/prometheus/prometheus.yml
    ports:
      - 9090:9090
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    networks:
      - gribya-my-netology-hw
    restart: always

  pushgateway:
     image: prom/pushgateway:v1.6.2
     container_name: gribya-netology-pushgateway
     ports:
      - 9091:9091
     networks:
      - gribya-my-netology-hw
     depends_on:
      - prometheus
     restart: unless-stopped

  grafana:
    image: grafana/grafana
    container_name: gribya-netology-grafana
    environment:
      GF_PATHS_CONFIG: /etc/grafana/custom.ini
    ports:
      - 80:3000
    volumes:
      - ./grafana:/etc/grafana
      - grafana_data:/var/lib/grafana
    networks:
      - gribya-my-netology-hw
    depends_on:
      - prometheus
    restart: unless-stopped

```

![Скриншот-5](https://github.com/Yulia-GG/pattern-docker/blob/img9.png)

![Скриншот-6](https://github.com/Yulia-GG/pattern-docker/blob/img8.png)

### Задание 8

![Скриншот-7](https://github.com/Yulia-GG/pattern-docker/blob/img10.png)
