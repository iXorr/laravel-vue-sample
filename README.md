# Инструкция по развёртыванию

![Docker](https://img.shields.io/badge/Docker-28.4.x-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-8.4-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-8.1-%23777BB4.svg?style=for-the-badge&logo=php&logoColor=white)
![Laravel](https://img.shields.io/badge/Laravel-10.x-%23FF2D20.svg?style=for-the-badge&logo=laravel&logoColor=white)
![Node.js](https://img.shields.io/badge/Node.js-22.x-%236DA55F.svg?style=for-the-badge&logo=node.js&logoColor=white)
![Vue.js](https://img.shields.io/badge/Vue.js-3.5.x-%2335495e.svg?style=for-the-badge&logo=vuedotjs&logoColor=%234FC08D)
![Nginx](https://img.shields.io/badge/Nginx-1.29.x-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)


## Описание
Это - стартовый набор Docker-сервисов для разработки и запуска REST API приложения. Структура включает в себя предопределенные конфигурации, и вся разработка происходит, по сути, в двух папках: ``backend`` и ``frontend``.

## Изменение версий
Если вам нужно использовать другую версию фреймворка (например, Laravel 10.x => Laravel 12.x), корректируйте версию и службы, которая связана с этим фреймворком (например, PHP 8.1 => 8.4), в соответствующем Dockerfile.

## Требования
- Linux или WSL2 (файловая система Windows сильно замедляет работу Docker).
- Docker (если вы используете Docker Desktop на Windows, проверьте совместимость с WSL).

## Шаги
1. Если у вас *есть* и *уже запущены* сервисы, которые занимают порты ``80``, ``3306``, ``5173``, конкретно для этого стартового набора вы можете переопределить порты обращения.
Скопируйте в корне проекта файл ``.env`` на основе ``.env.example`` и измените в нём переменные окружения на свободные, нужные вам порты.

   Не забудьте, что изменение веб-порта нужно учесть при разработке клиентской части приложения!
   ```bash
   cp .env.example .env
   ```
2. Загрузите зависимости для фронтенда.
   ```
   docker compose build frontend
   docker compose run --rm frontend npm install
   ```
3. Соберите и запустите сервисы. Или пересоберите: с флагом ``--build``.
   ```bash
   docker compose up -d
   ```
4. Когда все сервисы запущены, подготовьте бэкенд к работе. 

   Важно! Если вы изменили файлы в Laravel-проекте (например, сменили версию фреймворка), оставьте ``.env.example`` в изначальном виде!
   ```bash
   cp backend/.env.example backend/.env
   docker compose run --rm backend composer install
   docker compose run --rm backend php artisan key:generate
   docker compose run --rm backend php artisan migrate --seed
   ```

   Если возникает ошибка, связанная с правами на файлы и папки, выполните следующие команды.
   ```bash
   docker compose run --rm backend chown -R www-data:www-data storage bootstrap/cache
   docker compose run --rm backend chmod -R 775 storage bootstrap/cache
   ```
