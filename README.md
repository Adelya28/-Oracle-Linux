![image](https://github.com/user-attachments/assets/267323fd-013b-4b9f-ace1-b73fd6409840)

Методические указания по гостевым дополнениям в Линукс Иблиева Аделя К-ИСП-49-1

1 шаг. Верхняя вкладка "Устройства". Выбрать "Подключить образ диска Дополнений гостевой ОС"

2 шаг. В диалоговом окне выбрать "Run". Ввести пароль. Далее - установка.

3 шаг. Вкладка "Устройства". Общий буфер обмена - двунаправленый. Функция Drag and Drog - двунаправленный. 

4 шаг. Перезапустить ВМ. Проверить в терминале Ctrl+Shift+V.


Установка git

`sudo dnf install git-all`

![image](https://github.com/user-attachments/assets/5526f1ca-deb8-4fee-801a-b789a1fb4136)


Удалить podman:

`sudo apt remove podman`


Устанавливаем wget:

`sudo yum install wget`


Скачиваем конфигурационный файл для репозитория докер:

`wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo`

![image](https://github.com/user-attachments/assets/97021146-71d2-4254-93d1-a10e8ed0a8b4)


Теперь устанавливаем docker:

`sudo dnf install docker-ce docker-ce-cli`


И разрешаем автозапуск сервиса и стартуем его:

`systemctl enable docker --now`


Установка docker-compose:

`sudo yum install curl`

`COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)`

`sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose`

![установка docker-compose](https://github.com/user-attachments/assets/2497ce3a-8ac9-497d-b110-34c5b5d688bd)

`sudo chmod +x /usr/bin/docker-compose`

`sudo docker-compose --version`

![установка compose v2 29 7](https://github.com/user-attachments/assets/20a492d4-7380-42f0-b57d-629a639feb28)


Установка графана:

`git clone https://github.com/skl256/grafana_stack_for_docker.git`

`sudo mkdir -p /mnt/common_volume/swarm/grafana/config`

`sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}`

`sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}`

`touch /mnt/common_volume/grafana/grafana-config/grafana.ini`

`cd grafana_stack_for_docker`

`cp config/* /mnt/common_volume/swarm/grafana/config/`

`mv grafana.yaml docker-compose.yaml`

`sudo docker compose up -d`

![установка grafana](https://github.com/user-attachments/assets/a106fc25-6123-406e-bb83-0b3fe545076f)


Остановка docker-compose

![остановка docker-compose](https://github.com/user-attachments/assets/389f093b-18bd-41d9-925e-0ab9be7c34b2)


localhost:3000

Установка Prometheus (http://prometheus:3030)

В Терминале установка node-exporter:
ls
cd grafana_stack_for_docker
sudo vi docker-compose.yaml

node-exporter:
    image: prom/node-exporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    container_name: exporter
    hostname: exporter
    command:
      - --path.procfs=/host/proc
      - --path.sysfs=/host/sys
      - --collector.filesystem.ignored-mount-points
      - ^/(sys|proc|dev|host|etc|rootfs/var/lib/docker/containers|rootfs/var/lib/docker/overlay2|rootfs/run/docker/netns|rootfs/var/lib/docker/aufs)($$|/)
    ports:
      - 9100:9100
    restart: unless-stopped
    environment:
      TZ: "Europe/Moscow"
    networks:
      - default

:wq!

`sudo docker compose up -d`

![добавление node-exporter](https://github.com/user-attachments/assets/015d3026-4efe-4ca0-924c-81c2c4516321)


Удаление локи и тд:

`sudo docker-compose stop`

`sudo vi docker-compose.yaml`

удалить там всё ненужное!

`sudo docker-compose up -d`

![удаление локи и тд](https://github.com/user-attachments/assets/51724023-c80c-4493-8d10-d5b63f2abd6b)


node-exporter в Prometheus

![image](https://github.com/user-attachments/assets/cd951a53-ebd0-452c-8476-0b7007043c88)


Добавление VM и AM

![установк Ви Метрикс](https://github.com/user-attachments/assets/11494698-97b6-4e29-8b6d-84b9d93d22d6)




`echo -e "# TYPE OILCOINT_metric1 gauge\nOILCOINT_metric1 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus
echo -e "# TYPE OILCOINT_metric2 gauge\nOILCOINT_metric2 0" | curl --data-binary @- http://localhost:8428/api/v1/import/prometheus
curl -G 'http://localhost:8428/api/v1/query' --data-urlencode 'query=OILCOINT_metric1'`

![Curl](https://github.com/user-attachments/assets/e4233e5c-0eab-4080-a72f-48d2184b5eb2)



ЕСЛИ node-exporter НЕ РАБОТАЕТ:
проверить (может он рабоатет)

`sudo docker-compose ps`

`cd /mnt/common_volume/swarm/grafana/congig`

`ls`

`vi prometheus.yaml`

![image](https://github.com/user-attachments/assets/afc6f70e-fd32-483a-8c96-82fbe11d5bce)

targets: ['exporter:9100', и тд

`sudo docker-compose stop`

`sudo docker-compose up -d`

перезапустить dashboard.

