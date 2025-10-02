## Структура проекта

```
├── src/
│   ├── app/
│   │   ├── App.jsx
│   │   ├── layouts
│   │   │   └── RootLayout.jsx
│   │   ├── main.jsx
│   │   └── routes.jsx
│   ├── entities/
│   ├── features/
│   ├── pages/
│   ├── shared/
│   │   ├── api/
│   │   │   └── client.js
│   │   ├── hooks/
│   │   ├── lib/
│   │   ├── styles/
│   │   │   └── globals.css
│   │   └── ui/
│   │       ├── Button.jsx
│   │       └── Input.jsx
│   └── widgets/
├── .env.example
├── .gitignore
├── index.html
├── Makefile
├── jsconfig.json
├── package.json
├── postcss.congig.js
├── tailwind.config.js
├── vite.config.js
├── yarn.lock
└── README.md
```

## Назначение файлов и директорий
- `app/`
  ~ каркас приложения (вход , роутинг, общие макеты)
- `entities/`
  ~ основные домены (пользователь, проект, задача)
- `features/`
  ~ маленькие действия пользователя (кнопки, формы)
- `pages/`
  ~ страницы по роутам
- `shared/`
  ~ общее: UI-кнопки/инпуты, API клиент, стили, хуки, утилиты
- `widgets/`
  ~ крупные блоки UI (навигационная панель, канбан-доска)
- `.env.example`
  ~ пример переменных окружения проекта
- `.gitignore`
  ~ исключения системы git
- `index.html`
  ~ HTML-шаблон, куда монтируется React (#root)
- `Makefile`
  ~ удобные команды для новичка
- `jsconfig.json`
  ~ алиас @/ → src/
- `package.json`
  ~ зависимости и скрипты
- `postcss.congig.js`
  ~ стили через Tailwind
- `tailwind.config.js`
  ~ стили через Tailwind
- `vite.config.js`
  ~ плагин React
- `yarn.lock`
  ~ зависимости и скрипты
- `README.md` 
  ~ файл с описанием репозитория
