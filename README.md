# Apache Log Aggregator

## Структура проекта

Для просмотра структуры проекта перейдите к его [описанию](./structure.md)

## Установка и настройка

1. Клонируйте репозиторий:
    ```sh
    git clone <repository_url>
    cd log_aggregator
    ```

2. Установите Poetry, если он еще не установлен:
    ```sh
    curl -sSL https://install.python-poetry.org | python3 -
    ```

3. Установите зависимости:
    ```sh
    poetry install
    ```

4. Настройте переменные окружения в файле `.env`:
    ```dotenv
    DB_NAME=apache_logs
    DB_USER=postgres
    DB_PASSWORD=postgres
    DB_HOST=localhost
    DB_PORT=5432
    LOG_FILES_PATH=src/logs
    LOG_FILE_MASK=access_log
    SECRET_KEY=hpi7h78h8h8h8hcwdwwwww-23d23d2fdf3-f32fk2mfk23
    DEBUG=True
    ```

5. [Создайте базу данных](./database.md) и примените миграции:
    ```sh
    poetry run python src.manage.py makemigrations
    poetry run python src.manage.py migrate
    ```

6. Создайте суперпользователя для доступа к админ-панели:
    ```sh
    poetry run python src.manage.py createsuperuser
    ```

## Запуск приложения

### Запуск сервера

Запустите сервер Django:
```sh
poetry run python src.manage.py runserver
```

### Запуск парсера логов через консоль

Запустите консольное приложение для парсинга логов:
```sh
poetry run python src.manage.py parse_logs
```

### Запуск парсера логов через оконный интерфейс

Запустите оконное приложение для парсинга логов:
```sh
poetry run python apps/apache_log_aggregator/gui.py
```

### Настройка cron задач

В файле settings.py, добавьте следующую строку, чтобы добавить cron задачу:

```sh
CRONJOBS = [
    (
        "*/1 * * * *", # означает, что задача будет запускаться каждую минуту
        "apps.apache_log_aggregator.cron.parse_logs_cron", # это путь к функции, которая будет выполняться
        ">> ../logs/logfile.log 2>&1", # перенаправляет вывод и ошибки в файл логов
    )
]

```

Далее нужно зарегистрировать ваши задачи cron с помощью django-crontab.
Для этого выполните следующую команду:
```sh
poetry run python src.manage.py crontab add

```

### Проверка установленных задач cron

Чтобы убедиться, что задачи cron зарегистрированы правильно, выполните команду:

```sh
poetry run python src.manage.py crontab show
```

### Удаление задач cron (при необходимости)
Если вам нужно удалить зарегистрированные задачи cron, выполните команду:

```sh
poetry run python src.manage.py crontab remove

```


### API

API доступно по адресу:

```sh
curl -X GET http://127.0.0.1:8000/api/logs/
```

## Примеры запросов к API

Получение логов, сгруппированных по IP

```sh
curl -X GET "http://127.0.0.1:8000/api/logs/grouped/?group_by=ip"
```

Получение логов, сгруппированных по дате

```sh
curl -X GET "http://127.0.0.1:8000/api/logs/grouped/?group_by=date"
```

Получение логов, сгруппированных по IP и с выборкой по промежутку дат

```sh
curl -X GET "http://127.0.0.1:8000/api/logs/grouped/?group_by=ip&start_date=2023-01-01&end_date=2023-01-31"
```

Запуск парсинга логов

```sh
curl -X POST "http://127.0.0.1:8000/api/logs/parse_logs/"
```
