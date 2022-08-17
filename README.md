## YaMDb API

![yamdb workflow](https://github.com/mikhailsoldatkin/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

[http://51.250.106.141/api/v1/](http://51.250.106.141/api/v1/)

Документация: [http://51.250.106.141/redoc/](http://51.250.106.141/redoc/)

#### API для  проекта "YaMDb"

YaMDb собирает отзывы (Reviews) пользователей на произведения (Titles). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий (Categories) может быть расширен Администратором.
Произведению может быть присвоен жанр (Genre) из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только Администратор.
Пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку (Score) в диапазоне от 1 до 10; из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (Raiting). На одно произведение пользователь может оставить только один отзыв.
Для авторизации пользователей используется код подтверждения высылаемый на email. Для аутентификации пользователей используются JWT-токены.

### Технологии:

Python, Django, Django Rest Framework, Simple JWT, Docker, Gunicorn, NGINX, PostgreSQL, Continuous Integration, Continuous Deployment

### Запуск проекта на локальной машине:

- Клонировать репозиторий, перейти в директорию infra:
```
git clone https://github.com/mikhailsoldatkin/yamdb_final.git

cd infra
```

- В директории infra файл example.env переименовать в .env и заполнить своими данными:
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=password   # укажите свой пароль
DB_HOST=db
DB_PORT=5432
SECRET_KEY = 'your secret key'
```

- Создать и запустить контейнеры Docker:
```
docker-compose up -d
```

- Выполнить миграции:
```
docker-compose exec web python manage.py migrate
```

- Создать суперпользователя:
```
docker-compose exec web python manage.py createsuperuser
```

- Собрать статику:
```
docker-compose exec web python manage.py collectstatic
```

- Наполнить базу данных содержимым из файла fixtures.json:
```
docker-compose exec web python manage.py loaddata fixtures.json 
```

### После запуска проект будут доступен по адресу:

[http://localhost/api/v1/](http://localhost/api/v1/)

Документация будет доступна по адресу: [http://localhost/redoc/](http://localhost/redoc/). В ней описаны возможные запросы к API и структура ожидаемых ответов. Для каждого запроса указаны уровни прав доступа: пользовательские роли, которым разрешён запрос.

- Остановить и удалить контейнеры Docker:
```
docker-compose down -v 
```

### Развернуть проект на удаленном сервере:

Для запуска проекта на удаленном сервере необходимо:

- скопировать на сервер файл docker-compose.yaml, файл конфигурации nginx default.conf (команды выполнять находясь в директории с файлом):
```
scp docker-compose.yaml username@host:home/username/docker-compose.yaml

scp default.conf username@host:home/username/nginx/default.conf
```

- В разделе Secrets > Actions репозитория создать переменные окружения:
```
DOCKER_PASSWORD         # пароль от Docker Hub
DOCKER_USERNAME         # логин Docker Hub
HOST                    # публичный IP сервера
USER                    # имя пользователя на сервере
PASSPHRASE              # если ssh-ключ защищен паролем
SSH_KEY                 # приватный ssh-ключ
TELEGRAM_TO             # ID телеграм-аккаунта для сообщения
TELEGRAM_TOKEN          # токен бота

DB_ENGINE               # django.db.backends.postgresql
DB_NAME                 # postgres
POSTGRES_USER           # postgres
POSTGRES_PASSWORD       # свой пароль
DB_HOST                 # db
DB_PORT                 # 5432
```

### После каждого обновления репозитория будет происходить:

1. Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest
2. Сборка и доставка докер-образа для контейнера web на Docker Hub
3. Разворачивание проекта на удаленном сервере
4. Отправка сообщения в Telegram в случае успеха


### Примеры запросов к API:

- Получение списка всех произведений: доступно без токена.

*Запрос:*

```
GET http://localhost/api/v1/titles/
```

*Пример ответа:*

```
[
  {
    "count": 0,
    "next": "string",
    "previous": "string",
    "results": [
      {
        "id": 0,
        "name": "string",
        "year": 0,
        "rating": 0,
        "description": "string",
        "genre": [
          {
            "name": "string",
            "slug": "string"
          }
        ],
        "category": {
          "name": "string",
          "slug": "string"
        }
      }
    ]
  }
]
```

- Добавление нового отзыва к произведению: доступно аутентифицированным пользователям.

*Запрос:*

```
POST http://localhost/api/v1/titles/{title_id}/reviews/
```

*Содержимое запроса:*

```
{
  "text": "string",
  "score": 1
}
```

*Пример ответа:*

```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "score": 10,
  "pub_date": "2019-08-24T14:15:22Z"
}
```

- Добавление комментария к отзыву: доступно аутентифицированным пользователям.

*Запрос:*

```
POST http://localhost/api/v1/titles/{title_id}/reviews/{review_id}/comments/
```

*Содержимое запроса:*

```
{
  "text": "string"
}
```

*Пример ответа:*

```
{
  "id": 0,
  "text": "string",
  "author": "string",
  "pub_date": "2019-08-24T14:15:22Z"
}
```

- Удаление пользователя по имени пользователя (username): доступно Администратору.

*Запрос:*

```
DELETE http://localhost/api/v1/users/{username}/
```

### Авторы:

Михаил Солдаткин, Александр Жбаков, Дмитрий Коротеев (c) 2022
