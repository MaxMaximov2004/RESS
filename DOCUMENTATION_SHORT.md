# MERN Real Estate (RESS) — Краткое руководство

## 📋 Описание

Полнофункциональное веб-приложение для управления объявлениями о недвижимости на стеке MERN (MongoDB, Express, React, Node.js).

### Основные возможности
- ✅ Регистрация и вход (Email/Password + Google OAuth)
- ✅ Создание, редактирование, удаление объявлений
- ✅ Поиск и фильтрация объявлений
- ✅ Управление профилем
- ✅ Загрузка изображений
- ✅ Адаптивный дизайн

---

## 🛠 Технологии

### Backend
- **Node.js** + **Express.js** — серверная часть
- **MongoDB** + **Mongoose** — база данных
- **JWT** — аутентификация
- **bcryptjs** — хеширование паролей

### Frontend
- **React** (v18) — UI библиотека
- **Vite** — сборщик
- **Redux Toolkit** + **redux-persist** — управление состоянием
- **React Router** (v6) — маршрутизация
- **TailwindCSS** — стилизация
- **Firebase** — Google OAuth и хранение изображений

---

## 🚀 Быстрый старт

### 1. Установка зависимостей

```bash
# Установка backend зависимостей
npm install

# Установка frontend зависимостей
cd client
npm install
cd ..
```

### 2. Переменные окружения

Создайте файл `.env` в корне проекта:

```env
MONGO_URL=mongodb://localhost:27017/mern-estate
JWT_SECRET=your_secret_key_here
PORT=3000
```

Создайте файл `client/.env`:

```env
VITE_FIREBASE_API_KEY=your_firebase_api_key
```

### 3. Запуск

**Development режим:**

```bash
# Terminal 1 - Backend (порт 3000)
npm run dev

# Terminal 2 - Frontend (порт 5173)
cd client
npm run dev
```

**Production режим:**

```bash
# Сборка
npm run build

# Запуск
npm start
```

Приложение доступно по адресу: `http://localhost:5173` (dev) или `http://localhost:3000` (production)

---

## 📁 Структура проекта

```
RESS/
├── api/                          # Backend
│   ├── controllers/              # Бизнес-логика
│   │   ├── auth.controller.js    # Аутентификация
│   │   ├── user.controller.js    # Управление пользователями
│   │   └── listing.controller.js # Управление объявлениями
│   ├── models/                   # Модели данных
│   │   ├── user.model.js         # Модель пользователя
│   │   └── listing.model.js      # Модель объявления
│   ├── routes/                   # API маршруты
│   ├── utils/                    # Утилиты
│   │   ├── verifyUser.js         # JWT middleware
│   │   └── error.js              # Обработка ошибок
│   └── index.js                  # Точка входа сервера
│
├── client/                       # Frontend
│   ├── src/
│   │   ├── components/           # React компоненты
│   │   ├── pages/                # Страницы
│   │   ├── redux/                # Redux store
│   │   ├── firebase.js           # Firebase config
│   │   └── App.jsx               # Главный компонент
│   ├── vite.config.js            # Конфигурация Vite
│   └── tailwind.config.js        # Конфигурация Tailwind
│
└── .env                          # Переменные окружения
```

---

## 🔌 Основные API Endpoints

### Аутентификация
```
POST   /api/auth/signup          # Регистрация
POST   /api/auth/signin          # Вход
POST   /api/auth/google          # Google OAuth
GET    /api/auth/signout         # Выход
```

### Пользователи (защищены JWT)
```
PUT    /api/user/update/:id      # Обновить профиль
DELETE /api/user/delete/:id      # Удалить аккаунт
GET    /api/user/listings/:id    # Получить объявления пользователя
GET    /api/user/:id              # Получить пользователя
```

### Объявления
```
POST   /api/listing/create       # Создать объявление (защищен)
PUT    /api/listing/update/:id   # Обновить объявление (защищен)
DELETE /api/listing/delete/:id   # Удалить объявление (защищен)
GET    /api/listing/get/:id      # Получить объявление
GET    /api/listing/get           # Список с фильтрами
```

**Пример запроса с фильтрами:**
```
GET /api/listing/get?type=sale&furnished=true&sort=regularPrice&order=asc&limit=10
```

---

## 🗄 Модели данных

### User (Пользователь)
```javascript
{
  username: String (unique),
  email: String (unique),
  password: String (hashed),
  avatar: String,
  createdAt: Date,
  updatedAt: Date
}
```

### Listing (Объявление)
```javascript
{
  name: String,
  description: String,
  address: String,
  regularPrice: Number,
  discountPrice: Number,
  bedrooms: Number,
  bathrooms: Number,
  furnished: Boolean,
  parking: Boolean,
  type: String ('sale' | 'rent'),
  offer: Boolean,
  imageUrls: Array,
  userRef: String (User ID),
  createdAt: Date,
  updatedAt: Date
}
```

---

## 🔐 Аутентификация

### Схема работы:
1. Пользователь вводит credentials
2. Backend проверяет данные и генерирует JWT токен
3. Токен сохраняется в **HTTP-only cookie** (защита от XSS)
4. Frontend сохраняет данные пользователя в **Redux** + **localStorage** (через redux-persist)
5. Защищенные маршруты проверяют наличие токена в cookie

### Google OAuth:
1. Аутентификация через Firebase на frontend
2. Отправка данных на `/api/auth/google`
3. Backend создает/находит пользователя и выдает JWT

---

## 📄 Страницы приложения

| Путь | Компонент | Описание |
|------|-----------|----------|
| `/` | Home | Главная страница с объявлениями |
| `/sign-up` | SignUp | Регистрация |
| `/sign-in` | SignIn | Вход |
| `/about` | About | О проекте |
| `/search` | Search | Поиск с фильтрами |
| `/listing/:id` | Listing | Просмотр объявления |
| `/profile` | Profile | Профиль (🔒) |
| `/create-listing` | CreateListing | Создание объявления (🔒) |
| `/update-listing/:id` | UpdateListing | Редактирование (🔒) |

🔒 — защищенные маршруты (требуют авторизации)

---

## ⚙️ Настройка Firebase

1. Создайте проект на [Firebase Console](https://console.firebase.google.com/)
2. Включите **Authentication** → **Google Sign-In**
3. Создайте **Storage** для загрузки изображений
4. Скопируйте API Key в `client/.env`:
   ```env
   VITE_FIREBASE_API_KEY=your_api_key
   ```

---

## 🔧 Полезные команды

```bash
# Backend
npm run dev              # Запуск с nodemon (hot reload)
npm start                # Production запуск

# Frontend
cd client
npm run dev              # Dev сервер (Vite)
npm run build            # Production сборка
npm run preview          # Просмотр production сборки
npm run lint             # Проверка кода

# Full Stack
npm run build            # Установка зависимостей + сборка клиента
```

---

## 🌐 Development vs Production

### Development:
- Backend: `http://localhost:3000`
- Frontend: `http://localhost:5173`
- Vite proxy перенаправляет `/api` → backend
- Hot Module Replacement (HMR)

### Production:
- Один сервер: `http://localhost:3000`
- Express обслуживает статику из `client/dist`
- Все маршруты кроме `/api/*` отдают React app

---

## 📝 Примеры использования

### Создание пользователя
```bash
curl -X POST http://localhost:3000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{
    "username": "john_doe",
    "email": "john@example.com",
    "password": "password123"
  }'
```

### Вход
```bash
curl -X POST http://localhost:3000/api/auth/signin \
  -H "Content-Type: application/json" \
  -c cookies.txt \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'
```

### Создание объявления (с cookie)
```bash
curl -X POST http://localhost:3000/api/listing/create \
  -H "Content-Type: application/json" \
  -b cookies.txt \
  -d '{
    "name": "Квартира в центре",
    "description": "Уютная квартира",
    "address": "ул. Примерная, 123",
    "regularPrice": 2000000,
    "discountPrice": 1800000,
    "bedrooms": 2,
    "bathrooms": 1,
    "furnished": true,
    "parking": true,
    "type": "sale",
    "offer": true,
    "imageUrls": ["https://example.com/image.jpg"]
  }'
```

---

## 🐛 Решение проблем

### MongoDB не подключается
```bash
# Проверьте, запущен ли MongoDB
mongod --version

# Или используйте MongoDB Atlas (облачная БД)
# Замените MONGO_URL на connection string из Atlas
```

### Frontend не видит backend API
```bash
# Проверьте vite.config.js
# Должен быть настроен proxy для /api
```

### Ошибки при сборке
```bash
# Очистите кеш и переустановите зависимости
rm -rf node_modules client/node_modules
npm install
cd client && npm install
```

---

## 📚 Дополнительная документация

Для полной технической документации см. **[ТЕХНИЧЕСКАЯ_ДОКУМЕНТАЦИЯ.md](./ТЕХНИЧЕСКАЯ_ДОКУМЕНТАЦИЯ.md)**

---

## 📌 Важные моменты

1. **Безопасность:**
   - JWT токены в HTTP-only cookies
   - Пароли хешируются с bcryptjs
   - Проверка владельца перед изменением данных

2. **Производительность:**
   - Redis для кеширования (TODO)
   - Оптимизация изображений (TODO)
   - Пагинация вместо "Load More" (TODO)

3. **Текущий статус:**
   - Ветка: `feature/Russian_Lang`
   - Версия: 1.0.0
   - Статус: Активная разработка

---

## 👨‍💻 Разработка

Проект открыт для улучшений. Основные направления:
- Email verification
- Восстановление пароля
- Система отзывов
- Интеграция карт
- Тесты (Jest, Cypress)
- Docker контейнеризация

---

**Создано:** 2024
**Стек:** MongoDB + Express + React + Node.js
**Лицензия:** ISC
