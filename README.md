# Лабораторная работа №8: Docker Compose.

## Структура проекта

- `devops8compose/` — веб-приложение на Flask со счётчиком посещений в Redis:
  - `app.py` — код Flask-приложения.
  - `compose.yaml` — описание сервисов `web` и `redis`.
  - `dockerfile` — инструкция сборки образа приложения.
  - `requirements.txt` — зависимости Python.
- `devops8prom/` — стек мониторинга:
  - `compose.yaml` — описание сервисов `prometheus`, `grafana` и `blackbox`.
  - `prometheus/prometheus.yml` — конфигурация сбора метрик и Blackbox-проб.
  - `grafana/datasource.yml` — автонастройка источника данных Prometheus.
- `screenshots/` — скриншоты выполнения работы.

## Ход выполнения работы

### 1. Подготовка веб-приложения (Flask + Redis)

Создание Flask-приложения со счётчиком посещений в `app.py`:
![Создание app.py](screenshots/scr1.png)

Указание зависимостей `flask` и `redis` в `requirements.txt`:
![Зависимости в requirements.txt](screenshots/scr2.png)

Создание `dockerfile` для сборки образа Flask-приложения на базе `python:3.10-alpine`:
![Создание dockerfile](screenshots/scr3.png)

Настройка `compose.yaml` для сервисов `web` (проброс порта `8000:5000`) и `redis` (`redis:alpine`):
![Настройка compose.yaml](screenshots/scr4.png)

Запуск `docker compose up -d`: загрузка образа `redis`, сборка образа `web`, создание сети и запуск контейнеров `devops8compose-redis-1` и `devops8compose-web-1`:
![Загрузка образа redis и сборка web](screenshots/scr5.png)
![Запуск контейнеров web и redis](screenshots/scr6.png)

Проверка статуса контейнеров через `docker compose ps` и тестирование счётчика посещений запросами `curl 127.0.0.1:8000`:
![Проверка статуса контейнеров и работы счётчика](screenshots/scr7.png)

### 2. Настройка стека мониторинга (Prometheus + Grafana + Blackbox Exporter)

Настройка `compose.yaml` для стека мониторинга (фрагмент конфигурации сервиса `grafana`, порты, переменные окружения и тома `prom_data`, `grafana_data`):
![Настройка compose.yaml для стека мониторинга](screenshots/scr8.png)

Настройка `prometheus.yml` (глобальные интервалы сбора, `alerting` и задача `prometheus` на `localhost:9090`):
![Настройка prometheus.yml](screenshots/scr9.png)

Настройка `datasource.yml` для автоматического подключения источника данных Prometheus (`http://prometheus:9090`) в Grafana:
![Настройка datasource.yml](screenshots/scr10.png)

Запуск стека мониторинга через `docker compose up -d`: загрузка образов `grafana` и `prometheus`, создание сети и томов `devops8prom_grafana_data`, `devops8prom_prom_data` и запуск контейнеров `grafana` и `prometheus`:
![Загрузка образов grafana и prometheus](screenshots/scr11.png)
![Создание сети, томов и запуск контейнеров мониторинга](screenshots/scr12.png)

Проверка статуса контейнеров `grafana` (порт `3000`) и `prometheus` (порт `9090`) через `docker compose ps`:
![Проверка статуса контейнеров мониторинга](screenshots/scr13.png)

Открытый веб-интерфейс Prometheus по адресу `127.0.0.1:9090`:
![Веб-интерфейс Prometheus](screenshots/scr14.png)

Открытый веб-интерфейс Grafana по адресу `127.0.0.1:3000`:
![Веб-интерфейс Grafana](screenshots/scr15.png)

Остановка и удаление контейнеров `grafana`, `blackbox_exporter`, `prometheus` и сети `devops8prom_default` командой `docker compose down`:
![Остановка стека мониторинга через docker compose down](screenshots/scr16.png)

Пересборка и запуск обновлённого стека мониторинга (`docker compose build` и `docker compose up -d`) с поднятием контейнеров `prometheus`, `grafana` и `blackbox_exporter`:
![Пересборка и запуск стека с Blackbox Exporter](screenshots/scr17.png)

Проверка статуса сервисов `blackbox_exporter` (порт `9115`), `grafana` и `prometheus` через `docker compose ps` и обращение к веб-странице Blackbox Exporter (`curl 127.0.0.1:9115`) с просмотром результатов последних проб:
![Проверка статуса сервисов и ответ Blackbox Exporter](screenshots/scr18.png)

Дашборд `Blackbox Exporter (HTTP-пробник)` в Grafana с обзором HTTP-проб (`http://10.0.2.15:8000` — `UP`, `https://etis.psu.ru` — `DOWN`, `https://student.psu.ru` — `UP`) и графиком длительности проверок:
![Дашборд Blackbox Exporter в Grafana](screenshots/scr19.png)