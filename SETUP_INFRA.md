# Инструкция по запуску инфраструктуры

Подробная инструкция по настройке и развертыванию DevOps инфраструктуры проекта.

## Требования

- Docker 20.10+
- Docker Compose 2.0+
- Git
- 4GB RAM минимум для всех сервисов

## Структура инфраструктуры

```
infra/
├── nginx/                    # Конфигурация Nginx
│   ├── conf.d/              # Server blocks
│   └── nginx.conf           # Основная конфигурация
├── grafana/                 # Дашборды Grafana
├── docker-compose.yaml      # Production конфигурация
├── docker-compose.dev.yaml  # Development конфигурация
├── docker-compose.monitoring.yaml  # Мониторинг стек
├── prometheus.yml           # Конфигурация Prometheus
├── loki-config.yaml        # Конфигурация Loki
├── promtail-config.yaml    # Конфигурация Promtail
└── Makefile                # Утилиты для управления
```

## Быстрый старт

### Development режим

```bash
cd infra

# Скопировать переменные окружения
cp .env.dev.example .env.dev

# Запустить базовую инфраструктуру
docker-compose -f docker-compose.dev.yaml up -d

# Или через Makefile
make dev-up
```

### Production режим

```bash
cd infra

# Настроить переменные окружения
cp .env.example .env
# Отредактируйте .env файл

# Запустить все сервисы
docker-compose up -d

# Или через Makefile
make up
```

## Конфигурация

### Переменные окружения (.env)

```env
# Backend
BACKEND_PORT=8000

# Nginx
NGINX_PORT=80
FRONTEND_DIST=../frontend/dist

# Мониторинг
PROMETHEUS_PORT=9090
GRAFANA_PORT=3000

# Логирование
LOG_MAX_SIZE=10m
LOG_MAX_FILE=3
```

## Сервисы

### 1. Backend (FastAPI)

```yaml
# Доступен на: http://localhost:${BACKEND_PORT}
# Документация: http://localhost:${BACKEND_PORT}/docs
# Health check: http://localhost:${BACKEND_PORT}/api/v1/health
```

**Конфигурация:**
- Образ из GitHub Container Registry
- Автоматический restart
- Health checks каждые 30 секунд
- JSON логирование

### 2. Nginx (Reverse Proxy)

```yaml
# Доступен на: http://localhost:${NGINX_PORT}
```

**Функции:**
- Раздача статических файлов frontend
- Reverse proxy для backend API
- Gzip сжатие
- SSL/TLS терминация (в production)
- Rate limiting

**Конфигурация:**

```nginx
# nginx/conf.d/default.conf
upstream backend {
    server backend:8000;
}

server {
    listen 80;

    # Frontend
    location / {
        root /usr/share/nginx/html;
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### 3. Prometheus (Мониторинг метрик)

```yaml
# Доступен на: http://localhost:${PROMETHEUS_PORT}
# По умолчанию: http://localhost:9090
```

**Метрики:**
- Backend performance (запросы, latency, ошибки)
- Системные метрики (CPU, RAM, disk)
- Custom бизнес-метрики

**Конфигурация targets:**

```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'backend'
    static_configs:
      - targets: ['backend:8000']
    metrics_path: '/api/v1/metrics'
```

### 4. Grafana (Визуализация)

```yaml
# Доступен на: http://localhost:${GRAFANA_PORT}
# По умолчанию: http://localhost:3000
# Логин: admin / admin (при первом входе)
```

**Возможности:**
- Дашборды для визуализации метрик
- Алерты и уведомления
- Интеграция с Prometheus и Loki
- Кастомные панели

**Готовые дашборды:**
- Backend Performance
- System Metrics
- Application Logs

### 5. Loki (Агрегация логов)

```yaml
# Внутренний сервис (не exposed)
# Доступен через Grafana
```

**Функции:**
- Централизованное хранение логов
- Индексация и поиск
- Интеграция с Grafana

### 6. Promtail (Сбор логов)

```yaml
# Внутренний сервис (не exposed)
```

**Собирает логи из:**
- Docker контейнеров
- Системных логов (/var/log)
- Application логов

## Управление сервисами

### Docker Compose команды

```bash
# Запуск всех сервисов
docker-compose up -d

# Просмотр логов
docker-compose logs -f [service_name]

# Остановка сервисов
docker-compose down

# Перезапуск конкретного сервиса
docker-compose restart [service_name]

# Просмотр статуса
docker-compose ps

# Пересборка образов
docker-compose build --no-cache
```

### Makefile команды

```bash
# Development
make dev-up          # Запустить dev окружение
make dev-down        # Остановить dev окружение
make dev-logs        # Просмотр логов dev

# Production
make up              # Запустить production
make down            # Остановить production
make restart         # Перезапустить сервисы
make logs            # Просмотр логов

# Мониторинг
make monitoring-up   # Запустить мониторинг стек
make monitoring-down # Остановить мониторинг

# Утилиты
make clean           # Очистка volumes
make ps              # Статус контейнеров
```

## Мониторинг и логирование

### Настройка дашбордов Grafana

1. Откройте http://localhost:3000
2. Войдите (admin/admin)
3. Добавьте data sources:
   - Prometheus: http://prometheus:9090
   - Loki: http://loki:3100

4. Импортируйте дашборды из `grafana/dashboards/`

### Просмотр метрик в Prometheus

1. Откройте http://localhost:9090
2. Используйте PromQL для запросов:

```promql
# Количество запросов к API
rate(http_requests_total[5m])

# Latency запросов
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# Количество ошибок
rate(http_requests_total{status=~"5.."}[5m])
```

### Поиск логов в Grafana

1. Откройте Grafana → Explore
2. Выберите Loki data source
3. Используйте LogQL:

```logql
# Все логи backend
{container_name="backend"}

# Логи с ошибками
{container_name="backend"} |= "ERROR"

# Логи за последний час
{container_name="backend"} |= "error" [1h]
```

## SSL/TLS настройка (Production)

### Использование Let's Encrypt

```bash
# Установка certbot
sudo apt-get install certbot python3-certbot-nginx

# Получение сертификата
sudo certbot --nginx -d yourdomain.com

# Автоматическое обновление
sudo certbot renew --dry-run
```

### Конфигурация Nginx для HTTPS

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    # SSL настройки
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Остальная конфигурация...
}
```

## Backup и восстановление

### Backup Grafana

```bash
# Backup volume
docker run --rm \
  -v infra_grafana-data:/data \
  -v $(pwd)/backups:/backup \
  alpine tar czf /backup/grafana-backup-$(date +%Y%m%d).tar.gz -C /data .
```

### Восстановление Grafana

```bash
# Restore volume
docker run --rm \
  -v infra_grafana-data:/data \
  -v $(pwd)/backups:/backup \
  alpine sh -c "cd /data && tar xzf /backup/grafana-backup-YYYYMMDD.tar.gz"
```

## Troubleshooting

### Сервис не запускается

```bash
# Проверить логи
docker-compose logs [service_name]

# Проверить статус
docker-compose ps

# Пересоздать контейнер
docker-compose up -d --force-recreate [service_name]
```

### Проблемы с портами

```bash
# Проверить занятые порты
sudo lsof -i :[port]

# Изменить порт в .env файле
NGINX_PORT=8080
```

### Недостаточно памяти

```bash
# Проверить использование ресурсов
docker stats

# Увеличить лимиты в docker-compose.yaml
services:
  service_name:
    mem_limit: 2g
    mem_reservation: 1g
```

### Проблемы с сетью

```bash
# Пересоздать сети
docker-compose down
docker network prune
docker-compose up -d
```

## Масштабирование

### Horizontal scaling для backend

```bash
# Запустить несколько инстансов
docker-compose up -d --scale backend=3
```

### Load balancing в Nginx

```nginx
upstream backend {
    least_conn;
    server backend:8000;
    server backend:8001;
    server backend:8002;
}
```

## CI/CD Integration

### GitHub Actions

Workflow находится в `.github/workflows/`:
- Build и push Docker образов
- Деплой на production
- Автоматические тесты

### Ручной деплой

```bash
# Pull latest images
docker-compose pull

# Recreate containers
docker-compose up -d --force-recreate

# Проверка health
make health-check
```

## Безопасность

- Все чувствительные данные в `.env` файлах
- `.env` файлы в `.gitignore`
- Регулярные обновления образов
- Ограничение ресурсов для контейнеров
- Health checks для всех сервисов
- Rate limiting в Nginx
- Мониторинг подозрительной активности

## Полезные ссылки

- [Docker документация](https://docs.docker.com/)
- [Docker Compose документация](https://docs.docker.com/compose/)
- [Nginx документация](https://nginx.org/en/docs/)
- [Prometheus документация](https://prometheus.io/docs/)
- [Grafana документация](https://grafana.com/docs/)
- [Loki документация](https://grafana.com/docs/loki/)
