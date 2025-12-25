# Технологический стек

Полное описание технологий и инструментов, используемых в проекте.

## Backend

### Основные технологии

| Технология | Версия | Назначение |
|------------|--------|------------|
| **Python** | 3.12+ | Язык программирования |
| **FastAPI** | 0.115+ | Web-фреймворк для создания API |
| **SQLAlchemy** | 2.0+ | ORM для работы с БД |
| **Alembic** | 1.16+ | Инструмент для миграций БД |
| **Pydantic** | 2.12+ | Валидация данных и настройки |
| **Uvicorn** | 0.34+ | ASGI сервер |

### База данных

- **PostgreSQL** - основная реляционная БД
- **asyncpg** - асинхронный драйвер PostgreSQL
- **Redis** - кеширование и хранение сессий

### Безопасность и аутентификация

- **PyJWT** - работа с JWT токенами
- **Passlib[argon2]** - хеширование паролей с Argon2

### Инструменты разработки

- **pytest** - фреймворк для тестирования
- **pytest-cov** - покрытие кода тестами
- **Ruff** - линтер и форматтер Python кода
- **pre-commit** - Git hooks для проверки кода

### Мониторинг

- **prometheus-client** - экспорт метрик для Prometheus
- **rich** - красивый вывод в консоль и логирование

### HTTP клиент

- **httpx** - асинхронный HTTP клиент

## Frontend

### Основные технологии

| Технология | Версия | Назначение |
|------------|--------|------------|
| **React** | 18.3+ | UI библиотека |
| **TypeScript** | 5.9+ | Типизированный JavaScript |
| **Vite** | 5.4+ | Сборщик и dev-сервер |
| **React Router DOM** | 6.26+ | Маршрутизация |
| **Zustand** | 4.5+ | State management |

### UI и стили

- **TailwindCSS** - utility-first CSS фреймворк
- **PostCSS** - обработка CSS
- **Autoprefixer** - автоматические вендорные префиксы

### Drag & Drop

- **react-dnd** - библиотека для drag and drop
- **react-dnd-html5-backend** - HTML5 бэкенд для react-dnd

### Инструменты разработки

- **ESLint** - линтер JavaScript/TypeScript
- **Prettier** - форматтер кода
- **@vitejs/plugin-react** - React плагин для Vite
- **@typescript-eslint** - TypeScript правила для ESLint

## Infrastructure & DevOps

### Контейнеризация

- **Docker** - платформа контейнеризации
- **Docker Compose** - оркестрация контейнеров

### Web-сервер

- **Nginx** - reverse proxy и статический сервер

### Мониторинг и наблюдаемость

| Сервис | Назначение |
|--------|------------|
| **Prometheus** | Сбор и хранение метрик |
| **Grafana** | Визуализация метрик и дашборды |
| **Loki** | Агрегация и хранение логов |
| **Promtail** | Сбор логов и отправка в Loki |

### CI/CD

- **GitHub Actions** - автоматизация CI/CD
- **GitHub Container Registry** - хранение Docker образов

## Архитектурные паттерны

### Backend

- **Модульный монолит** - разделение на независимые модули
- **Repository Pattern** - абстракция работы с данными
- **Service Layer** - инкапсуляция бизнес-логики
- **DTO Pattern** - разделение Pydantic схем и dataclasses
- **Dependency Injection** - через FastAPI Depends

### Frontend

- **Feature-Sliced Design** - архитектура приложения
- **Component-Based** - компонентный подход React
- **State Management** - централизованное управление состоянием через Zustand
- **Smart/Dumb Components** - разделение логики и представления

## Дополнительные инструменты

### Backend

- **greenlet** - корутины для SQLAlchemy
- **tzdata** - временные зоны

### Frontend

- **@babel/core** - транспиляция JavaScript
- **@types/node** - типы Node.js для TypeScript
- **@types/react** - типы React для TypeScript

## Версионирование

Проект использует:
- **Semantic Versioning** для релизов
- **Conventional Commits** для сообщений коммитов
- **Git Flow** для управления ветками

## Безопасность

- HTTPS с Let's Encrypt сертификатами
- JWT токены для аутентификации
- Argon2 для хеширования паролей
- CORS настройки в FastAPI
- Rate limiting на уровне Nginx
- Healthcheck эндпоинты для мониторинга
