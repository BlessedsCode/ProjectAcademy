# API Specification

## Базовый URL
```
http://localhost:3000/api
```

## Аутентификация
Все запросы (кроме `/auth/login`) требуют JWT токен в заголовке:
```
Authorization: Bearer <token>
```

---

## Аутентификация

### POST /auth/login
Вход в систему

**Request Body:**
```json
{
  "username": "string",
  "password": "string"
}
```

**Response (200):**
```json
{
  "token": "jwt_token",
  "user": {
    "id": 1,
    "username": "courier1",
    "role": "courier"
  }
}
```

**Response (401):**
```json
{
  "error": "Invalid credentials"
}
```

---

## Заказы

### GET /orders
Получить список всех заказов

**Query Parameters:**
- `status` (optional) - Фильтр по статусу
- `courier_id` (optional) - Фильтр по курьеру

**Response (200):**
```json
{
  "orders": [
    {
      "id": 1,
      "order_number": "ORD-001",
      "description": "Пицца Маргарита, 2 колы",
      "delivery_address": "ул. Ленина, д. 10, кв. 5",
      "client_name": "Иван Иванов",
      "client_phone": "+79991234567",
      "courier_id": 1,
      "status": "assigned",
      "created_at": "2025-12-15T10:30:00Z"
    }
  ]
}
```

### GET /orders/:id
Получить детали заказа по ID

**Response (200):**
```json
{
  "id": 1,
  "order_number": "ORD-001",
  "description": "Пицца Маргарита, 2 колы",
  "delivery_address": "ул. Ленина, д. 10, кв. 5",
  "client_name": "Иван Иванов",
  "client_phone": "+79991234567",
  "courier": {
    "id": 1,
    "full_name": "Петров Петр",
    "phone": "+79997654321"
  },
  "status": "in_progress",
  "restaurant_location": {
    "lat": 55.7558,
    "lng": 37.6173
  },
  "delivery_location": {
    "lat": 55.7512,
    "lng": 37.6184
  },
  "created_at": "2025-12-15T10:30:00Z",
  "updated_at": "2025-12-15T10:45:00Z"
}
```

### POST /orders
Создать новый заказ

**Request Body:**
```json
{
  "description": "string",
  "delivery_address": "string",
  "client_name": "string",
  "client_phone": "string",
  "restaurant_lat": 55.7558,
  "restaurant_lng": 37.6173,
  "delivery_lat": 55.7512,
  "delivery_lng": 37.6184
}
```

**Response (201):**
```json
{
  "id": 1,
  "order_number": "ORD-001",
  "status": "created",
  "message": "Order created successfully"
}
```

### PUT /orders/:id/assign
Назначить заказ курьеру

**Request Body:**
```json
{
  "courier_id": 1
}
```

**Response (200):**
```json
{
  "message": "Order assigned successfully",
  "order_id": 1,
  "courier_id": 1
}
```

### PUT /orders/:id/status
Изменить статус заказа

**Request Body:**
```json
{
  "status": "in_progress"
}
```

**Допустимые статусы:**
- `created`
- `assigned`
- `in_progress`
- `delivered`
- `cancelled`

**Response (200):**
```json
{
  "message": "Status updated successfully",
  "order_id": 1,
  "new_status": "in_progress"
}
```

---

## Курьеры

### GET /couriers
Получить список всех курьеров

**Response (200):**
```json
{
  "couriers": [
    {
      "id": 1,
      "full_name": "Петров Петр",
      "phone": "+79997654321",
      "status": "available"
    }
  ]
}
```

### GET /couriers/:id/orders
Получить заказы конкретного курьера

**Query Parameters:**
- `status` (optional) - Фильтр по статусу

**Response (200):**
```json
{
  "courier_id": 1,
  "orders": [
    {
      "id": 1,
      "order_number": "ORD-001",
      "delivery_address": "ул. Ленина, д. 10, кв. 5",
      "status": "in_progress",
      "created_at": "2025-12-15T10:30:00Z"
    }
  ]
}
```

---

## Геолокация

### POST /location
Отправить данные геолокации (эмуляция)

**Request Body:**
```json
{
  "order_id": 1,
  "latitude": 55.7530,
  "longitude": 37.6180
}
```

**Response (201):**
```json
{
  "message": "Location updated successfully",
  "order_id": 1,
  "timestamp": "2025-12-15T10:50:00Z"
}
```

### GET /location/:orderId
Получить текущую геолокацию заказа

**Response (200):**
```json
{
  "order_id": 1,
  "current_location": {
    "latitude": 55.7530,
    "longitude": 37.6180,
    "timestamp": "2025-12-15T10:50:00Z"
  }
}
```

### GET /location/:orderId/history
Получить историю перемещений

**Response (200):**
```json
{
  "order_id": 1,
  "history": [
    {
      "latitude": 55.7558,
      "longitude": 37.6173,
      "timestamp": "2025-12-15T10:45:00Z"
    },
    {
      "latitude": 55.7530,
      "longitude": 37.6180,
      "timestamp": "2025-12-15T10:50:00Z"
    }
  ]
}
```

---

## Коды ошибок

| Код | Описание |
|-----|----------|
| 200 | OK - Успешный запрос |
| 201 | Created - Ресурс создан |
| 400 | Bad Request - Неверный запрос |
| 401 | Unauthorized - Не авторизован |
| 403 | Forbidden - Доступ запрещен |
| 404 | Not Found - Ресурс не найден |
| 500 | Internal Server Error - Ошибка сервера |

## Примеры использования

### Пример: Курьер получает свои заказы
```bash
curl -X GET "http://localhost:3000/api/couriers/1/orders?status=assigned" \
  -H "Authorization: Bearer <token>"
```

### Пример: Курьер изменяет статус заказа
```bash
curl -X PUT "http://localhost:3000/api/orders/1/status" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"status": "in_progress"}'
```

### Пример: Отправка геолокации
```bash
curl -X POST "http://localhost:3000/api/location" \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "order_id": 1,
    "latitude": 55.7530,
    "longitude": 37.6180
  }'
```
