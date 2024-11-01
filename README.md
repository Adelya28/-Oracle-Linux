
Методические указания по гостевым дополнениям в Линукс Иблиева Аделя К-ИСП-49-1
1 шаг. Верхняя вкладка "Устройства". Выбрать "Подключить образ диска Дополнений гостевой ОС"
2 шаг. В диалоговом окне выбрать "Run". Ввести пароль. Далее - установка.
3 шаг. Вкладка "Устройства". Общий буфер обмена - двунаправленый. Функция Drag and Drog - двунаправленный. 
4 шаг. Перезапустить ВМ. Проверить в терминале Ctrl+Shift+V.


Установка git
sudo dnf install git-all

Удалить podman:
sudo apt remove podman

Устанавливаем wget:
sudo yum install wget

Скачиваем конфигурационный файл для репозитория докер:
wget -P /etc/yum.repos.d/ https://download.docker.com/linux/centos/docker-ce.repo

Теперь устанавливаем docker:
sudo dnf install docker-ce docker-ce-cli

И разрешаем автозапуск сервиса и стартуем его:
systemctl enable docker --now

Установка docker-compose:
sudo yum install curl
COMVER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d\" -f4)
sudo curl -L "https://github.com/docker/compose/releases/download/$COMVER/docker-compose-$(uname -s)-$(uname -m)" -o /usr/bin/docker-compose
sudo chmod +x /usr/bin/docker-compose
sudo docker-compose --version

Установка графана:
git clone https://github.com/skl256/grafana_stack_for_docker.git
sudo mkdir -p /mnt/common_volume/swarm/grafana/config
sudo mkdir -p /mnt/common_volume/grafana/{grafana-config,grafana-data,prometheus-data,loki-data,promtail-data}
sudo chown -R $(id -u):$(id -g) {/mnt/common_volume/swarm/grafana/config,/mnt/common_volume/grafana}
touch /mnt/common_volume/grafana/grafana-config/grafana.ini
cd grafana_stack_for_docker
cp config/* /mnt/common_volume/swarm/grafana/config/
mv grafana.yaml docker-compose.yaml
sudo docker compose up -d

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

sudo docker compose up -d


Удаление локи и тд:
sudo docker-compose stop
sudo vi docker-compose.yaml
удалить там всё ненужное!
sudo docker-compose up -d