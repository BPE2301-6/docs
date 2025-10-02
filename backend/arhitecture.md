## Структура проекта
```
├── alembic
│   ├── env.py
│   └── versions/
├── alembic.ini
├── config.example.toml
├── Makefile
├── pyproject.toml
├── pytest.ini
├── README.md
├── run.sh
├── src/
│   ├── adapters/
│   ├── api/
│   │   ├── __main__.py
│   │   ├── lifespan.py
│   │   └── main.py
│   ├── apps/
│   │   └── exmpl/
│   │       ├── api/
│   │       │   └── v1/
│   │       │       └── endpoints
│   │       ├── repositories/
│   │       │   ├── interfaces.py
│   │       │   └── sql/
│   │       ├── schemas/
│   │       │   ├── dataclasses/
│   │       │   └── pydantic/
│   │       └── services/
│   ├── core/
│   │   ├── config/
│   │   │   └── config.py
│   │   ├── constants/
│   │   ├── db/
│   │   │   ├── base.py
│   │   │   ├── db.py
│   │   │   └── models/
│   │   ├── dependencies/
│   │   ├── di/
│   │   │   └── container.py
│   │   ├── exceptions/
│   │   ├── logger/
│   │   │   └── logger.py
│   │   └── ports/
│   └── tasks/
├── tests/
│   └── conftest.py
└── uv.lock
```

## Назначение файлов и директорий

- `alembic/`  
    ~ миграции базы данных
    - `env.py` — точка входа Alembic, подключает настройки и движок
    - `versions/` — каталог с файлами миграций (шаги изменения схемы БД)
- `alembic.ini`  
    ~ конфигурация Alembic (настройки миграций)
- `config.example.toml`  
    ~ пример конфигурации приложения (БД, Redis, MinIO, etc.)
- `Makefile`  
    ~ удобные команды (run, migrate, lint, format, test)
- `pyproject.toml`  
    ~ зависимости, форматтеры, линтеры, версия Python
- `pytest.ini`  
    ~ настройки для тестов (pytest)
- `README.md`  
    ~ описание репозитория, инструкция по запуску
- `run.sh`  
    ~ скрипт для локального запуска (shorthand для dev)
- `uv.lock`  
    ~ lock-файл зависимостей (uv, как аналог pip-tools/poetry.lock)

---

### `src/` — код приложения

- `adapters/`  
    ~ интеграции с внешними сервисами (например: Redis, MinIO, почта, платёжки)
- `api/`  
    ~ слой запуска приложения и FastAPI-роутинг
    - `__main__.py` — точка входа (`python -m src.api`)
    - `lifespan.py` — логика старта/остановки (инициализация ресурсов)
    - `main.py` — создание FastAPI-приложения, подключение роутов
- `apps/`  
    ~ доменные модули (каждое приложение — независимая область логики)
    - `exmpl/` — пример приложения
        - `api/v1/endpoints/` — контроллеры (обработчики запросов)
        - `repositories/` — доступ к данным
            - `interfaces.py` — интерфейсы репозиториев
            - `sql/` — SQL-реализации (работа через SQLAlchemy)
        - `schemas/` — DTO и схемы
            - `dataclasses/` — внутренние структуры
            - `pydantic/` — публичные валидационные модели (FastAPI)
        - `services/` — бизнес-логика (сценарии работы домена)
- `core/`  
    ~ базовая инфраструктура
    - `config/` — работа с конфигами
        - `config.py` — загрузка и парсинг .toml/.env
    - `constants/` — константы проекта
    - `db/` — база данных
        - `base.py` — декларативная база моделей
        - `db.py` — подключение, движок, сессии
        - `models/` — SQLAlchemy модели
    - `dependencies/` — зависимости FastAPI (Depends)
    - `di/`
        - `container.py` — DI-контейнер (внедрение сервисов/репозиториев)
    - `exceptions/` — кастомные исключения проекта
    - `logger/`
        - `logger.py` — конфигурация логирования
    - `ports/` — абстрактные интерфейсы (например: шина событий, адаптеры)
- `tasks/`  
    ~ фоновые задачи (Celery, APScheduler, FastStream и др.)

---

### `tests/`

- тесты проекта
- `conftest.py` — фикстуры pytest (БД, клиент, моки)