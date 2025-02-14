# Асинхронная система управления задачами

REST API для асинхронного выполнения задач с использованием Django, DRF и Celery.

## Технологии

- Python 3.11
- Django 5.0
- Django REST Framework
- Celery
- Redis
- JWT Authentication
- PostgreSQL
- Docker

## Установка и запуск

### С использованием Docker

1. Клонируйте репозиторий:
```bash
git clone <repository-url>
cd <project-directory>
```

2. Создайте файл .env на основе .env.example

3. Запустите проект:
```bash
docker-compose up --build
```
Выполнить миграции:
```bash
docker-compose exec web python manage.py migrate
```
Приложение будет доступно по адресу: http://localhost:8000

## API Endpoints

### Аутентификация

- `POST /api/register/` - Регистрация нового пользователя
  ```json
  {
    "username": "user",
    "password": "password",
  }
  ```

- `POST /api/token/` - Получение JWT токена
  ```json
  {
    "username": "user",
    "password": "password"
  }
  ```

### Задачи

- `GET /api/tasks/` - Получение списка задач (с пагинацией)
  - Поддерживает фильтрацию по статусу: `?status=pending`

- `POST /api/tasks/` - Создание новой задачи
  ```json
  {
    "task_type": "sum",
    "input_data": {
        "values" : [1,2,3,10],
        "scheduled_at": "2024-01-18 15:30:00"  
    },
    
  }
  ```
  или
  ```json
  {
    "task_type": "countdown",
    "input_data": {
      "seconds": 30,
      "scheduled_at": "2024-02-20T12:30:00Z"  
    },
    
  }
  ```

- `GET /api/tasks/{id}/` - Получение информации о конкретной задаче

## Планирование задач

Система поддерживает планирование задач на определенное время:

- Время указывается в параметре `scheduled_at`
- Поддерживается московское время (Europe/Moscow)
- Можно использовать следующие форматы:
  - Без указания зоны (считается московским): `"2024-02-20 15:30:00"`
  - С указанием зоны (конвертируется в московское): `"2024-02-20T12:30:00Z"`
- Нельзя запланировать задачу на прошедшее время
- Если время не указано, задача выполняется немедленно

## Ограничения

- Пользователь может иметь не более 5 активных задач одновременно
- Поддерживаются два типа задач:
  - `sum` - сложение чисел
  - `countdown` - обратный отсчет

## Статусы задач

- `pending` - задача создана и ожидает выполнения
- `started` - задача выполняется
- `success` - задача успешно завершена
- `failure` - произошла ошибка при выполнении задачи

## Дополнительные возможности

- Фильтрация задач по статусу
- Автоматическая конвертация времени в московский часовой пояс
- Изоляция задач между пользователями (каждый пользователь видит только свои задачи)
- Пагинация списка задач (20 задач на страницу)
