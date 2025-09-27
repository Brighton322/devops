Создать директорию и файл для логов:

sudo mkdir -p /opt/app
sudo touch /opt/app/log.txt

Создать скрипт random322.sh:

sudo nano /opt/app/random322.sh

#!/bin/bash

DIR="/opt/app"
LOG_FILE="$DIR/log.txt"

mkdir -p "$DIR"
touch "$LOG_FILE"

generate_random_string() {
  tr -dc A-Za-z0-9 </dev/urandom | head -c 20
}

while true; do
  RANDOM_STRING=$(generate_random_string)
  echo "$RANDOM_STRING" >> "$LOG_FILE"
  sleep 17
done

ctrl+O - сохранить
ctrl+X - вернуться к консоли

Сделать скрипт исполняемым:

sudo chmod +x /opt/app/random322.sh

Запуск в автозагрузке (через systemd)
Создать unit-файл:

sudo nano /etc/systemd/system/random322.service

[Unit]
Description=Random String Logger (random322)

[Service]
ExecStart=/opt/app/random322.sh
Restart=always

[Install]
WantedBy=multi-user.target

sudo systemctl daemon-reload
sudo systemctl enable random322
sudo systemctl start random322


Проверить статус:

systemctl status random322

ps aux | grep random322

tail -f /opt/app/log.txt

каждые 17 секунд должны появляться новые логи


Настройка ротации логов (logrotate)

создать конфигурационный файл:

sudo nano /etc/logrotate.d/random322

/opt/app/log.txt {
    daily
    rotate 7
    compress
    missingok
    notifempty
    create 0644 root root
}

выйти

проверить:

sudo logrotate -d /etc/logrotate.d/random322

выполнить ротацию:

sudo logrotate -f /etc/logrotate.d/random322
ls -lh /opt/app/

