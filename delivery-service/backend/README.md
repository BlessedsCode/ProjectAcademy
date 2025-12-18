# Backend - REST API

## Структура

### Controllers
Контроллеры для обработки HTTP запросов:
- `orderController.js` - Управление заказами
- `courierController.js` - Управление курьерами
- `locationController.js` - Геолокация
- `authController.js` - Аутентификация

### Models
Модели базы данных:
- `Order.js` - Модель заказа
- `Courier.js` - Модель курьера
- `Location.js` - Модель геолокации
- `User.js` - Модель пользователя

### Routes
API маршруты:
- `/api/orders` - Заказы
- `/api/couriers` - Курьеры
- `/api/location` - Геолокация
- `/api/auth` - Аутентификация

### Middleware
- `auth.js` - Проверка аутентификации
- `validation.js` - Валидация данных
- `errorHandler.js` - Обработка ошибок

### Config
- `database.js` - Конфигурация БД
- `server.js` - Конфигурация сервера
- `env.js` - Переменные окружения

## API Endpoints

### Заказы
- `GET /api/orders` - Получить список заказов
- `GET /api/orders/:id` - Получить заказ по ID
- `POST /api/orders` - Создать новый заказ
- `PUT /api/orders/:id/assign` - Назначить заказ курьеру
- `PUT /api/orders/:id/status` - Изменить статус заказа

### Курьеры
- `GET /api/couriers` - Получить список курьеров
- `GET /api/couriers/:id/orders` - Получить заказы курьера

### Геолокация
- `POST /api/location` - Отправить геолокацию
- `GET /api/location/:orderId` - Получить геолокацию заказа

### Аутентификация
- `POST /api/auth/login` - Вход в систему
- `POST /api/auth/register` - Регистрация
- `POST /api/auth/logout` - Выход

## База данных

### Таблицы
1. **orders** - Заказы
   - id, description, address, client_id, courier_id, status, created_at

2. **couriers** - Курьеры
   - id, name, phone, status

3. **locations** - Геолокация
   - id, order_id, latitude, longitude, timestamp

4. **users** - Пользователи
   - id, username, password_hash, role

## Установка
_Будет добавлено_

## Запуск
_Будет добавлено_
