# Аггрегатор Логов Apache

## Стек технологий
- FastAPI ![FastAPI](https://img.shields.io/badge/FastAPI-0.111.0-green.svg)
- PostgreSQL ![PostgreSQL](https://img.shields.io/badge/PostgreSQL-13.4-blue.svg)

## Быстрый старт

### Создание виртуального окружения
- `python3 -m venv venv && source venv/bin/activate`

### Установка зависимостей
- `pip install -r backend/infra/requirements.txt`

### Создание файла окружения
- `touch backend/infra/.env`
- После создание необходимо настроить файл по инструкции из блока "Настройки проекта".

### Команда запуска FastAPI приложения
- Выполнять команды необходимо из директории `backend`.
- `uvicorn main:app --reload`
- Список примеров URL, актуальных при локальном запуске:
- Сохранить все логи в БД: `http://127.0.0.1:8000/parse_logs/`
- Просмотреть все логи: `http://127.0.0.1:8000/logs/`
- Сохранить все логи в БД: `http://127.0.0.1:8000/logs/filter/?start_date=2024-06-20&ip_address=127.0.0.1`


### Примеры CLI команд
- Выполнять команды необходимо из директории `backend`. Примеры команд:
- Сохранить все логи в БД: `python cli.py parse-logs`
- Просмотреть все логи: `python cli.py get-logs `
- Сохранить все логи в БД: `python cli.py filter-logs --ip-address 127.0.0.1 --start-date 2024-06-20`

## Описание проекта

### Настройки проекта
- Настройки проекта хранятся в файле `settings.py`, расположенном в директории `backend/infra`.
- В настройках указаны:
  - (Стандартный) Путь к директории с логами Apache: `/var/log/apache2`
  - (Стандартный) Шаблон имени файла лога: `*.log`
  - Параметры подключения к базе данных PostgreSQL:
    - Пользователь: Получается из переменной окружения `POSTGRES_USER`
    - Пароль: Получается из переменной окружения `POSTGRES_PASSWORD`
    - Хост: Получается из переменной окружения `DB_HOST`
    - Порт: Получается из переменной окружения `DB_PORT`
    - Имя базы данных: Получается из переменной окружения `DB_NAME`
    - URI для подключения к PostgreSQL: `postgresql://<пользователь>:<пароль>@<хост>:<порт>/<имя_базы>`

### Требования
- Зависимости проекта перечислены в файле `requirements.txt`.
- Среди зависимостей указаны пакеты типа `fastapi`, `gino`, `uvicorn`, и др.

### Инициализация базы данных
- Инициализация базы данных осуществляется в файле `db_init.py`.
- Для работы с базой данных используется объект `Gino`.

### Модели базы данных
- Модели для записей логов определены в файле `models.py`.
- Модель `LogEntry` содержит поля `id`, `date_time`, `ip_address`, `method`, `url`, `status`, `size` и `user_agent`.

### Запуск и остановка приложения
- Основная логика приложения находится в файле `main.py`.
- В приложении определены обработчики событий для запуска и остановки, а также для подключения и отключения от базы данных.

### Представления API
- API представления определены в файле `views.py`.
- В представлениях содержатся функции для парсинга логов, получения логов из базы данных и фильтрации логов по параметрам.

### Команды CLI
- Команды CLI для парсинга логов определены в файле `cli.py`.
- Команды включают в себя парсинг логов, получение логов из базы данных и фильтрацию логов по параметрам.

### Обработчик базы данных
- Функции обработчика подключения к базе данных находятся в файле `db_handler.py`.
- Функция `connect_to_db` устанавливает соединение с базой данных PostgreSQL, используя URI из настроек проекта.
- Функция `disconnect_from_db` закрывает соединение с базой данных.