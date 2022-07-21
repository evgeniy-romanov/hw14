# **Homework 14**
# **Мониторинг: Prometheus-node_exporter-grafana**
## Домашнее задание:
### *Настройка мониторинга*

### Описание/Пошаговая инструкция выполнения домашнего задания:

Настроить дашборд с 4-мя графиками:

    память;
    процессор;
    диск;
    сеть. Настроить на одной из систем:
    zabbix (использовать screen (комплексный экран);
    prometheus - grafana.

    использование систем, примеры которых не рассматривались на занятии. Список возможных систем был приведен в презентации. В качестве результата прислать скриншот экрана - дашборд должен содержать в названии имя приславшего.

---
## Рабочее окружение:
```
root@home:/home/evgeniy# lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.4 LTS
Release:	20.04
Codename:	focal
```
## Установим Prometheus:
```
root@home:/home/evgeniy# mkdir prometheus
root@home:/home/evgeniy# cd prometheus/

curl -LO https://github.com/prometheus/prometheus/releases/download/v2.26.0/prometheus-2.26.0.linux-amd64.tar.gz 

tar -xvf prometheus-2.26.0.linux-amd64.tar.gz
```

Создадим пользователя из под которого будет работать прометеус
```
useradd  --no-create-home  --shell /usr/sbin/nologin prometheus
```
Создадим пользователя из под которого будет работать node-exporter
```
useradd  --no-create-home  --shell /bin/false node_exporter
```
Создадим директории для Prometheus
```
mkdir {/etc/,/var/lib/}prometheus
```
Скопируем утилиты prometheus и promtool в /usr/local/bin/
```
cp  -iv  prometheus-2.26.0.linux-amd64/prom{etheus,tool}  /usr/local/bin/
```
Скопируем директории, передадим права пользователя Prometheus, настроим его запуск
```
cp -riv prometheus-2.26.0.linux-amd64/{console{s,_libraries},prometheus.yml} /etc/prometheus/

chown -Rv prometheus: /usr/local/bin/prom{etheus,tool} /etc/prometheus/ /var/lib/prometheus/

rm -rf /etc/systemd/system/prometheus.service

wget -P /etc/systemd/system/ https://raw.githubusercontent.com/evgeniy-romanov/hw14/main/prometheus.service

systemctl daemon-reload && systemctl start prometheus.service && systemctl status prometheus

--------------------------------------------------------------------------------------

● prometheus.service - Prometheus Monitoring
     Loaded: loaded (/etc/systemd/system/prometheus.service; enabled; vendor preset: ena>
     Active: active (running) since Thu 2022-07-21 11:43:33 MSK; 1h 21min ago
   Main PID: 9327 (prometheus)
      Tasks: 10 (limit: 19053)
     Memory: 37.2M
     CGroup: /system.slice/prometheus.service
             └─9327 /usr/local/bin/prometheus --config.file /etc/prometheus/prometheus.y>

июл 21 11:43:33 home prometheus[9327]: level=info ts=2022-07-21T08:43:33.856Z caller=hea>

```
## Устанавливаем и запускаем node_exporter:
```
curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.1.2/node_exporter-1.1.2.linux-amd64.tar.gz

tar -xvf node_exporter-1.1.2.linux-amd64.tar.gz

wget https://raw.githubusercontent.com/evgeniy-romanov/hw14/main/prometheus.yml

mv prometheus.yml /etc/prometheus/prometheus.yml

systemctl restart prometheus.service && systemctl status prometheus.service

cp -vi node_exporter-1.1.2.linux-amd64/node_exporter /usr/local/bin/node_exporter

chown node_exporter: /usr/local/bin/node_exporter

wget https://raw.githubusercontent.com/evgeniy-romanov/hw14/main/node_exporter.service

mv  node_exporter.service /etc/systemd/system/node_exporter.service

systemctl daemon-reload && systemctl start node_exporter.service && systemctl status node_exporter.service

--------------------------------------------------------------------------------------

● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor>
     Active: active (running) since Thu 2022-07-21 11:44:47 MSK; 1h 31min ago
   Main PID: 9374 (node_exporter)
      Tasks: 12 (limit: 19053)
     Memory: 13.0M
     CGroup: /system.slice/node_exporter.service
             └─9374 /usr/local/bin/node_exporter

июл 21 13:15:25 home node_exporter[9374]: level=error ts=2022-07-21T10:15:25.90>

```
## Устанавливаем и запускаем Grafana (скачивание производится с включенным VPN):

```
sudo apt-get install -y adduser libfontconfig1

https://dl.grafana.com/enterprise/release/grafana-enterprise_9.0.4_amd64.deb

sudo dpkg -i grafana-enterprise_9.0.4_amd64.deb

systemctl daemon-reload && systemctl start grafana-server && systemctl status grafana-server

------------------------------------------------------------------------------------

● grafana-server.service - Grafana instance
     Loaded: loaded (/lib/systemd/system/grafana-server.service; enabled; vendo>
     Active: active (running) since Thu 2022-07-21 11:52:55 MSK; 1h 31min ago
       Docs: http://docs.grafana.org
   Main PID: 10138 (grafana-server)
      Tasks: 10 (limit: 19053)
     Memory: 45.5M
     CGroup: /system.slice/grafana-server.service
             └─10138 /usr/sbin/grafana-server --config=/etc/grafana/grafana.ini>

июл 21 12:07:58 home grafana-server[10138]: logger=sqlstore t=2022-07-21T12:07:>
июл 21 12:22:11 home grafana-server[10138]: logger=context traceID=000000000000>
```
Открываем в браузере локально внешнюю ссылку по порту 3000 (попросит логин/пароль=admin). В качестве datasourse выбрать prometheus 
#
![alt text](https://github.com/evgeniy-romanov/hw14/raw/main/1.png)
#
Dashboards import
#
![alt text](https://github.com/evgeniy-romanov/hw14/raw/main/2.png)
#
Выводим дашборд с 4-мя графиками: память, процессор, диск, сеть
#
![alt text](https://github.com/evgeniy-romanov/hw14/raw/main/3.png)
#















