# Техническая Документация Проекта MERN Real Estate (RESS)

## Содержание
1. [Обзор проекта](#обзор-проекта)
2. [Технологический стек](#технологический-стек)
3. [Архитектура приложения](#архитектура-приложения)
4. [Backend (API)](#backend-api)
5. [Frontend (Client)](#frontend-client)
6. [База данных](#база-данных)
7. [Аутентификация и безопасность](#аутентификация-и-безопасность)
8. [Настройка и запуск](#настройка-и-запуск)
9. [API Endpoints](#api-endpoints)
10. [Структура компонентов](#структура-компонентов)

---

## Обзор проекта

**MERN Real Estate (RESS)** — это полнофункциональное веб-приложение для управления объявлениями о недвижимости, построенное на основе стека MERN (MongoDB, Express.js, React, Node.js).

### Основные возможности:
- Регистрация и аутентификация пользователей (email/password + Google OAuth)
- Создание, редактирование и удаление объявлений о недвижимости
- Поиск и фильтрация объявлений
- Управление профилем пользователя
- Загрузка изображений объектов недвижимости
- Адаптивный дизайн для всех устройств

---

## Технологический стек

### Backend
- **Node.js** (v18+) — серверная платформа JavaScript
- **Express.js** (v4.19.1) — веб-фреймворк для Node.js
- **MongoDB** — NoSQL база данных
- **Mongoose** (v8.2.3) — ODM для работы с MongoDB
- **JSON Web Token (JWT)** (v9.0.2) — для аутентификации
- **bcryptjs** (v2.4.3) — хеширование паролей
- **cookie-parser** (v1.4.6) — парсинг cookies
- **dotenv** (v16.4.5) — управление переменными окружения
- **nodemon** (v3.1.0) — автоматическая перезагрузка сервера при разработке

### Frontend
- **React** (v18.2.0) — библиотека для построения пользовательских интерфейсов
- **Vite** (v5.2.0) — современный сборщик и dev-сервер
- **React Router DOM** (v6.22.3) — маршрутизация на клиенте
- **Redux Toolkit** (v2.2.2) — управление глобальным состоянием
- **redux-persist** (v6.0.0) — сохранение состояния Redux в localStorage
- **TailwindCSS** (v3.4.1) — CSS-фреймворк для стилизации
- **Firebase** (v10.9.0) — для Google OAuth аутентификации
- **Swiper** (v11.1.0) — слайдер изображений
- **React Icons** (v5.0.1) — библиотека иконок

### Инструменты разработки
- **ESLint** — линтер кода
- **PostCSS** + **Autoprefixer** — обработка CSS
- **Concurrently** (v9.2.1) — запуск нескольких команд одновременно

---

## Архитектура приложения

Проект организован как **монорепозиторий** с двумя основными директориями:

```
RESS/
├── api/                      # Backend сервер
│   ├── controllers/          # Бизнес-логика
│   ├── models/              # Модели данных (Mongoose)
│   ├── routes/              # Определение маршрутов API
│   ├── utils/               # Утилиты (middleware, обработка ошибок)
│   └── index.js             # Точка входа сервера
│
├── client/                   # Frontend приложение
│   ├── src/
│   │   ├── components/      # Переиспользуемые компоненты
│   │   ├── pages/           # Страницы приложения
│   │   ├── redux/           # Redux store и slices
│   │   ├── assets/          # Статические ресурсы
│   │   ├── firebase.js      # Конфигурация Firebase
│   │   ├── App.jsx          # Главный компонент приложения
│   │   └── main.jsx         # Точка входа React
│   ├── dist/                # Скомпилированная production сборка
│   ├── vite.config.js       # Конфигурация Vite
│   └── tailwind.config.js   # Конфигурация TailwindCSS
│
├── .env                      # Переменные окружения (backend)
├── package.json             # Зависимости проекта
└── node_modules/            # Установленные пакеты
```

### Архитектурные паттерны

#### 1. **MVC (Model-View-Controller)**
Backend организован по паттерну MVC:
- **Models** (`api/models/`) — схемы данных Mongoose
- **Controllers** (`api/controllers/`) — бизнес-логика обработки запросов
- **Routes** (`api/routes/`) — определение endpoint'ов и связывание с контроллерами

#### 2. **Component-Based Architecture**
Frontend построен на компонентном подходе React:
- Переиспользуемые UI компоненты
- Страницы как композиция компонентов
- Разделение логики и представления

#### 3. **State Management with Redux**
Глобальное состояние управляется через Redux Toolkit:
- Централизованное хранилище (store)
- Immutable state updates
- Синхронизация с localStorage через redux-persist

---

## Backend (API)

### Структура Backend

#### 1. **Точка входа (api/index.js)**

Главный файл сервера, который:
- Подключается к MongoDB
- Настраивает Express middleware
- Регистрирует маршруты API
- Обслуживает статические файлы фронтенда (в production)
- Обрабатывает ошибки

```javascript
// Основные middleware
app.use(express.json())           // Парсинг JSON
app.use(cookieParser())           // Парсинг cookies

// API маршруты
app.use("/api/user", userRouter)
app.use("/api/auth", authRouter)
app.use("/api/listing", listingRouter)

// Обслуживание React приложения (production)
app.use(express.static(path.join(__dirname, "/client/dist")))
app.get("*", (req, res) => {
  res.sendFile(path.join(__dirname, "client", "dist", "index.html"))
})
```

**Порт сервера:** `3000`

#### 2. **Модели данных (api/models/)**

##### User Model (`user.model.js`)

Модель пользователя с полями:

| Поле | Тип | Описание | Обязательное |
|------|-----|----------|--------------|
| `username` | String | Имя пользователя | Да (unique) |
| `email` | String | Email адрес | Да (unique) |
| `password` | String | Хешированный пароль | Да |
| `avatar` | String | URL аватара | Нет (default URL) |
| `createdAt` | Date | Дата создания | Автоматически |
| `updatedAt` | Date | Дата обновления | Автоматически |

##### Listing Model (`listing.model.js`)

Модель объявления о недвижимости:

| Поле | Тип | Описание | Обязательное |
|------|-----|----------|--------------|
| `name` | String | Название объекта | Да |
| `description` | String | Описание | Да |
| `address` | String | Адрес | Да |
| `regularPrice` | Number | Обычная цена | Да |
| `discountPrice` | Number | Цена со скидкой | Да |
| `bedrooms` | Number | Количество спален | Да |
| `bathrooms` | Number | Количество ванных комнат | Да |
| `furnished` | Boolean | Меблирован | Да |
| `parking` | Boolean | Наличие парковки | Да |
| `type` | String | Тип (rent/sale) | Да |
| `offer` | Boolean | Есть ли специальное предложение | Да |
| `imageUrls` | Array | Массив URL изображений | Да |
| `userRef` | String | ID создателя объявления | Да |
| `createdAt` | Date | Дата создания | Автоматически |
| `updatedAt` | Date | Дата обновления | Автоматически |

**Примечание:** В модели есть дублирование поля `offer` (строки 55-63), что является потенциальной ошибкой.

#### 3. **Контроллеры (api/controllers/)**

##### Auth Controller (`auth.controller.js`)

Отвечает за аутентификацию пользователей:

| Функция | Метод | Endpoint | Описание |
|---------|-------|----------|----------|
| `signup` | POST | `/api/auth/signup` | Регистрация нового пользователя |
| `signin` | POST | `/api/auth/signin` | Вход в систему (email/password) |
| `google` | POST | `/api/auth/google` | OAuth аутентификация через Google |
| `signout` | GET | `/api/auth/signout` | Выход из системы |

**Особенности:**
- Пароли хешируются с помощью bcryptjs (salt rounds: 10)
- JWT токен сохраняется в HTTP-only cookie
- Google OAuth создает пользователя автоматически, если не существует
- При Google OAuth генерируется случайный пароль

##### User Controller (`user.controller.js`)

Управление пользователями и их данными:

| Функция | Описание |
|---------|----------|
| `updateUser` | Обновление данных пользователя (username, email, password, avatar) |
| `deleteteUser` | Удаление аккаунта и всех связанных объявлений |
| `getUserListing` | Получение всех объявлений конкретного пользователя |
| `getUser` | Получение информации о пользователе по ID |

**Безопасность:**
- Проверка, что пользователь может изменять только свои данные
- При удалении пользователя каскадно удаляются все его объявления
- Пароли не возвращаются в ответах API

##### Listing Controller (`listing.controller.js`)

Управление объявлениями о недвижимости:

| Функция | Описание |
|---------|----------|
| `createListing` | Создание нового объявления |
| `updateListing` | Обновление существующего объявления |
| `deleteListing` | Удаление объявления |
| `getListing` | Получение одного объявления по ID |
| `getListings` | Получение списка объявлений с фильтрацией и пагинацией |

#### 4. **Маршруты (api/routes/)**

##### Auth Routes (`auth.route.js`)
```javascript
POST   /api/auth/signup     // Регистрация
POST   /api/auth/signin     // Вход
POST   /api/auth/google     // Google OAuth
GET    /api/auth/signout    // Выход
```

##### User Routes (`user.route.js`)
```javascript
PUT    /api/user/update/:id          // Обновление пользователя (защищен)
DELETE /api/user/delete/:id          // Удаление пользователя (защищен)
GET    /api/user/listings/:id        // Получение объявлений пользователя (защищен)
GET    /api/user/:id                 // Получение пользователя
```

##### Listing Routes (`listing.route.js`)
```javascript
POST   /api/listing/create           // Создание объявления (защищен)
DELETE /api/listing/delete/:id       // Удаление объявления (защищен)
PUT    /api/listing/update/:id       // Обновление объявления (защищен)
GET    /api/listing/get/:id          // Получение объявления
GET    /api/listing/get              // Получение списка с фильтрами
```

#### 5. **Утилиты (api/utils/)**

##### Error Handler (`error.js`)

Функция для создания стандартизированных ошибок:
```javascript
errorHandler(statusCode, message)
```

##### Verify Token Middleware (`verifyUser.js`)

Middleware для проверки JWT токена:
- Извлекает токен из cookie `access_token`
- Проверяет валидность токена
- Добавляет данные пользователя в `req.user`
- Возвращает 401 (Unauthorized) если токен отсутствует
- Возвращает 403 (Forbidden) если токен невалиден

---

## Frontend (Client)

### Структура Frontend

#### 1. **Точка входа (client/src/main.jsx)**

Инициализирует React приложение:
- Оборачивает приложение в Redux Provider
- Подключает PersistGate для сохранения состояния
- Монтирует приложение в DOM

#### 2. **Маршрутизация (client/src/App.jsx)**

Определяет все маршруты приложения:

| Путь | Компонент | Защищен | Описание |
|------|-----------|---------|----------|
| `/` | Home | Нет | Главная страница |
| `/sign-up` | SignUp | Нет | Страница регистрации |
| `/sign-in` | SignIn | Нет | Страница входа |
| `/about` | About | Нет | О проекте |
| `/listing/:listingId` | Listing | Нет | Просмотр объявления |
| `/search` | Search | Нет | Поиск объявлений |
| `/profile` | Profile | Да | Профиль пользователя |
| `/create-listing` | CreateListing | Да | Создание объявления |
| `/update-listing/:listingId` | UpdateListing | Да | Редактирование объявления |

**Защищенные маршруты** используют компонент `PrivateRoute`, который проверяет наличие авторизованного пользователя в Redux store.

#### 3. **Redux State Management**

##### Store Configuration (`redux/store.js`)

Конфигурация Redux store:
- Использует `redux-persist` для сохранения в localStorage
- Отключает проверку сериализуемости для корректной работы с persist
- Ключ хранения: `root`
- Версия: 1

##### User Slice (`redux/user/userSlice.js`)

Управляет состоянием пользователя:

**Состояние:**
```javascript
{
  currentUser: null,    // Данные текущего пользователя
  error: null,          // Ошибки
  loading: false        // Индикатор загрузки
}
```

**Actions (экшены):**

**Вход:**
- `signInStart` — начало входа (loading = true)
- `signInSuccess` — успешный вход (сохраняет данные пользователя)
- `signInFailure` — ошибка входа

**Обновление:**
- `updateUserStart` — начало обновления
- `updateuserSuccess` — успешное обновление
- `updateUserFailure` — ошибка обновления

**Удаление:**
- `deleteUserStart` — начало удаления
- `deleteUserSuccess` — успешное удаление (очищает currentUser)
- `deleteUserFailure` — ошибка удаления

**Выход:**
- `signOutUserStart` — начало выхода
- `signOutUserSuccess` — успешный выход
- `signOutUserFailure` — ошибка выхода

#### 4. **Компоненты (client/src/components/)**

##### Header (`Header.jsx`)
Навигационный хедер:
- Логотип и навигационные ссылки
- Поисковая строка
- Отображение аватара пользователя или кнопки входа
- Адаптивное меню для мобильных устройств

##### OAuth (`OAuth.jsx`)
Кнопка для аутентификации через Google:
- Интегрируется с Firebase Authentication
- Использует Google Auth Provider
- Отправляет данные на backend endpoint `/api/auth/google`
- Обновляет Redux state при успешной аутентификации

##### PrivateRoute (`PrivateRoute.jsx`)
Компонент защиты маршрутов:
- Проверяет наличие `currentUser` в Redux store
- Перенаправляет на `/sign-in` если пользователь не авторизован
- Использует `<Outlet />` для рендеринга дочерних маршрутов

##### ListingItem (`ListingItem.jsx`)
Карточка объявления:
- Отображает основную информацию об объекте
- Показывает первое изображение
- Отображает цену, адрес, количество комнат
- Использется на главной странице и в результатах поиска

##### Contact (`Contact.jsx`)
Форма связи с владельцем объявления:
- Загружает информацию о владельце по userRef
- Предоставляет форму для отправки сообщения
- Формирует mailto ссылку для email клиента

##### Spinner (`Spinner.jsx`)
Индикатор загрузки:
- Анимированный спиннер для отображения процессов загрузки

#### 5. **Страницы (client/src/pages/)**

##### Home (`Home.jsx`)
Главная страница:
- Hero секция с призывом к действию
- Последние объявления с предложениями
- Объявления на продажу
- Объявления в аренду
- Использует Swiper для слайдеров

##### SignIn (`SignIn.jsx`)
Страница входа:
- Форма с полями email и password
- Кнопка OAuth (Google)
- Обработка ошибок
- Редирект на главную после успешного входа

##### SignUp (`SignUp.jsx`)
Страница регистрации:
- Форма с полями username, email, password
- Кнопка OAuth (Google)
- Валидация полей
- Редирект на страницу входа после регистрации

##### Profile (`Profile.jsx`)
Личный кабинет пользователя:
- Редактирование данных профиля (username, email, password, avatar)
- Загрузка нового аватара (через Firebase Storage)
- Просмотр своих объявлений
- Удаление объявлений
- Кнопка создания нового объявления
- Выход из системы
- Удаление аккаунта

##### CreateListing (`CreateListing.jsx`)
Создание объявления:
- Многошаговая форма с валидацией
- Загрузка множественных изображений (до 6 шт)
- Поля для всех характеристик объекта
- Переключатели для типа (продажа/аренда)
- Опции: парковка, меблирован, специальное предложение
- Расчет цены со скидкой

##### UpdateListing (`UpdateListing.jsx`)
Редактирование объявления:
- Аналогична CreateListing
- Предзаполненные поля текущими данными
- Возможность добавления/удаления изображений
- Сохранение изменений

##### Listing (`Listing.jsx`)
Детальный просмотр объявления:
- Полная информация об объекте
- Галерея изображений (Swiper слайдер)
- Все характеристики (bedrooms, bathrooms, etc.)
- Кнопка связи с владельцем
- Иконки для удобств (парковка, мебель)
- Показ на карте (опционально)

##### Search (`Search.jsx`)
Поиск и фильтрация:
- Поисковая строка
- Фильтры:
  - Тип (продажа/аренда/все)
  - Парковка
  - Меблировано
  - Предложение
- Сортировка:
  - По дате (новые/старые)
  - По цене (возрастание/убывание)
- Пагинация (Load More)
- Отображение результатов списком

##### About (`About.jsx`)
Информация о проекте:
- Описание сервиса
- Миссия компании
- Преимущества использования платформы

#### 6. **Firebase Integration (client/src/firebase.js)**

Конфигурация Firebase для OAuth:
```javascript
{
  apiKey: VITE_FIREBASE_API_KEY,      // Из переменных окружения
  authDomain: "mern-estate-f8f18.firebaseapp.com",
  projectId: "mern-estate-f8f18",
  storageBucket: "mern-estate-f8f18.appspot.com",
  messagingSenderId: "304767473129",
  appId: "1:304767473129:web:e67df8378b70567db42778"
}
```

Firebase используется для:
- Google OAuth аутентификации
- Хранения загруженных изображений (Storage)

#### 7. **Styling с TailwindCSS**

Проект использует utility-first подход:
- Все стили применяются через классы Tailwind
- Конфигурация: `tailwind.config.js`
- PostCSS обработка
- Autoprefixer для кроссбраузерности
- Адаптивный дизайн (mobile-first)

---

## База данных

### MongoDB Structure

База данных состоит из двух основных коллекций:

#### 1. **users** (Пользователи)
```javascript
{
  _id: ObjectId,
  username: "john_doe",
  email: "john@example.com",
  password: "$2a$10$hashed_password...",  // Bcrypt hash
  avatar: "https://url-to-avatar.com",
  createdAt: ISODate("2024-01-01T00:00:00.000Z"),
  updatedAt: ISODate("2024-01-15T00:00:00.000Z")
}
```

**Индексы:**
- `username` (unique)
- `email` (unique)

#### 2. **listings** (Объявления)
```javascript
{
  _id: ObjectId,
  name: "Современная квартира в центре",
  description: "Уютная 2-комнатная квартира...",
  address: "ул. Примерная, д. 123",
  regularPrice: 2000000,
  discountPrice: 1800000,
  bedrooms: 2,
  bathrooms: 1,
  furnished: true,
  parking: true,
  type: "sale",  // "sale" или "rent"
  offer: true,
  imageUrls: [
    "https://url-to-image1.com",
    "https://url-to-image2.com"
  ],
  userRef: "user_id",  // Ссылка на пользователя
  createdAt: ISODate("2024-01-01T00:00:00.000Z"),
  updatedAt: ISODate("2024-01-15T00:00:00.000Z")
}
```

**Индексы:**
- `userRef` (для быстрого поиска объявлений пользователя)

### Связи между коллекциями

- **One-to-Many**: Один пользователь может иметь множество объявлений
- Связь через поле `userRef` в коллекции listings
- При удалении пользователя каскадно удаляются все его объявления

---

## Аутентификация и безопасность

### 1. **JWT Authentication Flow**

#### Регистрация:
1. Пользователь отправляет данные на `/api/auth/signup`
2. Пароль хешируется с помощью bcryptjs (10 salt rounds)
3. Создается новая запись в базе данных
4. Возвращается сообщение об успехе

#### Вход:
1. Пользователь отправляет email и password на `/api/auth/signin`
2. Проверяется существование пользователя
3. Сравнивается хеш пароля с помощью bcryptjs
4. Генерируется JWT токен с payload `{ id: user._id }`
5. Токен сохраняется в HTTP-only cookie с именем `access_token`
6. Возвращаются данные пользователя (без пароля)

#### Google OAuth:
1. Пользователь проходит аутентификацию через Firebase (фронтенд)
2. Данные отправляются на `/api/auth/google`
3. Проверяется существование пользователя по email
4. Если пользователь существует — выполняется вход
5. Если нет — создается новый пользователь с:
   - Сгенерированным username (на основе имени + случайные символы)
   - Случайным паролем
   - Avatar из Google аккаунта
6. Генерируется JWT и сохраняется в cookie

#### Защищенные запросы:
1. Middleware `verifyToken` извлекает токен из cookie
2. Проверяет валидность и расшифровывает payload
3. Добавляет данные пользователя в `req.user`
4. Передает управление следующему middleware/контроллеру

#### Выход:
1. Запрос на `/api/auth/signout`
2. Cookie `access_token` очищается
3. Фронтенд очищает Redux state

### 2. **Безопасность**

#### Backend:
- **HTTP-only cookies** — защита от XSS атак (JavaScript не может получить токен)
- **Bcrypt hashing** — необратимое хеширование паролей
- **JWT Secret** — хранится в переменных окружения
- **Authorization checks** — пользователь может изменять только свои данные
- **Error handling** — стандартизированная обработка ошибок
- **Mongoose validation** — валидация на уровне схемы

#### Frontend:
- **Redux persist encryption** — состояние сохраняется в localStorage (можно добавить шифрование)
- **Private routes** — защита маршрутов от неавторизованного доступа
- **CORS** — Vite proxy в development, один домен в production
- **Environment variables** — чувствительные данные не в коде

### 3. **Переменные окружения**

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

---

## Настройка и запуск

### Требования
- Node.js (v18 или выше)
- MongoDB (локально или MongoDB Atlas)
- npm или yarn
- Firebase аккаунт (для OAuth)

### Установка

#### 1. Клонирование репозитория
```bash
git clone <repository-url>
cd RESS
```

#### 2. Установка зависимостей

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

#### 3. Настройка переменных окружения

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

#### 4. Настройка MongoDB

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
    "dev": "nodemon api/index.js",          // Backend dev server
    "start": "node api/index.js",           // Production server
    "build": "npm install && npm install --prefix client && npm run build --prefix client"
  }
}
```

**Client package.json:**
```json
{
  "scripts": {
    "dev": "vite",                          // Frontend dev server
    "build": "vite build",                  // Production build
    "preview": "vite preview",              // Preview production build
    "lint": "eslint . --ext js,jsx"         // Lint code
  }
}
```

---

## API Endpoints

### Authentication Endpoints

#### POST /api/auth/signup
Регистрация нового пользователя

**Request Body:**
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "securepassword123"
}
```

**Response (201):**
```json
{
  "message": "User created successfully"
}
```

**Errors:**
- 400: Validation error (дублирующий email/username)

---

#### POST /api/auth/signin
Вход в систему

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "securepassword123"
}
```

**Response (200):**
```json
{
  "_id": "user_id",
  "username": "john_doe",
  "email": "john@example.com",
  "avatar": "https://...",
  "createdAt": "2024-01-01T00:00:00.000Z",
  "updatedAt": "2024-01-01T00:00:00.000Z"
}
```

**Cookies:**
- `access_token`: JWT токен (httpOnly)

**Errors:**
- 404: User not found / Invalid credentials

---

#### POST /api/auth/google
OAuth аутентификация через Google

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "photo": "https://google-avatar-url.com"
}
```

**Response (200):**
```json
{
  "_id": "user_id",
  "username": "johndoe1a2b",
  "email": "john@example.com",
  "avatar": "https://google-avatar-url.com",
  "createdAt": "2024-01-01T00:00:00.000Z",
  "updatedAt": "2024-01-01T00:00:00.000Z"
}
```

**Cookies:**
- `access_token`: JWT токен (httpOnly)

---

#### GET /api/auth/signout
Выход из системы

**Response (200):**
```json
{
  "message": "User signed out successfully"
}
```

**Действия:**
- Очищает cookie `access_token`

---

### User Endpoints

#### PUT /api/user/update/:id
Обновление данных пользователя (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Request Body:**
```json
{
  "username": "new_username",
  "email": "newemail@example.com",
  "password": "newpassword123",
  "avatar": "https://new-avatar-url.com"
}
```

**Response (200):**
```json
{
  "_id": "user_id",
  "username": "new_username",
  "email": "newemail@example.com",
  "avatar": "https://new-avatar-url.com",
  "createdAt": "2024-01-01T00:00:00.000Z",
  "updatedAt": "2024-01-15T00:00:00.000Z"
}
```

**Errors:**
- 401: Unauthorized (не тот пользователь)
- 403: Forbidden (невалидный токен)

---

#### DELETE /api/user/delete/:id
Удаление аккаунта (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Response (200):**
```json
"User has been deleted Successfully"
```

**Действия:**
- Удаляет пользователя
- Удаляет все объявления пользователя
- Очищает cookie `access_token`

**Errors:**
- 401: Unauthorized

---

#### GET /api/user/listings/:id
Получение объявлений пользователя (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Response (200):**
```json
[
  {
    "_id": "listing_id",
    "name": "Квартира",
    "description": "...",
    "address": "...",
    "regularPrice": 2000000,
    "discountPrice": 1800000,
    "bedrooms": 2,
    "bathrooms": 1,
    "furnished": true,
    "parking": true,
    "type": "sale",
    "offer": true,
    "imageUrls": ["url1", "url2"],
    "userRef": "user_id",
    "createdAt": "...",
    "updatedAt": "..."
  }
]
```

**Errors:**
- 401: Unauthorized (не тот пользователь)

---

#### GET /api/user/:id
Получение информации о пользователе

**Response (200):**
```json
{
  "_id": "user_id",
  "username": "john_doe",
  "email": "john@example.com",
  "avatar": "https://...",
  "createdAt": "...",
  "updatedAt": "..."
}
```

**Errors:**
- 404: User not found

---

### Listing Endpoints

#### POST /api/listing/create
Создание объявления (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Request Body:**
```json
{
  "name": "Современная квартира",
  "description": "Уютная 2-комнатная квартира в центре города",
  "address": "ул. Примерная, д. 123",
  "regularPrice": 2000000,
  "discountPrice": 1800000,
  "bedrooms": 2,
  "bathrooms": 1,
  "furnished": true,
  "parking": true,
  "type": "sale",
  "offer": true,
  "imageUrls": [
    "https://image1.com",
    "https://image2.com"
  ]
}
```

**Response (201):**
```json
{
  "_id": "listing_id",
  "name": "Современная квартира",
  // ... все поля из запроса
  "userRef": "user_id",
  "createdAt": "2024-01-01T00:00:00.000Z",
  "updatedAt": "2024-01-01T00:00:00.000Z"
}
```

**Errors:**
- 401/403: Unauthorized

---

#### PUT /api/listing/update/:id
Обновление объявления (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Request Body:**
```json
{
  "name": "Обновленное название",
  "regularPrice": 2100000
  // ... другие поля для обновления
}
```

**Response (200):**
```json
{
  "_id": "listing_id",
  // ... обновленные данные
}
```

**Errors:**
- 401: Unauthorized (не владелец объявления)

---

#### DELETE /api/listing/delete/:id
Удаление объявления (защищен)

**Headers:**
- Cookie: `access_token=<jwt_token>`

**Response (200):**
```json
{
  "message": "Listing deleted successfully"
}
```

**Errors:**
- 401: Unauthorized (не владелец)
- 404: Listing not found

---

#### GET /api/listing/get/:id
Получение одного объявления

**Response (200):**
```json
{
  "_id": "listing_id",
  "name": "Современная квартира",
  "description": "...",
  // ... все поля объявления
}
```

**Errors:**
- 404: Listing not found

---

#### GET /api/listing/get
Получение списка объявлений с фильтрацией

**Query Parameters:**
- `limit` (number): Количество результатов (default: 9)
- `startIndex` (number): Пагинация (default: 0)
- `offer` (boolean): Фильтр по специальным предложениям
- `furnished` (boolean): Фильтр по меблировке
- `parking` (boolean): Фильтр по парковке
- `type` ('sale'|'rent'|'all'): Тип объявления
- `searchTerm` (string): Поиск по названию/описанию
- `sort` (string): Поле для сортировки (default: 'createdAt')
- `order` ('asc'|'desc'): Направление сортировки (default: 'desc')

**Пример запроса:**
```
GET /api/listing/get?type=sale&furnished=true&sort=regularPrice&order=asc&limit=10
```

**Response (200):**
```json
[
  {
    "_id": "listing_id_1",
    "name": "Квартира 1",
    // ...
  },
  {
    "_id": "listing_id_2",
    "name": "Квартира 2",
    // ...
  }
]
```

---

## Структура компонентов

### Иерархия компонентов

```
App
├── Header (на всех страницах)
│   ├── Logo
│   ├── Search Form
│   └── Navigation
│       ├── Home Link
│       ├── About Link
│       └── Profile/SignIn Link
│
├── Routes
│   ├── Home
│   │   ├── Hero Section
│   │   ├── Swiper (recent offers)
│   │   ├── ListingItem[] (offers)
│   │   ├── ListingItem[] (rent)
│   │   └── ListingItem[] (sale)
│   │
│   ├── SignIn
│   │   ├── Form
│   │   └── OAuth
│   │
│   ├── SignUp
│   │   ├── Form
│   │   └── OAuth
│   │
│   ├── About
│   │   └── Info Sections
│   │
│   ├── Search
│   │   ├── Sidebar (filters)
│   │   └── Results
│   │       └── ListingItem[]
│   │
│   ├── Listing
│   │   ├── Swiper (images)
│   │   ├── Listing Details
│   │   └── Contact
│   │       └── Contact Form
│   │
│   └── PrivateRoute
│       ├── Profile
│       │   ├── User Info Form
│       │   ├── Avatar Upload
│       │   ├── My Listings
│       │   │   └── ListingItem[]
│       │   └── Actions (Sign Out, Delete Account)
│       │
│       ├── CreateListing
│       │   ├── Listing Form
│       │   └── Image Upload
│       │
│       └── UpdateListing
│           ├── Listing Form (prefilled)
│           └── Image Upload
```

### Переиспользуемые компоненты

1. **Header** — используется на всех страницах
2. **OAuth** — используется на SignIn и SignUp
3. **ListingItem** — используется на Home, Search, Profile
4. **Contact** — используется на странице Listing
5. **Spinner** — используется во время загрузки данных
6. **PrivateRoute** — обертка для защищенных маршрутов

---

## Потоки данных

### 1. Аутентификация (Email/Password)

```
SignIn Page
  ↓ (submit form)
API: POST /api/auth/signin
  ↓ (validate credentials)
Database: Find user & compare password
  ↓ (generate JWT)
Response: User data + Set cookie
  ↓ (dispatch action)
Redux: signInSuccess(userData)
  ↓ (persist)
LocalStorage: Save state
  ↓ (navigate)
Redirect to Profile
```

### 2. Создание объявления

```
CreateListing Page
  ↓ (upload images)
Firebase Storage
  ↓ (get URLs)
State: imageUrls[]
  ↓ (submit form)
API: POST /api/listing/create
  ↓ (verify token)
Middleware: verifyToken
  ↓ (create document)
Database: Create listing with userRef
  ↓ (return created listing)
Response: Listing data
  ↓ (navigate)
Redirect to Listing page
```

### 3. Поиск объявлений

```
Search Page
  ↓ (user inputs filters)
Local State: searchParams
  ↓ (submit)
Update URL query params
  ↓ (fetch)
API: GET /api/listing/get?filters...
  ↓ (query database)
Database: Find with filters & sort
  ↓ (return results)
Response: Listings[]
  ↓ (render)
Display ListingItem components
  ↓ (load more)
Update startIndex & refetch
```

### 4. Обновление профиля

```
Profile Page
  ↓ (edit fields)
Local State: formData
  ↓ (submit)
Redux: updateUserStart()
  ↓ (API request)
API: PUT /api/user/update/:id
  ↓ (verify ownership)
Middleware: Check req.user.id === params.id
  ↓ (update database)
Database: Update user document
  ↓ (return updated user)
Response: Updated user data
  ↓ (dispatch)
Redux: updateuserSuccess(userData)
  ↓ (persist)
LocalStorage: Save updated state
```

---

## Особенности реализации

### 1. **Development vs Production**

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

### 2. **Vite Proxy Configuration**

```javascript
// vite.config.js
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        secure: false
      }
    }
  }
})
```

Это позволяет фронтенду делать запросы на `/api/...` без CORS проблем.

### 3. **Cookie-Based Authentication**

Преимущества использования cookies вместо localStorage:
- **Безопасность:** httpOnly cookies не доступны для JavaScript (защита от XSS)
- **Автоматическая отправка:** браузер автоматически включает cookies в запросы
- **CSRF защита:** можно добавить sameSite атрибут

### 4. **Redux Persist**

Состояние пользователя сохраняется в localStorage:
- При перезагрузке страницы пользователь остается авторизованным
- Ключ хранения: `persist:root`
- Можно добавить encryption для дополнительной безопасности

### 5. **Image Upload Flow**

1. Пользователь выбирает файлы в форме
2. Файлы загружаются в Firebase Storage
3. Получаются публичные URL
4. URL сохраняются в массив `imageUrls`
5. При создании объявления массив отправляется на backend
6. В БД сохраняются только URL, не сами файлы

### 6. **Error Handling**

**Backend:**
```javascript
// Централизованный error handler
app.use((err, req, res, next) => {
  const statusCode = err.statusCode || 500
  const message = err.message || "Internal Server Error!"
  return res.status(statusCode).json({
    success: false,
    statusCode,
    message,
  })
})
```

**Frontend:**
```javascript
try {
  const res = await fetch('/api/...')
  const data = await res.json()
  if (!res.ok) {
    // Обработка ошибки
    setError(data.message)
  }
} catch (error) {
  setError(error.message)
}
```

---

## Возможные улучшения

### Безопасность
1. **CSRF Protection** — добавить CSRF токены
2. **Rate Limiting** — ограничение количества запросов
3. **Input Validation** — валидация на backend (express-validator)
4. **Helmet.js** — HTTP заголовки безопасности
5. **HTTPS** — обязательно в production

### Функциональность
1. **Email Verification** — подтверждение email при регистрации
2. **Password Reset** — восстановление пароля
3. **Pagination** — пагинация вместо "Load More"
4. **Advanced Search** — поиск по цене, площади, локации
5. **Favorites** — избранные объявления
6. **Messaging** — внутренняя система сообщений
7. **Reviews** — отзывы о объектах/пользователях
8. **Map Integration** — интеграция карт (Google Maps, Mapbox)

### Производительность
1. **Caching** — Redis для кеширования запросов
2. **CDN** — раздача статики через CDN
3. **Image Optimization** — сжатие изображений
4. **Lazy Loading** — отложенная загрузка изображений
5. **Code Splitting** — разделение бундла

### DevOps
1. **CI/CD** — автоматизация деплоя (GitHub Actions)
2. **Docker** — контейнеризация приложения
3. **Logging** — централизованное логирование (Winston, Morgan)
4. **Monitoring** — мониторинг (PM2, New Relic)
5. **Testing** — unit и integration тесты (Jest, Cypress)

### UX/UI
1. **Dark Mode** — темная тема
2. **Internationalization (i18n)** — мультиязычность
3. **Accessibility** — улучшение доступности (ARIA)
4. **Progressive Web App** — PWA возможности
5. **Skeleton Screens** — скелетоны вместо спиннеров

---

## Известные проблемы

1. **Дублирование поля `offer`** в `listing.model.js` (строки 55-63)
2. **Отсутствие валидации** на backend (только Mongoose схемы)
3. **Hardcoded Firebase config** — публичные данные в коде
4. **Отсутствие пагинации** — используется "Load More"
5. **Нет обработки больших изображений** — могут быть проблемы с производительностью
6. **Cookie без secure flag** — должен быть включен в production
7. **Отсутствие логирования** — нет системы логов
8. **Нет тестов** — отсутствуют автоматические тесты

---

## Заключение

MERN Real Estate — это полноценное, современное веб-приложение, демонстрирующее лучшие практики разработки на стеке MERN. Проект включает аутентификацию, CRUD операции, поиск с фильтрацией, интеграцию с внешними сервисами (Firebase) и современный UI с TailwindCSS.

Архитектура проекта позволяет легко масштабировать приложение и добавлять новые функции. Код организован, следует принципам разделения ответственности и готов для дальнейшего развития.

---

**Дата создания документации:** 2024
**Версия проекта:** 1.0.0
**Статус:** Активная разработка (feature/Russian_Lang branch)
