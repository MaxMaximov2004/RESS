## Настройка и запуск

### Требования

- Node.js (v18 или выше)
- MongoDB (локально или MongoDB Atlas)
- npm или yarn
- Firebase аккаунт (для OAuth)

### Установка

#### Клонирование репозитория

```bash
git clone <repository-url>
cd RESS
```

#### Установка зависимостей

**Backend:**

```bash
npm install
```

**Frontend:**

```bash
cd client
npm install
cd ..
```

#### Настройка переменных окружения

**Backend (.env в корне):**

```env
MONGO_URL=mongodb://localhost:27017/mern-estate
JWT_SECRET=your_super_secret_jwt_key
PORT=3000
```

**Frontend (client/.env):**

```env
VITE_FIREBASE_API_KEY=your_firebase_api_key
```

#### Настройка MongoDB

**Локально:**

```bash
mongod --dbpath /path/to/data/db
```

**MongoDB Atlas:**

- Создайте кластер
- Получите connection string
- Укажите его в `MONGO_URL`

#### 5. Настройка Firebase

1. Создайте проект в [Firebase Console](https://console.firebase.google.com/)
2. Включите Authentication → Google Sign-In
3. Скопируйте API Key в `VITE_FIREBASE_API_KEY`
4. (Опционально) Настройте Firebase Storage для загрузки изображений

### Запуск в режиме разработки

#### Вариант 1: Раздельный запуск

**Terminal 1 (Backend):**

```bash
npm run dev
# Сервер запустится на http://localhost:3000
```

**Terminal 2 (Frontend):**

```bash
cd client
npm run dev
# Dev сервер запустится на http://localhost:5173
```

#### Вариант 2: Одновременный запуск

```bash
npm run dev
# Можно настроить concurrently в package.json
```

### Production сборка

#### 1. Сборка frontend

```bash
npm run build
# Выполняет:
# - npm install (корневые зависимости)
# - npm install --prefix client (клиентские зависимости)
# - npm run build --prefix client (сборка в client/dist)
```

#### 2. Запуск production сервера

```bash
npm start
# Запускает Node сервер на порту 3000
# Обслуживает статические файлы из client/dist
```

#### 3. Деплой

**Варианты деплоя:**

**Heroku:**

```bash
heroku create your-app-name
git push heroku main
heroku config:set MONGO_URL=<your-mongodb-url>
heroku config:set JWT_SECRET=<your-jwt-secret>
```

**Vercel/Netlify:**

- Frontend можно задеплоить отдельно
- Backend на Heroku/Railway/Render

**VPS (DigitalOcean, AWS EC2):**

```bash
# На сервере
git clone <repo>
npm install
npm run build
pm2 start api/index.js
# Настройте nginx как reverse proxy
```

### Scripts в package.json

**Корневой package.json:**

```json
{
  "scripts": {
    "dev": "nodemon api/index.js", // Backend dev server
    "start": "node api/index.js", // Production server
    "build": "npm install && npm install --prefix client && npm run build --prefix client"
  }
}
```

**Client package.json:**

```json
{
  "scripts": {
    "dev": "vite", // Frontend dev server
    "build": "vite build", // Production build
    "preview": "vite preview", // Preview production build
    "lint": "eslint . --ext js,jsx" // Lint code
  }
}
```

---

## Особенности реализации

### **Development vs Production**

**Development:**

- Frontend на Vite dev server (http://localhost:5173)
- Backend на Express (http://localhost:3000)
- Vite proxy перенаправляет `/api` запросы на backend
- Hot Module Replacement (HMR) для быстрой разработки

**Production:**

- Express обслуживает статические файлы из `client/dist`
- Один домен для frontend и backend
- Все `/api` запросы обрабатываются Express
- SPA routing: все неизвестные маршруты отдают `index.html`

### **Vite Proxy Configuration**

```javascript
// vite.config.js
export default defineConfig({
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:3000",
        secure: false,
      },
    },
  },
});
```

Это позволяет фронтенду делать запросы на `/api/...` без CORS проблем.

### **Переменные окружения**

#### Backend (.env в корне):

```env
MONGO_URL=mongodb://localhost:27017/mern-estate
JWT_SECRET=your_jwt_secret_key_here
PORT=3000
```

#### Frontend (client/.env):

```env
VITE_FIREBASE_API_KEY=your_firebase_api_key
```

**Безопасность:**

- Файлы `.env` добавлены в `.gitignore`
- Секретные ключи не попадают в репозиторий
- В production используются серверные переменные окружения
