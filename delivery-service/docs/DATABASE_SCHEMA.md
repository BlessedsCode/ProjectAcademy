# Схема базы данных

## ER-диаграмма
_Будет добавлена диаграмма_

## Таблицы

### 1. users (Пользователи)
Таблица для хранения данных пользователей системы.

| Поле | Тип | Описание | Ограничения |
|------|-----|----------|-------------|
| id | INTEGER | Уникальный идентификатор | PRIMARY KEY, AUTO_INCREMENT |
| username | VARCHAR(50) | Имя пользователя | UNIQUE, NOT NULL |
| password_hash | VARCHAR(255) | Хеш пароля | NOT NULL |
| role | ENUM | Роль ('courier', 'admin') | NOT NULL |
| created_at | TIMESTAMP | Дата создания | DEFAULT CURRENT_TIMESTAMP |

### 2. couriers (Курьеры)
Таблица для хранения информации о курьерах.

| Поле | Тип | Описание | Ограничения |
|------|-----|----------|-------------|
| id | INTEGER | Уникальный идентификатор | PRIMARY KEY, AUTO_INCREMENT |
| user_id | INTEGER | Ссылка на пользователя | FOREIGN KEY (users.id) |
| full_name | VARCHAR(100) | ФИО курьера | NOT NULL |
| phone | VARCHAR(20) | Телефон | NOT NULL |
| status | ENUM | Статус ('available', 'busy', 'offline') | DEFAULT 'offline' |
| created_at | TIMESTAMP | Дата регистрации | DEFAULT CURRENT_TIMESTAMP |

### 3. orders (Заказы)
Таблица для хранения информации о заказах.

| Поле | Тип | Описание | Ограничения |
|------|-----|----------|-------------|
| id | INTEGER | Уникальный идентификатор | PRIMARY KEY, AUTO_INCREMENT |
| order_number | VARCHAR(20) | Номер заказа | UNIQUE, NOT NULL |
| description | TEXT | Описание заказа | NOT NULL |
| delivery_address | VARCHAR(255) | Адрес доставки | NOT NULL |
| client_name | VARCHAR(100) | Имя клиента | NOT NULL |
| client_phone | VARCHAR(20) | Телефон клиента | NOT NULL |
| courier_id | INTEGER | Назначенный курьер | FOREIGN KEY (couriers.id), NULL |
| status | ENUM | Статус заказа | NOT NULL |
| restaurant_lat | DECIMAL(10,8) | Широта ресторана | NOT NULL |
| restaurant_lng | DECIMAL(11,8) | Долгота ресторана | NOT NULL |
| delivery_lat | DECIMAL(10,8) | Широта адреса доставки | NOT NULL |
| delivery_lng | DECIMAL(11,8) | Долгота адреса доставки | NOT NULL |
| created_at | TIMESTAMP | Дата создания | DEFAULT CURRENT_TIMESTAMP |
| updated_at | TIMESTAMP | Дата обновления | ON UPDATE CURRENT_TIMESTAMP |

**Возможные статусы заказа:**
- `created` - Создан
- `assigned` - Назначен курьеру
- `in_progress` - В пути
- `delivered` - Доставлен
- `cancelled` - Отменен

### 4. locations (Геолокация)
Таблица для хранения истории перемещений курьеров (эмуляция).

| Поле | Тип | Описание | Ограничения |
|------|-----|----------|-------------|
| id | INTEGER | Уникальный идентификатор | PRIMARY KEY, AUTO_INCREMENT |
| order_id | INTEGER | Ссылка на заказ | FOREIGN KEY (orders.id) |
| latitude | DECIMAL(10,8) | Широта | NOT NULL |
| longitude | DECIMAL(11,8) | Долгота | NOT NULL |
| timestamp | TIMESTAMP | Время фиксации | DEFAULT CURRENT_TIMESTAMP |

### 5. order_status_history (История статусов заказа)
Таблица для хранения истории изменения статусов заказов.

| Поле | Тип | Описание | Ограничения |
|------|-----|----------|-------------|
| id | INTEGER | Уникальный идентификатор | PRIMARY KEY, AUTO_INCREMENT |
| order_id | INTEGER | Ссылка на заказ | FOREIGN KEY (orders.id) |
| status | ENUM | Новый статус | NOT NULL |
| changed_by | INTEGER | Кто изменил | FOREIGN KEY (users.id) |
| timestamp | TIMESTAMP | Время изменения | DEFAULT CURRENT_TIMESTAMP |

## Связи между таблицами

1. **users → couriers** (1:1)
   - Один пользователь может быть одним курьером

2. **couriers → orders** (1:N)
   - Один курьер может иметь много заказов
   - Один заказ может быть назначен только одному курьеру

3. **orders → locations** (1:N)
   - Один заказ может иметь много записей геолокации

4. **orders → order_status_history** (1:N)
   - Один заказ может иметь много записей истории статусов

## Индексы

Для оптимизации запросов рекомендуется создать следующие индексы:

1. `idx_orders_status` на `orders(status)`
2. `idx_orders_courier` на `orders(courier_id)`
3. `idx_locations_order` на `locations(order_id)`
4. `idx_locations_timestamp` на `locations(timestamp)`
5. `idx_order_number` на `orders(order_number)`

## Примечания

- Все координаты хранятся в формате десятичных градусов (WGS 84)
- Геолокация является эмуляцией для учебных целей
- При удалении заказа связанные записи в locations и order_status_history удаляются каскадно
