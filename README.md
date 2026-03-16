# LMS API Master: Backend for Educational Platforms

![Python](https://img.shields.io/badge/python-3.11+-blue.svg)
![DRF](https://img.shields.io/badge/DRF-3.15+-red.svg)
![PostgreSQL](https://img.shields.io/badge/postgresql-latest-blue.svg)
![Swagger](https://img.shields.io/badge/swagger-docs-green.svg)

**LMS API Master** — это высокопроизводительное API для управления образовательным контентом. Проект демонстрирует навыки построения сложных REST-сервисов с использованием Django Rest Framework, включая кастомную фильтрацию, многоуровневую систему прав доступа и фоновую обработку данных.

## Ключевой функционал
* **Управление контентом:** Реализован полный цикл CRUD для курсов и уроков.
* **Система прав доступа (Permissions):** Разграничение логики для обычных пользователей, владельцев контента и модераторов.
* **Подписки на контент:** Механизм отслеживания обновлений курсов с автоматическим уведомлением подписчиков через Celery.
* **Интеграция с платежами:** Реализована логика интеграции со Stripe для оплаты доступа к материалам.
* **Автодокументация:** Поддержка интерактивных схем API через Swagger (drf-yasg).

## Технологический стек
* **Core:** Python 3.11+, Django 5.x.
* **API Framework:** Django Rest Framework (ViewSets, Generics).
* **Authentication:** SimpleJWT (Access/Refresh tokens).
* **Async Tasks:** Celery + Redis (обработка очередей).
* **Database:** PostgreSQL.
* **Infrastructure:** Docker (контейнеризация), Git.

## Архитектура
Проект разделен на независимые приложения для упрощения масштабирования:
* `materials/`: Управление образовательным контентом (курсы, уроки).
* `users/`: Расширенная модель пользователя, регистрация и профили.
* `subscriptions/`: Сервис управления подписками.

## Установка и запуск

1. Клонируйте репозиторий:

    git clone https://github.com/AJLbN0H/lms-api-master.git

2. Настройка переменных окружения:
Создайте файл `.env` на основе `.env.example` и укажите параметры БД и API ключи Stripe.

3. Запуск через Docker:

    docker-compose up --build

4. Локальная установка:

    poetry install
    python manage.py migrate
    python manage.py runserver

## API Документация
После запуска сервера интерактивная документация доступна по адресам:
* Swagger UI: `/swagger/`
* ReDoc: `/redoc/`

## Roadmap
* Доведение покрытия кода тестами (pytest) до 90%+.
* Настройка CI/CD пайплайна (GitHub Actions) для автоматического тестирования при пуллах.
* Добавление системы тестирования для студентов внутри платформы.
* Интеграция с Telegram-ботом для мгновенных уведомлений.

## Тестирование
В проекте настроено автоматическое тестирование основных модулей. Текущее покрытие кода составляет **83%** (509 проверенных строк).

Для проверки покрытия выполните команды внутри работающего Docker-контейнера:

    # Установка пакета coverage
    docker-compose exec app pip install pytest-cov

    # Запуск тестов с выводом отчета по неразмеченным строкам
    docker-compose exec app pytest --cov=users --cov=materials --cov-report=term-missing