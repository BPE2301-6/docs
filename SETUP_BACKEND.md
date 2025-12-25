# Инструкция по запуску Backend

Подробная инструкция по настройке и запуску backend части приложения.

## Требования

- Python 3.12 или выше
- uv (package manager)
- PostgreSQL (опционально, можно использовать Docker)
- Redis (опционально, можно использовать Docker)

## Установка зависимостей

### 1. Установка uv

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 2. Клонирование и установка зависимостей

```bash
cd backend
uv sync
```

Это создаст виртуальное окружение и установит все необходимые зависимости из `pyproject.toml`.

## Конфигурация

### 1. Создание конфигурационных файлов

```bash
# Скопировать примеры конфигурации
cp .env.example .env.dev
cp config.example.toml config.toml
```

### 2. Настройка переменных окружения (.env.dev)

```env
# База данных
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=tasktracker
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379

# Backend
BACKEND_PORT=8000
SECRET_KEY=your-secret-key-here

# Логирование
LOG_LEVEL=INFO
LOG_MAX_SIZE=10m
LOG_MAX_FILE=3
```

### 3. Настройка config.toml

Отредактируйте файл `config.toml` согласно вашим требованиям.

## Запуск базы данных

### Вариант 1: Docker Compose (рекомендуется)

```bash
docker-compose -f docker-compose.dev.yaml up -d
```

Это запустит PostgreSQL и Redis в контейнерах.

### Вариант 2: Локальная установка

Установите PostgreSQL и Redis локально и настройте подключение в `.env.dev`.

## Миграции базы данных

### Применение миграций

```bash
# Активировать виртуальное окружение
source .venv/bin/activate  # Linux/macOS
# или
.venv\Scripts\activate  # Windows

# Применить миграции
alembic upgrade head
```

### Создание новой миграции

```bash
alembic revision --autogenerate -m "описание изменений"
```

## Запуск приложения

### Способ 1: Через run.sh (Linux/macOS)

```bash
chmod +x run.sh
./run.sh
```

### Способ 2: Через uvicorn напрямую

```bash
# Активировать виртуальное окружение
source .venv/bin/activate

# Запустить сервер
uvicorn src.api.main:app --reload --host 0.0.0.0 --port 8000
```

### Способ 3: Через Makefile

```bash
make run
```

## Проверка работоспособности

После запуска приложение будет доступно по адресу:

- API: http://localhost:8000
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc
- Health check: http://localhost:8000/api/v1/health

## Тестирование

### Запуск всех тестов

```bash
pytest
```

### Запуск с покрытием

```bash
pytest --cov=src tests/
```

### Запуск конкретного теста

```bash
pytest tests/test_specific.py
```

## Линтинг и форматирование

### Проверка кода

```bash
ruff check .
```

### Автоматическое исправление

```bash
ruff check --fix .
```

### Форматирование кода

```bash
ruff format .
```

### Pre-commit hooks

```bash
# Установка pre-commit hooks
pre-commit install

# Ручной запуск
pre-commit run --all-files
```

## Структура проекта

```
backend/
├── alembic/              # Миграции БД
│   └── versions/
├── src/
│   ├── api/              # API конфигурация
│   │   ├── main.py       # Точка входа
│   │   └── lifespan.py   # Lifecycle hooks
│   ├── apps/             # Модули приложения
│   │   ├── api/          # Эндпоинты
│   │   ├── models/       # SQLAlchemy модели
│   │   ├── repository/   # Репозитории
│   │   ├── schemas/      # Pydantic/dataclass схемы
│   │   └── service/      # Бизнес-логика
│   ├── core/             # Базовые компоненты
│   │   ├── config/       # Конфигурация
│   │   ├── db/           # Database setup
│   │   ├── dependencies/ # FastAPI зависимости
│   │   ├── exceptions/   # Кастомные исключения
│   │   └── logger/       # Логирование
│   └── tasks/            # Фоновые задачи
└── tests/                # Тесты
```

## Работа с Docker

### Сборка образа

```bash
docker build -t tasktracker-backend .
```

### Запуск контейнера

```bash
docker run -p 8000:8000 --env-file .env.dev tasktracker-backend
```

## Troubleshooting

### Ошибка подключения к БД

1. Проверьте, что PostgreSQL запущен
2. Проверьте правильность credentials в `.env.dev`
3. Проверьте, что порт 5432 не занят

### Ошибки миграций

```bash
# Откат последней миграции
alembic downgrade -1

# Полный откат
alembic downgrade base
```

### Проблемы с зависимостями

```bash
# Очистка и переустановка
rm -rf .venv
uv sync
```

## Полезные команды

```bash
# Просмотр логов Docker
docker-compose -f docker-compose.dev.yaml logs -f backend

# Проверка здоровья приложения
curl http://localhost:8000/api/v1/health

# Интерактивная оболочка Python
source .venv/bin/activate
python
```

## Дополнительная информация

- [FastAPI документация](https://fastapi.tiangolo.com/)
- [SQLAlchemy 2.0 документация](https://docs.sqlalchemy.org/)
- [Alembic документация](https://alembic.sqlalchemy.org/)
- [uv документация](https://github.com/astral-sh/uv)
