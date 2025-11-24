# Инструкция по развёртыванию

![Docker](https://img.shields.io/badge/Docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-%23777BB4.svg?style=for-the-badge&logo=php&logoColor=white)
![Laravel](https://img.shields.io/badge/Laravel-%23FF2D20.svg?style=for-the-badge&logo=laravel&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-%236DA55F.svg?style=for-the-badge&logo=node.js&logoColor=white)
![Vue.js](https://img.shields.io/badge/Vue.js-%2335495e.svg?style=for-the-badge&logo=vuedotjs&logoColor=%234FC08D)
![Nginx](https://img.shields.io/badge/Nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)

## Описание
Это - стартовый набор Docker-сервисов для разработки и запуска REST API приложения. Структура включает в себя предопределенные конфигурации, и вся разработка происходит, по сути, в двух папках: ``backend`` и ``frontend``.

## Требования
- Linux или WSL2 (файловая система Windows сильно замедляет работу Docker).
- Docker (если вы используете Docker Desktop на Windows, проверьте совместимость с WSL).

## Версии служб
Корректируйте версии служб в соответствующем Dockerfile (например, при изменении версии PHP нужно перейти по ``services/php/Dockerfile``). Также, не забудьте, что версии фреймворков зависят от версии служб: так, для PHP 8.1 нужно установить Laravel 10.x.

## Конфигурация веб-сервера
Все запросы, начинающиеся с ``/api/``, перенаправляются Laravel-приложению - в папку ``backend/public``. Остальные же запросы перехватываются через прокси и перенаправляются фронтенду - поэтому, если он не запущен, Nginx будет выдавать ошибку.

## Переопределение портов обращения

Если у вас *есть* и *уже запущены* сервисы, которые занимают порты ``80``, ``3306``, ``5173``, конкретно для этого стартового набора вы можете переопределить порты обращения.
Скопируйте в корне проекта файл ``.env`` на основе ``.env.example`` и измените в нём переменные окружения на свободные, нужные вам порты.

Не забудьте, что изменение портов нужно учесть при разработке приложения (например, обращаться к серверу не по ``localhost``, а по ``localhost:180``)!
```bash
cp .env.example .env
```

## Использование репозитория
Поскольку это не полноценное приложение, а всего лишь вспомогательный стартовый пакет, стоит не клонировать его, а скачать и распаковать как обычный архив.
```bash
curl -L https://github.com/iXorr/laravel-vue-sample/archive/refs/heads/pure-sample.tar.gz \
  | tar -xz --strip-components=1
```

И уже потом инициализировать репозиторий именно *вашего* проекта.

## Начало работы
1. Эта версия стартового пакета без предустановленных пресетов. Поэтому, для начала просто забилдите проект.
   ```bash
   docker compose build
   ```
2. Скачайте в папки ``backend`` и ``frontend`` фреймворки нужных вам версий.
   ```bash
   docker compose run --rm backend composer create-project laravel/laravel:^10.x .
   docker compose run --rm frontend npm create vite . 
   ```
   При создании проектов должны появиться и зависимости, но проверьте их наличие (папки ``node_modules`` и ``vendor``).
3. Запустите сервисы.
   ```bash
   docker compose up -d
   ```
4. Когда все сервисы запущены, подготовьте бэкенд к работе. 

   Вам нужно настроить окружение базы данных для Laravel. В папке ``backend`` скопируйте файл ``.env`` из ``.env.example`` и внесите в него следующие изменения.
   ```
   DB_CONNECTION=mysql
   DB_HOST=db # docker container name
   DB_DATABASE=app_db # docker db conf
   DB_USERNAME=admin # docker db conf
   DB_PASSWORD=admin # docker db conf
   ```

   Далее, сгенерируйте ключ и произведи миграции, если последнее необходимо.
   ```bash
   docker compose run --rm backend php artisan key:generate
   docker compose run --rm backend php artisan migrate --seed
   ```

   Если возникает ошибка, связанная с правами на файлы и папки, выполните следующие команды.
   ```bash
   docker compose run --rm backend chown -R www-data:www-data storage bootstrap/cache
   docker compose run --rm backend chmod -R 775 storage bootstrap/cache
   ```
