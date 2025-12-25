# Инструкция по запуску Frontend

Подробная инструкция по настройке и запуску frontend части приложения.

## Требования

- Node.js 18+ (рекомендуется 20 LTS)
- npm или yarn
- Git

## Установка зависимостей

### 1. Переход в директорию

```bash
cd frontend
```

### 2. Установка пакетов

```bash
# Используя npm
npm install

# Или используя yarn
yarn install
```

## Конфигурация

### 1. Переменные окружения

Создайте файл `.env` в корне директории `frontend`:

```env
# API endpoint
VITE_API_URL=http://localhost:8000/api/v1

# Другие настройки
VITE_APP_TITLE=Task Tracker
```

### 2. Настройка API endpoint

Если backend запущен на другом порту или хосте, измените `VITE_API_URL` соответственно.

## Запуск приложения

### Development режим

```bash
# Используя npm
npm run dev

# Или используя yarn
yarn dev
```

Приложение будет доступно по адресу: http://localhost:5173

### Production сборка

```bash
# Сборка
npm run build

# Превью production сборки
npm run preview
```

Собранные файлы будут находиться в директории `dist/`.

## Скрипты

| Команда | Описание |
|---------|----------|
| `npm run dev` | Запуск dev сервера с hot reload |
| `npm run build` | Production сборка приложения |
| `npm run preview` | Превью production сборки локально |
| `npm run lint` | Проверка кода ESLint |
| `npm run lint:fix` | Автоматическое исправление ошибок ESLint |
| `npm run format` | Форматирование кода с Prettier |

## Структура проекта

Frontend построен с использованием архитектуры Feature-Sliced Design:

```
frontend/
├── public/               # Статические файлы
├── src/
│   ├── app/              # Инициализация приложения
│   │   ├── providers/    # Providers (Router, Theme, etc.)
│   │   ├── styles/       # Глобальные стили
│   │   └── App.tsx       # Корневой компонент
│   ├── pages/            # Страницы приложения
│   │   ├── HomePage/
│   │   ├── ProjectPage/
│   │   └── TaskPage/
│   ├── features/         # Бизнес-функции
│   │   ├── auth/
│   │   ├── task-create/
│   │   └── task-edit/
│   ├── entities/         # Бизнес-сущности
│   │   ├── task/
│   │   ├── project/
│   │   └── user/
│   └── shared/           # Переиспользуемый код
│       ├── api/          # API клиент
│       ├── ui/           # UI компоненты
│       ├── lib/          # Утилиты
│       └── config/       # Конфигурация
├── index.html
├── vite.config.ts        # Конфигурация Vite
├── tailwind.config.js    # Конфигурация Tailwind
├── tsconfig.json         # TypeScript конфигурация
└── package.json
```

## Разработка

### Архитектурные принципы

1. **Feature-Sliced Design** - модульная архитектура
2. **Component-based** - всё является компонентом
3. **Type-safe** - использование TypeScript для типобезопасности
4. **Atomic design** - для UI компонентов в `shared/ui`

### Работа с состоянием

Проект использует **Zustand** для управления состоянием:

```typescript
// Пример создания store
import { create } from 'zustand'

interface TaskStore {
  tasks: Task[]
  addTask: (task: Task) => void
}

export const useTaskStore = create<TaskStore>((set) => ({
  tasks: [],
  addTask: (task) => set((state) => ({
    tasks: [...state.tasks, task]
  })),
}))
```

### Маршрутизация

Используется **React Router v6**:

```typescript
// src/app/providers/RouterProvider.tsx
import { createBrowserRouter } from 'react-router-dom'

const router = createBrowserRouter([
  {
    path: '/',
    element: <HomePage />,
  },
  {
    path: '/projects/:id',
    element: <ProjectPage />,
  },
])
```

### Стилизация

Проект использует **TailwindCSS**:

```tsx
// Пример использования
<button className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600">
  Click me
</button>
```

### Drag & Drop

Для функциональности drag and drop используется **react-dnd**:

```typescript
import { useDrag, useDrop } from 'react-dnd'

const [{ isDragging }, drag] = useDrag(() => ({
  type: 'TASK',
  item: { id: task.id },
  collect: (monitor) => ({
    isDragging: monitor.isDragging(),
  }),
}))
```

## Линтинг и форматирование

### ESLint

```bash
# Проверка
npm run lint

# Автоисправление
npm run lint:fix
```

### Prettier

```bash
# Форматирование всех файлов
npm run format
```

### Конфигурация ESLint

Проект использует:
- `@typescript-eslint` для TypeScript
- `eslint-plugin-react` для React
- `eslint-plugin-react-hooks` для React Hooks
- `eslint-config-prettier` для совместимости с Prettier

## Сборка для production

### Локальная сборка

```bash
npm run build
```

Результат будет в директории `dist/`.

### Оптимизация сборки

Vite автоматически:
- Минифицирует код
- Применяет tree-shaking
- Оптимизирует assets
- Генерирует source maps

### Развертывание

Для развертывания на production:

```bash
# 1. Сборка
npm run build

# 2. Скопировать dist/ на сервер
# Или использовать Docker:
cd ../infra
docker-compose up -d nginx
```

## Работа с API

### API клиент

```typescript
// src/shared/api/client.ts
import axios from 'axios'

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
})

// Interceptor для добавления токена
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('token')
  if (token) {
    config.headers.Authorization = `Bearer ${token}`
  }
  return config
})
```

### Типы

Типы генерируются на основе Pydantic схем backend:

```typescript
// src/shared/types/task.ts
export interface Task {
  id: string
  title: string
  description?: string
  status: TaskStatus
  created_at: string
  updated_at: string
}
```

## Troubleshooting

### Порт уже занят

Измените порт в `vite.config.ts`:

```typescript
export default defineConfig({
  server: {
    port: 3000, // Другой порт
  },
})
```

### Проблемы с CORS

Настройте proxy в `vite.config.ts`:

```typescript
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
      },
    },
  },
})
```

### Ошибки TypeScript

```bash
# Проверка типов
npx tsc --noEmit

# Очистка кеша
rm -rf node_modules/.vite
npm run dev
```

### Проблемы с зависимостями

```bash
# Очистка и переустановка
rm -rf node_modules package-lock.json
npm install
```

## Полезные ссылки

- [React документация](https://react.dev/)
- [Vite документация](https://vitejs.dev/)
- [TailwindCSS документация](https://tailwindcss.com/)
- [React Router документация](https://reactrouter.com/)
- [Zustand документация](https://zustand-demo.pmnd.rs/)
- [Feature-Sliced Design](https://feature-sliced.design/)
