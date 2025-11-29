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
  "imageUrls": ["https://image1.com", "https://image2.com"]
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
  "_id": "listing_id"
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
  "description": "..."
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
    "name": "Квартира 1"
    // ...
  },
  {
    "_id": "listing_id_2",
    "name": "Квартира 2"
    // ...
  }
]
```

---
